---
title: 驗證參考
description: Adobe LLM應用程式中一般使用者驗證的欄位定義、權杖需求、探索端點、處理常式API及LLM平台行為。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1428'
ht-degree: 2%
---

# 驗證參考 {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

您可以在此頁面查詢驗證欄位與合約。 如需安裝指示，請參閱[使用您自己的身分識別提供者來驗證一般使用者](/help/guides/authentication.md)。

## 驗證設定 {#authentication-settings}

開啟&#x200B;**[!UICONTROL 設定]** > **[!UICONTROL 驗證]**。 每個環境都會儲存自己的設定。 使用&#x200B;**[!UICONTROL Workspace]**&#x200B;選取器來選取要編輯的環境；儲存設定不會影響其他環境。

| 欄位 | 必要 | 說明 |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | 驗證設定的環境： **[!UICONTROL 階段]**&#x200B;或&#x200B;**[!UICONTROL 生產]** |
| **[!UICONTROL 啟用驗證]** | — | 主開關。 關閉時，無論驗證模式為何，所有動作均為公開 |
| **[!UICONTROL 簽發者]** | 是 | 您的身分提供者的簽發者URL和預期的`iss`宣告。 必須是HTTPS。 也作為此應用程式的授權伺服器發佈。 每個應用程式一個身分提供者 |
| **[!UICONTROL 支援的領域]** | 否 | 此應用程式動作可能需要的一組完整範圍。 作為應用程式支援的範圍發佈至LLM平台 |
| **[!UICONTROL JWKS URI]** | 否 | 進階。 您的簽署金鑰組的HTTPS URL。 只有當它與授權伺服器的中繼資料通告內容不同時才需要 |

### 驗證規則

| 規則 | 效果 |
|------|--------|
| **[!UICONTROL 簽發者]**&#x200B;是空的，而&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;已開啟 | 已封鎖儲存 |
| **[!UICONTROL 簽發者]**&#x200B;或&#x200B;**[!UICONTROL JWKS URI]**&#x200B;不是HTTPS URL | 已封鎖儲存 |
| 動作需要&#x200B;**[!UICONTROL 支援的領域]**&#x200B;中缺少領域 | 儲存會遭到封鎖，直到您新增範圍或從動作中移除範圍為止 |
| 已從支援的&#x200B;**[!UICONTROL 領域移除領域]** | 它會從需要它的每個動作中立即移除，而不等待儲存 |
| **[!UICONTROL 支援的領域]**&#x200B;是空的 | 無法授與任何範圍，因此會移除動作中已有的任何範圍。 在此情況下不會顯示警告 |
| `offline_access`已列在&#x200B;**[!UICONTROL 支援的領域]**&#x200B;或動作中 | 在部署應用程式時移除，無論大小寫或周圍的空白區域為何，因此設定頁面可顯示已部署應用程式沒有的範圍。 `offline_access`會向您的授權伺服器要求重新整理Token，而不是授與此應用程式的存取權，因此這不是此應用程式通告的範圍。 您不需要列出它 — LLM平台會直接從您的授權伺服器要求它 |

**[!UICONTROL 簽發者]**&#x200B;的結尾斜線已標準化。 `iss`比較容許差異，所以來自一律包含尾隨斜線的提供者的Token仍會驗證。

## 驗證模式 {#auth-modes}

在&#x200B;**[!UICONTROL 每個動作組態]**&#x200B;下設定每個動作。

| 模式 | 需要權杖 | 處理常式接收身分 | 在平台上廣告為 |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL 無]** | 否 | 只有當呼叫者提供有效的權杖時 | `noauth` |
| **[!UICONTROL 必要]** | 是，包含每個列出的範圍 | 一律 | `oauth2` |
| **[!UICONTROL 選擇性]** | 否 | 當有效的權杖出現時 | `noauth` 和 `oauth2` |

**[!UICONTROL 必要]**&#x200B;動作的處理常式，永遠不會在沒有有效、範圍正確的權杖的情況下執行。 **[!UICONTROL Optional]**&#x200B;動作的處理常式一律會執行，而且可能會要求使用`extra.challengeAuth()`登入本身。

因此，**[!UICONTROL 必要]**&#x200B;是唯一拒絕未驗證來電者的模式。 應用程式只會在每個動作為&#x200B;**[!UICONTROL 必要]**&#x200B;時才拒絕所有匿名呼叫。 如果任何動作是&#x200B;**[!UICONTROL 無]**&#x200B;或&#x200B;**[!UICONTROL 選擇性]**，應用程式會使用混合式驗證，因為至少有一個動作接受匿名呼叫。

