---
title: 使用您自己的身分提供者驗證一般使用者
description: 開啟您Adobe LLM應用程式的一般使用者驗證，讓支援的LLM平台在呼叫受保護的動作之前，先將使用者登入您的身分提供者。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '2136'
ht-degree: 0%
---

# 使用您自己的身分提供者驗證一般使用者 {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

依預設，應用程式中的每個動作都是公開的。 任何具有MCP伺服器URL的LLM平台都可以呼叫這些動作，而且您的處理常式無法識別一般使用者。

當動作需要知道哪些一般使用者在要求時（例如，傳回其訂單、權益或帳戶詳細資訊），請開啟驗證。 LLM平台使用&#x200B;**您的**&#x200B;身分提供者(IdP)登入使用者，每次呼叫都會傳送產生的存取權杖，而且您的處理常式會接收已驗證的身分識別。

**在本指南中：**&#x200B;瞭解如何連線您的身分提供者，並選擇需要登入的動作。 在處理常式中使用已驗證的身分、部署應用程式，並測試登入和受保護的動作。

驗證設定是進階工作。 先完成[基礎結構設定](/help/guides/create-app.md)、[建立動作](/help/guides/creating-actions.md)和[部署您的應用程式](/help/guides/deploy-your-app.md)。

## 運作方式

您自備身分識別提供者。 您部署的應用程式只是OAuth 2.1 **資源伺服器** — 它驗證您的授權伺服器所發行的權杖。 它不會核發權杖，且[!DNL Adobe]不會儲存您的使用者端ID或使用者端密碼。

```
┌── Your identity provider ───────────────────────────────────────────────┐
│  Authorization server — you own it                                      │
│  Issues access tokens, holds the user directory, defines the scopes     │
└─────────────────────────────────────────────────────────────────────────┘
        ▲  2  user signs in, platform gets an access token
        │                                    │
        │  1  platform discovers your        │  3  every tools/call carries
        │     authorization server from      │     Authorization: Bearer <token>
        │     your app's metadata            ▼
┌── LLM platform (ChatGPT, Claude, …) ────────────────────────────────────┐
└─────────────────────────────────────────────────────────────────────────┘
                                             │
                                             ▼
┌── Your LLM App on Adobe I/O Runtime ────────────────────────────────────┐
│  Resource server — verifies the token's signature, issuer, audience,    │
│  and expiry, then enforces the auth mode you set for each action        │
│                                                                         │
│  Your handler reads the verified identity from its second argument      │
└─────────────────────────────────────────────────────────────────────────┘
```

為每個環境&#x200B;**設定驗證**。 **[!UICONTROL 階段]**&#x200B;和&#x200B;**[!UICONTROL 生產]**&#x200B;保留獨立的設定。 在&#x200B;**[!UICONTROL 生產]**&#x200B;上啟用驗證之前，請先針對開發IdP租使用者驗證設定。

## 開始之前

- 發行以非對稱演演算法簽署之&#x200B;**JWT**&#x200B;存取權杖的OAuth 2.1或OpenID Connect身分提供者。 不支援不透明權杖和HMAC簽署的權杖。 檢視[權杖需求](/help/reference/authentication-reference.md#token-requirements)。
- 管理員存取該身分提供者以註冊API和使用者端。
- 您的應用程式至少部署至您正在設定的環境一次。 已部署的MCP伺服器URL是您的權杖必須設定範圍的數值。

## 複製資源識別碼

應用程式的&#x200B;**資源識別碼**&#x200B;是其MCP伺服器URL。 您的身分提供者針對應用程式發行的每個存取Token都必須將該確切URL命名為對象。 此對象繫結可防止使用為其他服務核發的權杖。

1. 開啟「應用程式詳細資料」頁面。
2. 捲動至&#x200B;**[!UICONTROL 測試應用程式]**。
3. 在您設定的環境中，選取&#x200B;**[!UICONTROL 複製URL]**。

