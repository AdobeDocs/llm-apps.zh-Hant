---
title: 使用您自己的身分提供者驗證一般使用者
description: 開啟您Adobe LLM應用程式的一般使用者驗證，讓支援的LLM平台在呼叫受保護的動作之前，先將使用者登入您的身分提供者。
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '2149'
ht-degree: 0%
---

# 使用您自己的身分提供者來驗證一般使用者 {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

根據預設，您應用程式上的每個動作都是公開的：任何擁有您MCP伺服器URL的LLM平台都可以呼叫它，而且您的處理常式無法識別一般使用者。

當動作需要知道哪些一般使用者在要求時（例如，傳回其訂單、權益或帳戶詳細資訊），請開啟驗證。 LLM平台使用&#x200B;**您的**&#x200B;身分提供者(IdP)登入使用者，每次呼叫都會傳送產生的存取權杖，而且您的處理常式會接收已驗證的身分識別。

**歷程：**&#x200B;複製資源識別碼→設定您的身分提供者→開啟驗證，→為→部署的每個動作設定驗證模式→讀取處理常式中的身分識別，→測試受保護的應用程式。

這是進階分支，不是首次執行歷程的一部分。 完成[自動建立您的第一個應用程式](/help/guides/create-app.md)並[先部署您的應用程式](/help/guides/deploy-your-app.md)。

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

已針對每個環境&#x200B;**設定**&#x200B;驗證。 **[!UICONTROL 階段]**&#x200B;和&#x200B;**[!UICONTROL 生產]**&#x200B;保留獨立的設定，所以您可以在&#x200B;**[!UICONTROL 生產]**&#x200B;上啟用它之前，針對開發IdP租使用者驗證設定。

## 開始之前

- 發行以非對稱演演算法簽署之&#x200B;**JWT**&#x200B;存取權杖的OAuth 2.1或OpenID Connect身分提供者。 不支援不透明權杖和HMAC簽署的權杖。 檢視[權杖需求](/help/reference/authentication-reference.md#token-requirements)。
- 管理員可存取該身分提供者，因此您可以註冊API和使用者端。
- 您的應用程式至少部署至您正在設定的環境一次。 已部署的MCP伺服器URL是您的權杖必須設定範圍的數值。

## 複製資源識別碼

應用程式的&#x200B;**資源識別碼**&#x200B;是其MCP伺服器URL。 您的身分提供者針對此應用程式發行的每個存取Token都必須將該確切URL命名為對象 — 該繫結可防止針對您的應用程式重播為其他服務所指定的Token。

1. 開啟「應用程式詳細資料」頁面。
2. 捲動至&#x200B;**[!UICONTROL 測試應用程式]**。
3. 在您設定的環境中，選取&#x200B;**[!UICONTROL 複製URL]**。