只有在&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;開啟時，驗證模式才會生效。 變更會在應用程式下一次部署時生效。

變更驗證交換器也會重寫每個動作的模式：

| 切換變更 | 對每次動作模式的影響 |
|---------------|----------------------------|
| 關閉至開啟 | 每個&#x200B;**[!UICONTROL 無]**&#x200B;動作會變成&#x200B;**[!UICONTROL 必要]**。 動作已&#x200B;**[!UICONTROL 必要]**&#x200B;或&#x200B;**[!UICONTROL 選用]**，請保留其模式 |
| 開啟至關閉 | 系統會為該環境清除每個動作的模式和範圍。 如果您再次開啟開關，則不會還原設定 |

切換&#x200B;**[!UICONTROL Workspace]**&#x200B;絕不重寫模式。 選擇器會載入其他環境的已儲存設定，而不會變更它。

啟用&#x200B;**[!UICONTROL 驗證]**，且每個動作都設為&#x200B;**[!UICONTROL 無]**，應用程式不需要驗證，但仍會發佈其授權伺服器以供探索。 若要讓應用程式完全公開，請關閉開關。

應用程式可對個別動作使用不同的驗證模式。 請參閱[LLM平台行為](/help/reference/authentication-reference.md#platform-behavior)，瞭解每個平台如何套用這些行為。

## 權杖需求 {#token-requirements}

您的身分提供者必須核發符合下列所有要求的存取權杖。 執行階段將未通過任何檢查的權杖視為不存在：呼叫者未驗證，且&#x200B;**[!UICONTROL 必要]**&#x200B;動作會要求呼叫者登入。

| 需求 | 詳細資料 |
|-------------|--------|
| 格式 | 已簽署JWT。 不支援不透明權杖 |
| 簽署演演算法 | `RS256`、`RS384`、`RS512`、`ES256`、`ES384`、`ES512`、`PS256`、`PS384`或`PS512`。 已拒絕HMAC演演算法（例如`HS256`） |
| `iss` | 必須符合&#x200B;**[!UICONTROL 簽發者]** |
| `aud` | 必須包含應用程式的資源識別碼 — 該環境的MCP伺服器URL |
| `exp` | 必須為未來 |
| `scope` 或 `scp` | 執行階段讀取以空格分隔的`scope`字串，或退回到`scp`中的字串陣列。 針對每個動作的需求，使用其中一個圖形來檢查範圍 |
| `sub` | 處理常式透過`getAuthenticatedUser`讀取的使用者識別碼 |
| 傳輸 | `Authorization: Bearer <token>`請求標頭 |

您的提供者包含的任何其他一般宣告（例如`tenant`或`email`）都會傳遞給您的處理常式。 巢狀物件會被捨棄，長字串值會被截斷。

## 身分提供者探索 {#discovery}

您的應用程式會發佈自己的[RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728)受保護資源中繼資料，讓LLM平台可以找到您的授權伺服器。 您不需要建立、託管或設定受保護的資源中繼資料。

您的身分提供者必須提供下列探索資訊：

| 需求 | 詳細資料 |
|-------------|--------|
| 授權伺服器中繼資料 | 您的授權伺服器必須發佈LLM使用者端的探索中繼資料。 除非設定&#x200B;**[!UICONTROL JWKS URI]**，否則此應用程式的執行階段會特別尋找[RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414) OAuth授權伺服器中繼資料來找到`jwks_uri`；它不會回溯至OIDC探索路徑 |
| 具有路徑的簽發者 | 知名區段會位於路徑之前，而非路徑之後。 位於`https://auth.example.com/oauth2/default`的簽發者在`https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default`提供其中繼資料 |
| 直接簽署金鑰URL | 當金鑰託管於其他位置或提供者僅公開OIDC探索時，請設定&#x200B;**[!UICONTROL JWKS URI]**。 這會略過執行階段金鑰的探索，而非LLM使用者端所需的探索 |

## 處理常式驗證API {#handler-auth-api}

套件`@adobe/llm-apps-runtime`匯出下列協助程式。 每個協助程式都接受`extra`，這是您的處理常式接收的第二個引數。

| 協助程式 | 傳回 |
|--------|---------|
| `getAuthenticatedUser(extra)` | 登入使用者的`sub`宣告，或呼叫未驗證時的`undefined` |
| `hasScope(extra, scope)` | 呼叫者的權杖攜帶`scope`時的`true` |

`extra.authInfo`中有已驗證的Token資訊，對於未驗證的呼叫而言是`undefined`。

| 屬性 | 說明 |
|----------|-------------|
| `authInfo.token` | 原始持有人權杖。 請勿將其記錄或傳回使用者端 |
| `authInfo.clientId` | `client_id`或`azp`宣告，或`unknown` |
| `authInfo.scopes` | 已授與範圍的陣列 |
| `authInfo.expiresAt` | 權杖到期，如`exp`宣告 |
| `authInfo.resource` | 權杖驗證所針對的應用程式資源識別碼 |
| `authInfo.extra` | `sub`加上您的身分提供者包含的任何其他一般宣告 |

`extra.challengeAuth(options)`僅適用於&#x200B;**[!UICONTROL 選擇性]**&#x200B;動作。 從您的處理常式傳回結果，要求使用者登入而非傳回內容。

協助程式傳回[!DNL ChatGPT]的工具結果挑戰。 [!DNL Claude]需要傳輸層級HTTP挑戰。 執行階段提供會挑戰&#x200B;**[!UICONTROL 必要]**&#x200B;動作，但不透過選擇性處理常式協助程式進行。

| 選項 | 說明 |
|--------|-------------|
| `error` | [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750)持有者錯誤碼： `invalid_token`、`insufficient_scope`或`invalid_request`。 預設為`insufficient_scope` |
| `errorDescription` | 向使用者顯示的訊息。 預設為一般登入提示 |
| `scope` | 要要求的以空格分隔的範圍。 省略此專案，讓平台回覆至應用程式支援的範圍 |

>[!IMPORTANT]
>
>一律明確設定`error`。 對於沒有有效工作階段的呼叫者使用`invalid_token`，而只對語彙基元有效但缺少必要範圍的呼叫者使用`insufficient_scope`。 執行階段會將值傳給LLM平台，由平台選擇登入提示的措辭。 傳送準確描述條件的程式碼，而非具有您偏好提示的程式碼。

## LLM平台行為 {#platform-behavior}

兩種平台都支援相同應用程式中的公開和受保護動作，但其登入觸發程式不同。

| 行為 | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| 混合驗證 | 公用動作以匿名方式運作；**[!UICONTROL 必要]**&#x200B;動作要求登入 | 透過&#x200B;**[!UICONTROL 需要時登入]**，公開動作會以匿名方式運作，且受保護的通話會開始登入 |
| 具有每個動作&#x200B;**[!UICONTROL 必要的應用程式]** | 設定OAuth驗證 | 使用前需要驗證 |
| 聯結器設定 | 針對公用應用程式使用&#x200B;**[!UICONTROL 無驗證]**，當每個動作為&#x200B;**[!UICONTROL 必要]**&#x200B;時，使用&#x200B;**[!UICONTROL OAuth]**，否則使用&#x200B;**[!UICONTROL 混合]**，其中會提供這些選項 | 檢閱偵測到的設定。 選擇&#x200B;**[!UICONTROL 需要時登入]**&#x200B;供混合式應用程式使用，或&#x200B;**[!UICONTROL 立即登入]**&#x200B;供預先登入使用 |
| 缺少身分或必要的範圍 | 執行階段傳回平台的驗證挑戰 | 執行階段會針對遺失/無效的身分傳回HTTP 401，或針對遺失必要範圍的有效權杖傳回範圍不足的HTTP 403 |
| 選擇性處理常式的`extra.challengeAuth()` | 可以開始重新驗證 | 成功的HTTP工具結果回應不會開始登入。 針對可攜式登入閘道使用&#x200B;**[!UICONTROL 必要]**&#x200B;動作 |

檢視[Claude延遲驗證](https://claude.com/docs/connectors/building/lazy-authentication)和[ChatGPT驗證](https://developers.openai.com/plugins/build/auth)目前的使用者端行為。 聯結器UI和原則可能會因帳戶和組織而異。


## 相關

- [使用您自己的身分提供者驗證一般使用者](/help/guides/authentication.md)
- [動作和Widget欄位](/help/reference/reference-docs.md)
- [疑難排解](/help/reference/troubleshooting.md#authentication)