![應用程式詳細資料 — 複製暫存MCP伺服器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

在下個步驟中，保留識別提供者設定的複製MCP伺服器URL。 貼上URL而非重新輸入。 對象檢查需要完全相符的字串，包括路徑。 單一字元差異會導致每個Token驗證失敗。

>[!NOTE]
>
>每個環境都有自己的MCP伺服器URL，因此也有自己的受眾。 分別設定&#x200B;**[!UICONTROL 階段]**&#x200B;和&#x200B;**[!UICONTROL 生產]**。

## 設定您的身分提供者

步驟因提供者而異。 設定下列四個專案。

1. **將您的應用程式註冊為API （資源）。** 將它的識別碼（提供者放入權杖的`aud`宣告中的值）設定為複製的MCP伺服器URL。 提供者通常會標示此欄位&#x200B;*識別碼*&#x200B;或&#x200B;*對象*。 使用應用程式專屬的識別碼，而非通用值（例如`api`），以防止使用為其他服務所核發的權杖。
2. **定義保護您動作的範圍**，例如`orders:read`或`profile:read`。 針對每個有意義的許可權使用一個範圍，讓每個動作僅要求所需的存取權。
3. **支援PKCE。** LLM平台會在每個授權要求上傳送帶有`code_challenge_method=S256`的`code_challenge`，因此您的授權伺服器必須支援S256 PKCE並在其中繼資料中通告`"code_challenge_methods_supported": ["S256"]`。
4. **選擇LLM平台識別其OAuth使用者端的方式。** 如果您的提供者支援平台的需求、動態使用者端註冊(DCR)或預先註冊的OAuth使用者端，請使用使用者端ID中繼資料檔案(CIMD)。 只有在您選擇的方法需要使用者端ID和密碼時，才提供它們。 註冊由平台記錄或顯示的確切重新導向URI；[!DNL Claude]的託管介面為`https://claude.ai/api/mcp/auth_callback`。 [!DNL ChatGPT]可能會使用穩定或聯結器特定的回呼（視提供者已公告的功能而定），因此請複製其顯示的URI而非猜測。 請參閱目前的[ChatGPT驗證檔案](https://developers.openai.com/plugins/build/auth)和[克勞德驗證檔案](https://claude.com/docs/connectors/building/authentication)。

>[!IMPORTANT]
>
>您的身分提供者的簽發者、JWKS、授權和權杖端點都必須可透過公開HTTPS存取。 LLM平台和您已部署的應用程式都會直接從您的提供者擷取中繼資料。 封鎖這些請求的VPN、IP允許清單、防火牆或網頁應用程式防火牆會防止登入，即使應用程式本身仍可連線。

## 開啟驗證

1. 在左側導覽中，選取&#x200B;**[!UICONTROL 設定]**，然後開啟&#x200B;**[!UICONTROL 驗證]**&#x200B;標籤。
2. 在&#x200B;**[!UICONTROL Workspace]**&#x200B;中，選擇&#x200B;**[!UICONTROL 階段]**&#x200B;或&#x200B;**[!UICONTROL 生產]**。
3. 開啟&#x200B;**[!UICONTROL 啟用驗證]**。
4. 在&#x200B;**[!UICONTROL 核心設定]**&#x200B;下，輸入：
   - **[!UICONTROL Issuer]** — 您的身分提供者的HTTPS簽發者URL，也是它放入每個權杖`iss`宣告中的值。 平台需要此URL，並將簽發者發佈為應用程式的授權伺服器，以進行LLM使用者端探索。 每個應用程式支援一個身分提供者。
   - **[!UICONTROL 支援的領域]** — 允許此應用程式的動作所需的每個領域。 映象您在身分提供者中定義的範圍。
5. **[!UICONTROL 進階設定]**&#x200B;是選用的。 只有當您的簽署金鑰不在授權伺服器的中繼資料通告它們時，才設定&#x200B;**[!UICONTROL JWKS URI]**；否則應用程式會自動探索它們。
6. 選取「**[!UICONTROL 儲存]**」。

![驗證 — 啟用驗證並完成核心設定](/help/assets/guide-authentication/auth-core-settings.png)

如需欄位需求，請參閱[驗證設定](/help/reference/authentication-reference.md#authentication-settings)。

## 選擇每個動作的驗證模式

當您開啟&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;時，目前設定為&#x200B;**[!UICONTROL 無]**&#x200B;的每個動作都會變更為&#x200B;**[!UICONTROL 必要]**。 在&#x200B;**[!UICONTROL 每個動作組態]**&#x200B;下，檢閱該指派並設定每個動作所需的模式：

| 模式 | 行為 |
|------|----------|
| **[!UICONTROL 無]** | 公開。 來電者不需要權杖。 |
| **[!UICONTROL 必要]** | 受保護。 來電者需要一個有效權杖來攜帶每個列出的範圍。 執行階段會要求未驗證的呼叫者登入。 |
| **[!UICONTROL 選擇性]** | 可匿名呼叫，但動作也通告其支援登入。 您的處理常式會根據呼叫決定是要提供一般結果還是要求使用者登入個人化結果。 |

![驗證 — 為每個動作設定驗證模式和範圍](/help/assets/guide-authentication/auth-per-action.png)

動作已設定為&#x200B;**[!UICONTROL 必要]**&#x200B;或&#x200B;**[!UICONTROL 選用]**，請保留其現有模式。

若要執行&#x200B;**[!UICONTROL 必要]**&#x200B;或&#x200B;**[!UICONTROL 選用]**&#x200B;動作，請新增所需的&#x200B;**[!UICONTROL 領域]**。 每個範圍都必須出現在&#x200B;**[!UICONTROL 支援的範圍]**&#x200B;中，所以應用程式會向LLM平台通告每個必要的許可權。 平台會封鎖儲存，直到您解決任何範圍不符為止。

**[!UICONTROL 支援的領域]**&#x200B;控制可用的領域。 從該清單中移除範圍會立即從需要它的每個動作中移除範圍。 將每個範圍新增至清單，然後再將範圍指派給動作。

選取&#x200B;**[!UICONTROL 需要驗證所有動作]**&#x200B;變更&#x200B;**[!UICONTROL 無]**&#x200B;動作為&#x200B;**[!UICONTROL 必要]**，但保留已設定為&#x200B;**[!UICONTROL 選擇性]**&#x200B;的動作。 若要讓每個動作都需要登入，請個別將任何剩餘的&#x200B;**[!UICONTROL 選擇性]**&#x200B;資料列變更為&#x200B;**[!UICONTROL 必要]**。 清除核取方塊會傳回每個動作至&#x200B;**[!UICONTROL 無]**。

完成時選取&#x200B;**[!UICONTROL 儲存]**。 驗證模式和範圍變更會與應用程式層級設定一併儲存。

>[!IMPORTANT]
>
>關閉&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;會清除所選環境的每個動作的驗證模式和範圍。 平台不會保留這些設定。 再次開啟驗證會將所有動作設定為&#x200B;**[!UICONTROL 必要]**。

>[!NOTE]
>
>將每個動作設定為&#x200B;**[!UICONTROL 無]**&#x200B;並不會停用驗證。 不需要以該狀態登入任何動作，但應用程式仍會向LLM平台公告您的授權伺服器。 使用者端可提供不授予額外存取權的登入功能。 若要讓應用程式完全公開，請關閉&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;並部署。

[!DNL ChatGPT]支援一個應用程式中的混合驗證模式，並個別套用每個動作的模式。 公用動作不會提示登入；受保護動作會視需要提示。

[!DNL Claude]也透過&#x200B;**延遲驗證**&#x200B;支援混合式應用程式。 在聯結器設定期間選取&#x200B;**[!UICONTROL 需要時登入]**，公開動作仍可匿名使用，且平台在呼叫&#x200B;**[!UICONTROL 必要]**&#x200B;動作時要求登入。 其動作全部為&#x200B;**[!UICONTROL 必要]**&#x200B;的應用程式需要先登入，才能使用。 請參閱[LLM平台行為](/help/reference/authentication-reference.md#platform-behavior)，瞭解必要動作與要求登入的選擇性處理常式之間的區別。

>[!NOTE]
>
>目前的執行階段支援此混合驗證行為，並針對1.1.5版進行驗證。 較舊部署的應用程式可能需要執行階段更新和重新部署。 有些UI建置仍會顯示舊版警告，指出Claude需要對整個聯結器進行驗證。 驗證已部署的執行階段，並使用克勞德目前的&#x200B;**[!UICONTROL 視需要登入]**&#x200B;選項，而不是依賴警告。

## 部署變更

驗證變更會在此應用程式的下一次部署中生效。 **再次將應用程式**&#x200B;部署至您設定的環境。 請參閱[部署您的應用程式](/help/guides/deploy-your-app.md)。

您的MCP伺服器URL不會變更。 重新整理連線的中繼資料，並確認其驗證設定與應用程式的新設定相符。 當平台的驗證方法變更時，可能會要求您重新連線或重新建立連線。

## 讀取處理常式中的身分識別

無論動作的驗證模式為何，只要呼叫者傳送有效權杖，驗證身分就會到達您的處理常式，做為第二個引數。 **[!UICONTROL 選擇性]**&#x200B;動作可以使用該身分來個人化結果（若有可用的話），否則會傳回匿名結果。

使用`getAuthenticatedUser`讀取登入的使用者：

```javascript
const { getAuthenticatedUser } = require('@adobe/llm-apps-runtime');

module.exports = async ({ orderId }, extra) => {
  const userId = getAuthenticatedUser(extra);

  if (!userId) {
    return { content: [{ type: 'text', text: 'Sign in to see your orders.' }] };
  }

  const order = await fetchOrderForUser(userId, orderId);

  return {
    content: [{ type: 'text', text: `Order ${order.id} is ${order.status}.` }],
    structuredContent: order
  };
};
```

執行階段會為您驗證權杖。 若為&#x200B;**[!UICONTROL 必要]**&#x200B;動作，它會封鎖所有在列出的範圍中缺少有效權杖的呼叫。 處理常式只會針對授權的呼叫者執行。 使用`hasScope`根據許可權選擇處理常式行為 — 例如，在&#x200B;**[!UICONTROL 選擇性]**&#x200B;動作中。

若要在交談期間要求登入，請從處理常式傳回`extra.challengeAuth()`。 協助程式僅適用於&#x200B;**[!UICONTROL 選擇性]**&#x200B;動作：

```javascript
module.exports = async ({ signIn }, extra) => {
  if (signIn && !extra.authInfo) {
    return extra.challengeAuth({
      error: 'invalid_token',
      errorDescription: 'Sign in to see member pricing.'
    });
  }

  return {
    content: [{
      type: 'text',
      text: extra.authInfo ? await memberDeals() : await publicDeals()
    }]
  };
};
```

使用明確的輸入引數（例如此範例中的`signIn`）來決定何時要求驗證。 請勿從使用者的措辭推斷該決定。

設定`error`以符合報告的條件。 當呼叫者沒有有效的工作階段且需要登入時，請使用`invalid_token`，如上述範例所示。 當呼叫者已登入但權杖缺少必要的範圍時，請使用`insufficient_scope`。 每個LLM平台都會選擇提示字詞以及如何回應錯誤碼。 傳送準確描述狀況的程式碼。

只有在缺少必要的身分時才會要求驗證，如同此範例中的`!extra.authInfo`檢查一樣。 登入不會解決無條件挑戰：處理常式會在每次呼叫時再次要求驗證。

>[!NOTE]
>
>`extra.challengeAuth()`傳回[!DNL ChatGPT]的工具結果挑戰。 在處理常式執行之前，[!DNL Claude]需要HTTP驗證挑戰。 對於需要跨支援平台登入的作業，請使用&#x200B;**[!UICONTROL 必要]**&#x200B;動作。 視需要保留公開體驗的個別&#x200B;**[!UICONTROL 無]**&#x200B;動作。

保留身分識別伺服器端。 只將Widget所需的資料傳遞至`structuredContent`。 絕對不要在`structuredContent`中包含存取權杖。 請參閱[自訂產生的處理常式](/help/guides/customize-handler.md)。

如需完整合約，請參閱[處理常式驗證API](/help/reference/authentication-reference.md#handler-auth-api)。

## 測試受保護的應用程式

您現有的外掛程式或聯結器會在部署後使用更新的設定。 若要建立新連線，請遵循平台的指示：

### [!DNL ChatGPT]

在&#x200B;**[!UICONTROL 新外掛程式]**&#x200B;對話方塊中，設定&#x200B;**[!UICONTROL 驗證]**&#x200B;以符合您設定應用程式動作的方式：

| 應用程式的動作 | 選取 |
|--------------------|--------|
| 全部設定為&#x200B;**[!UICONTROL 無]** | **[!UICONTROL 沒有驗證]** |
| 全部設定為&#x200B;**[!UICONTROL 必要]** | **[!UICONTROL OAuth]** |
| 任何其他組合 | **[!UICONTROL 混合]** |

**[!UICONTROL Optional]**&#x200B;動作一律會接受匿名呼叫，因此包含此動作的應用程式永遠不會完整接通 — 即使每個動作都設為&#x200B;**[!UICONTROL Optional]**，請選擇&#x200B;**[!UICONTROL Mixed]**。 只有&#x200B;**[!UICONTROL 必要]**&#x200B;拒絕未驗證的來電者。

請參閱[測試ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)，瞭解對話方塊的其餘部分。

### [!DNL Claude]

新增自訂聯結器並檢閱其驗證設定。 對於公用/受保護的混合式應用程式，請選擇&#x200B;**[!UICONTROL 需要時登入]** （可用時）。 若要在使用聯結器之前要求登入，請選取&#x200B;**[!UICONTROL 立即登入]**。 對話方塊會因帳戶或組織而異；請參閱[測試Claude聯結器](/help/guides/test-in-claude.md)。

### 驗證

- 平台會將您重新導向至您自己的身分提供者的登入頁面。
- 受保護的動作會在登入後傳回使用者特有的資料。
- 當您登出時，受保護動作會提示您登入。
- 在混合式應用程式中，設定為&#x200B;**[!UICONTROL 無]**&#x200B;的動作仍會在未登入的情況下回應。 在[!DNL Claude]中，使用&#x200B;**[!UICONTROL 在需要時登入]**&#x200B;來驗證公開動作。
- 登入後，請重試受保護的動作，並確認執行階段會強制實施預期的許可權。

如果登入未開始，或權杖被拒絕，請參閱[疑難排解](/help/reference/troubleshooting.md#authentication)。

## 安全性指引

- 授予每個動作所需的最窄範圍。 請勿在每一個動作中重複使用一個廣泛的範圍。
- 在身分提供者和LLM平台的聯結器設定中保留使用者端秘密。 切勿將其放在動作中繼資料、處理常式程式碼、Widget JavaScript或原始檔控制中。
- 將Token宣告視為來自外部系統的輸入。 在查詢中使用這些值之前，請先驗證來自`authInfo.extra`的值。
- 授權並驗證。 有效的Token可證明使用者的身分，而非讀取特定記錄的許可權。 在傳回資料之前，請檢查處理常式中的擁有權。
- 請勿記錄權杖、完整宣告集或使用者識別碼。
- 傳回安全錯誤。 請勿將上游身分提供者回應或棧疊追蹤顯示給使用者。
- 在&#x200B;**[!UICONTROL 生產]**&#x200B;上啟用驗證之前，針對非生產身分提供者租使用者設定並驗證&#x200B;**[!UICONTROL 階段]**。

## 後續步驟

- [驗證參考](/help/reference/authentication-reference.md) — 欄位、權杖需求和平台行為。
- [自訂產生的處理常式](/help/guides/customize-handler.md) — 從處理常式呼叫受保護的上游API。
- [部署您的應用程式](/help/guides/deploy-your-app.md)。