![應用程式詳細資料 — 複製暫存MCP伺服器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

保留此值：您需要在下一步的身分提供者中使用此值。 貼上複製的URL，而非重新輸入。 對象檢查是完全相符的字串，包括任何路徑元件，因此單一字元差異會導致每個權杖驗證失敗。

>[!NOTE]
>
>每個環境都有自己的MCP伺服器URL，因此也有自己的受眾。 分別設定&#x200B;**[!UICONTROL 階段]**&#x200B;和&#x200B;**[!UICONTROL 生產]**。

## 設定您的身分提供者

確切的步驟依提供者而異，但每個提供者需要相同的四個專案。

1. **將您的應用程式註冊為API （資源）。** 將其識別碼（提供者將代號的`aud`宣告中的值）設定為您複製的MCP伺服器URL。 提供者以不同方式標示此欄位，通常是&#x200B;*識別碼*&#x200B;或&#x200B;*對象*。 請勿使用一般值（例如`api`）；識別碼必須對此應用程式是唯一的，否則可針對此應用程式重播為其他服務所核發的權杖。
2. **定義您要啟動動作的範圍**，例如`orders:read`或`profile:read`。 針對每一個有意義的許可權使用一個範圍，因此動作只會要求需要的內容。
3. **支援PKCE。** LLM平台會在每個授權要求上傳送帶有`code_challenge_method=S256`的`code_challenge`，因此您的授權伺服器必須支援S256 PKCE並在其中繼資料中通告`"code_challenge_methods_supported": ["S256"]`。
4. **允許LLM平台註冊為使用者端。** 支援的LLM平台會針對您的授權伺服器建立自己的OAuth使用者端，因此如果您的提供者提供，請啟用動態使用者端註冊。 否則，請手動建立公用使用者端並提供其使用者端ID （以及密碼），前提是您的提供者需要在平台上的聯結器設定期間進行機密使用者端驗證。 在平台檔案中註冊重新導向URI；針對[!DNL Claude]的託管介面`https://claude.ai/api/mcp/auth_callback`。 某些平台會針對使用者建立的每個聯結器發出不同的重新導向URI （其中[!DNL ChatGPT]個），因此請從聯結器設定畫面中讀取值，並在第一次登入前註冊。 未登入的重新導向URI會導致授權伺服器完全拒絕授權要求。

>[!IMPORTANT]
>
>您的身分提供者的簽發者、JWKS、授權和權杖端點都必須可透過公開HTTPS存取。 LLM平台和已部署的應用程式都會直接從提供者擷取中繼資料，因此VPN或IP允許清單背後的身分提供者無法完成登入。 供應商面前的防火牆或Web應用程式防火牆是常見的原因，即使您的應用程式本身可以連線，它也會中斷流程。

## 開啟驗證

1. 在左側導覽中，選取&#x200B;**[!UICONTROL 設定]**，然後開啟&#x200B;**[!UICONTROL 驗證]**&#x200B;標籤。
2. 在&#x200B;**[!UICONTROL Workspace]**&#x200B;中，選擇&#x200B;**[!UICONTROL 階段]**&#x200B;或&#x200B;**[!UICONTROL 生產]**。
3. 開啟&#x200B;**[!UICONTROL 啟用驗證]**。
4. 在&#x200B;**[!UICONTROL 核心設定]**&#x200B;下，輸入：
   - **[!UICONTROL 簽發者]** — 您的身分提供者的簽發者URL，也是它放入每個權杖`iss`宣告中的值。 此為必要專案，必須是HTTPS，也會發佈為應用程式的授權伺服器，因此LLM平台可以探索將使用者傳送至何處。 每個應用程式僅支援一個身分提供者。
   - **[!UICONTROL 支援的領域]** — 允許此應用程式的動作所需的每個領域。 映象您在身分提供者中定義的範圍。
5. **[!UICONTROL 進階設定]**&#x200B;是選用的。 只有當您的簽署金鑰不在授權伺服器的中繼資料通告它們時，才設定&#x200B;**[!UICONTROL JWKS URI]**；否則應用程式會自動探索它們。
6. 選取「**[!UICONTROL 儲存]**」。

![驗證 — 啟用驗證並完成核心設定](/help/assets/guide-authentication/auth-core-settings.png)

如需每個欄位接受的內容，請參閱[驗證設定](/help/reference/authentication-reference.md#authentication-settings)。

## 選擇每個動作的驗證模式

當您開啟&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;時，目前設定為&#x200B;**[!UICONTROL 無]**&#x200B;的每個動作都會變更為&#x200B;**[!UICONTROL 必要]**。 在&#x200B;**[!UICONTROL 每個動作組態]**&#x200B;下，檢閱該指派並設定每個動作所需的模式：

| 模式 | 行為 |
|------|----------|
| **[!UICONTROL 無]** | 公開。 沒有權杖便可以呼叫動作。 |
| **[!UICONTROL 必要]** | 閘道。 只有有效的Token能容納您為其列出的每個範圍，才能呼叫動作。 未驗證的來電者將被要求登入。 |
| **[!UICONTROL 選擇性]** | 可匿名呼叫，但動作也通告其支援登入。 您的處理常式會根據呼叫決定是要提供一般結果還是要求使用者登入個人化結果。 |

![驗證 — 為每個動作設定驗證模式和範圍](/help/assets/guide-authentication/auth-per-action.png)

動作已設定為&#x200B;**[!UICONTROL 必要]**&#x200B;或&#x200B;**[!UICONTROL 選用]**，請保留其現有模式。

若要執行&#x200B;**[!UICONTROL 必要]**&#x200B;或&#x200B;**[!UICONTROL 選用]**&#x200B;動作，請新增所需的&#x200B;**[!UICONTROL 領域]**。 每個範圍必須已出現在以上支援的&#x200B;**[!UICONTROL 範圍]**&#x200B;中；否則應用程式會要求不通告給LLM平台的許可權。 儲存會被封鎖，直到不符問題解決為止。

**[!UICONTROL 支援的領域]**&#x200B;是此清單的授權單位。 如果您從其中移除範圍，當您進行變更時，會從每個需要它的動作中移除該範圍 — 因此，請先在那裡新增範圍，然後將其指派給動作。

**[!UICONTROL 所有動作都需要驗證]**&#x200B;將每個動作設定為&#x200B;**[!UICONTROL 必要]**。 清除它會傳回每個動作至&#x200B;**[!UICONTROL 無]**。

完成時選取&#x200B;**[!UICONTROL 儲存]**。 驗證模式和範圍變更會與應用程式層級設定一併儲存。

>[!IMPORTANT]
>
>開啟&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;後退會捨棄所選環境的這個個別動作設定 — 每個動作的模式和範圍都已清除，不會記憶中。 從全部 — **[!UICONTROL 必要]**&#x200B;重新開啟。

>[!NOTE]
>
>將每個動作設定為&#x200B;**[!UICONTROL 無]**&#x200B;並不會停用驗證。 在該狀態下，不會拒絕任何呼叫，但應用程式仍會將您的授權伺服器通告給LLM平台，因此使用者端可以為使用者提供登入，而不會授予額外的存取權。 若要讓應用程式完全公開，請關閉&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;並部署。

支援在單一應用程式中混合模式（部分為公開動作，其他為閘道動作），且[!DNL ChatGPT]會個別套用每個動作的模式：只有閘道動作會提示使用者登入。

>[!IMPORTANT]
>
>[!DNL Claude]為例外狀況。 它會針對每個聯結器套用驗證，而非針對每個動作，因此如果應用程式上的任何動作設為&#x200B;**[!UICONTROL 必要]**&#x200B;或&#x200B;**[!UICONTROL 選擇性]**，[!DNL Claude]會要求使用者先登入再使用聯結器，包括設為&#x200B;**[!UICONTROL 無]**&#x200B;的動作。 若要讓[!DNL Claude]個使用者能夠公開某個動作，請將其託管於個別應用程式上。

## 部署變更

驗證變更會在此應用程式的下一次部署中生效。 **再次將應用程式**&#x200B;部署至您設定的環境。 請參閱[部署您的應用程式](/help/guides/deploy-your-app.md)。

您的MCP伺服器URL不會變更，因此您已經建立的任何外掛程式或聯結器會繼續運作。 現在此功能已設為閘道，因此下次使用者使用此功能時，系統會要求使用者登入。

## 讀取處理常式中的身分識別

經過驗證的身分會到達您的處理常式，做為第二個引數。 無論動作的驗證模式為何，只要呼叫者傳送有效的權杖，就會出現權杖，因此&#x200B;**[!UICONTROL 選用]**&#x200B;動作可以在權杖存在時個人化其結果，而在權杖不存在時仍會傳回結果。

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

您不需要自行驗證權杖。 對於&#x200B;**[!UICONTROL 必要]**&#x200B;動作，執行階段會封鎖每個缺少有效權杖的呼叫，該權杖包含您列出的範圍，因此處理常式只會針對授權呼叫者執行。 當您想要在許可權上分支而不是依賴閘道時，請使用`hasScope`，例如，在&#x200B;**[!UICONTROL 選擇性]**&#x200B;動作中。

**[!UICONTROL 選擇性]**&#x200B;動作可要求使用者傳回`extra.challengeAuth()`以登入對話中間。 這僅適用於&#x200B;**[!UICONTROL 選擇性]**&#x200B;動作：

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

決定是否從明確的輸入引數呈報（如`signIn`在此做的那樣），而不是透過檢查使用者的措辭呈報。

設定`error`以符合您報告的條件。 當呼叫者沒有有效的工作階段且需要登入時，請使用`invalid_token`，如上述範例所示；當呼叫者已登入但權杖缺少動作所需的範圍時，請使用`insufficient_scope`。 LLM平台會選擇使用者所看到提示的措辭，以及此值在不同平台之間的差異程度，因此請傳送描述該條件的正確程式碼。

只有在您需要的身分確實遺失時，才能提出質詢，就像這裡的`!extra.authInfo`檢查一樣。 如果處理常式要求無條件挑戰，則無法透過登入來加以滿足，因此系統會要求使用者在每次呼叫時再次進行驗證。

>[!NOTE]
>
>在[!DNL ChatGPT]上，登入以這種方式提出，要求使用者重新連線聯結器，而不是授與額外的許可權。 使用者會在[!DNL Claude]上登入，然後才會執行任何動作，因此動作永遠不需要引發。

保留身分識別伺服器端。 只將Widget需要的傳遞到`structuredContent`，並且永遠不要將存取權杖放在該處 — 請參閱[自訂產生的處理常式](/help/guides/customize-handler.md)。

如需完整合約，請參閱[處理常式驗證API](/help/reference/authentication-reference.md#handler-auth-api)。

## 測試受保護的應用程式

您現有的外掛程式或聯結器會在部署後擷取變更。 若要從頭開始設定：

### [!DNL ChatGPT]

在&#x200B;**[!UICONTROL 新外掛程式]**&#x200B;對話方塊中，設定&#x200B;**[!UICONTROL 驗證]**&#x200B;以符合您設定應用程式動作的方式：

| 應用程式的動作 | 選取 |
|--------------------|--------|
| 全部設定為&#x200B;**[!UICONTROL 無]** | **[!UICONTROL 沒有驗證]** |
| 全部設定為&#x200B;**[!UICONTROL 必要]** | **[!UICONTROL OAuth]** |
| 任何其他組合 | **[!UICONTROL 混合]** |

![ChatGPT — 選取外掛程式的驗證模式](/help/assets/guide-authentication/chatgpt-authentication-mode.png)

**[!UICONTROL Optional]**&#x200B;動作一律會接受匿名呼叫，因此包含此動作的應用程式永遠不會完整接通 — 即使每個動作都設為&#x200B;**[!UICONTROL Optional]**，請選擇&#x200B;**[!UICONTROL Mixed]**。 只有&#x200B;**[!UICONTROL 必要]**&#x200B;拒絕未驗證的來電者。

請參閱[測試ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)，瞭解對話方塊的其餘部分。

### [!DNL Claude]

新增自訂聯結器，然後選取&#x200B;**[!UICONTROL 連線]**&#x200B;並完成您的身分提供者顯示的登入。 沒有要進行的驗證選擇 — [!DNL Claude]會在任何動作被閘道時閘道整個聯結器。 請參閱[測試克勞德聯結器](/help/guides/test-in-claude.md)。

### 驗證

- 平台會將您重新導向至您自己的身分提供者的登入頁面。
- 受保護的動作會在登入後傳回使用者特有的資料。
- 當您登出時，受保護動作會提示您登入。
- 在[!DNL ChatGPT]上，設定為&#x200B;**[!UICONTROL 無]**&#x200B;的動作仍會在未登入的情況下回應。 在[!DNL Claude]上，整個聯結器已閘道。

如果登入未開始，或權杖被拒絕，請參閱[疑難排解](/help/reference/troubleshooting.md#authentication)。

## 安全性指引

- 授予每個動作所需的最窄範圍。 請勿在每一個動作中重複使用一個廣泛的範圍。
- 在身分提供者和LLM平台的聯結器設定中保留使用者端秘密。 切勿將其放在動作中繼資料、處理常式程式碼、Widget JavaScript或原始檔控制中。
- 將Token宣告視為來自外部系統的輸入。 請先驗證您從`authInfo.extra`中讀取的任何內容，然後再將其用於查詢中。
- 授權並驗證。 有效的Token可證明使用者身份，而非他們可能會看到特定記錄 — 在傳回資料之前，請檢查處理常式中的擁有權。
- 請勿記錄權杖、完整宣告集或使用者識別碼。
- 傳回安全錯誤。 請勿將上游身分提供者回應或棧疊追蹤顯示給使用者。
- 在&#x200B;**[!UICONTROL 生產]**&#x200B;上啟用驗證之前，針對非生產身分提供者租使用者設定並驗證&#x200B;**[!UICONTROL 階段]**。

## 後續步驟

- [驗證參考](/help/reference/authentication-reference.md) — 欄位、權杖需求和平台行為。
- [自訂產生的處理常式](/help/guides/customize-handler.md) — 從處理常式呼叫受保護的上游API。
- [部署您的應用程式](/help/guides/deploy-your-app.md)。
