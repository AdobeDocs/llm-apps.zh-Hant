---
title: 驗證參考
description: Adobe LLM應用程式中一般使用者驗證的欄位定義、權杖需求、探索端點、處理常式API及LLM平台行為。
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '1260'
ht-degree: 2%
---

# 驗證參考 {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

您可以在此頁面查詢驗證欄位與合約。 如需安裝歷程，請參閱[使用您自己的身分識別提供者來驗證一般使用者](/help/guides/authentication.md)。

## 驗證設定 {#authentication-settings}

在&#x200B;**[!UICONTROL 設定]** > **[!UICONTROL 驗證]**&#x200B;下找到。 每個欄位都依環境儲存 — **[!UICONTROL Workspace]**&#x200B;選擇器會選取您正在編輯的欄位，而且儲存時不會影響其他欄位。

| 欄位 | 必要 | 說明 |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | 這些設定套用至哪個環境： **[!UICONTROL 階段]**&#x200B;或&#x200B;**[!UICONTROL 生產]** |
| **[!UICONTROL 啟用驗證]** | — | 主開關。 關閉時，無論驗證模式為何，所有動作均為公開 |
| **[!UICONTROL 簽發者]** | 是 | 您的身分提供者的簽發者URL，以及預期的`iss`宣告。 必須是HTTPS。 也作為此應用程式的授權伺服器發佈。 每個應用程式一個身分提供者 |
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

**[!UICONTROL 簽發者]**&#x200B;上的結尾斜線會正規化，而`iss`比較容許差異 — 一律發出結尾斜線的提供者仍會驗證。

## 驗證模式 {#auth-modes}

在&#x200B;**[!UICONTROL 每個動作組態]**&#x200B;下設定每個動作。

| 模式 | 需要權杖 | 處理常式接收身分 | 在平台上廣告為 |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL 無]** | 否 | 只有當呼叫者提供有效的權杖時 | `noauth` |
| **[!UICONTROL 必要]** | 是，包含每個列出的範圍 | 一律 | `oauth2` |
| **[!UICONTROL 選擇性]** | 否 | 當有效的權杖出現時 | `noauth` 和 `oauth2` |

**[!UICONTROL 必要]**&#x200B;動作的處理常式，永遠不會在沒有有效、範圍正確的權杖的情況下執行。 **[!UICONTROL Optional]**&#x200B;動作的處理常式一律執行，而且可能要求使用`extra.challengeAuth()`登入本身。

因此，**[!UICONTROL 必要]**&#x200B;是唯一拒絕未驗證來電者的模式。 只有當應用程式的每一個動作為&#x200B;**[!UICONTROL 必要]**&#x200B;時，應用程式才會完全關閉；單一&#x200B;**[!UICONTROL 無]**&#x200B;或&#x200B;**[!UICONTROL 選用]**&#x200B;動作會混合應用程式，因為匿名呼叫至少仍會成功執行一個動作。

只有在&#x200B;**[!UICONTROL 啟用驗證]**&#x200B;開啟時，驗證模式才會生效。 變更會在應用程式下一次部署時生效。

切換開關時會重寫每個動作的模式：

| 切換變更 | 對每次動作模式的影響 |
|---------------|----------------------------|
| 關閉至開啟 | 每個&#x200B;**[!UICONTROL 無]**&#x200B;動作會變成&#x200B;**[!UICONTROL 必要]**。 動作已&#x200B;**[!UICONTROL 必要]**&#x200B;或&#x200B;**[!UICONTROL 選用]**，請保留其模式 |
| 開啟至關閉 | 系統會為該環境清除每個動作的模式和範圍。 如果您再次開啟開關，則不會還原設定 |

切換&#x200B;**[!UICONTROL Workspace]**&#x200B;絕不重寫模式 — 它會載入其他環境的儲存組態。

**[!UICONTROL 啟用驗證]** （每個動作皆設為&#x200B;**[!UICONTROL 無]**）為有效但無效的組合：從未拒絕任何呼叫，但應用程式仍發佈其授權伺服器以供探索。 關閉開關，讓應用程式完全公開。

各模式可在一個應用程式中自由混合。 請參閱[LLM平台行為](/help/reference/authentication-reference.md#platform-behavior)，瞭解每個平台如何套用這些行為。

## 權杖需求 {#token-requirements}

您的身分提供者必須核發符合下列所有條件的存取權杖。 未通過任何檢查的Token會被視為不存在 — 呼叫者未驗證，且&#x200B;**[!UICONTROL 必要]**&#x200B;動作會要求他們登入。

| 需求 | 詳細資料 |
|-------------|--------|
| 格式 | 已簽署JWT。 不支援不透明權杖 |
| 簽署演演算法 | `RS256`、`RS384`、`RS512`、`ES256`、`ES384`、`ES512`、`PS256`、`PS384`或`PS512`。 已拒絕HMAC演演算法（例如`HS256`） |
| `iss` | 必須符合&#x200B;**[!UICONTROL 簽發者]** |
| `aud` | 必須包含應用程式的資源識別碼 — 該環境的MCP伺服器URL |
| `exp` | 必須為未來 |
| `scope` 或 `scp` | 以空格分隔的字串，或字串陣列。 提供根據每個動作的需求檢查的範圍 |
| `sub` | 處理常式透過`getAuthenticatedUser`讀取的使用者識別碼 |
| 傳輸 | `Authorization: Bearer <token>`請求標頭 |

您的提供者包含的任何其他一般宣告（例如`tenant`或`email`）都會傳遞給您的處理常式。 巢狀物件會被捨棄，長字串值會被截斷。

## 身分提供者探索 {#discovery}

您的應用程式會發佈自己的[RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728)受保護資源中繼資料，讓LLM平台可以找到您的授權伺服器。 您沒有為其建立、託管或設定任何專案。

您必須自行提供探索：

| 需求 | 詳細資料 |
|-------------|--------|
| 授權伺服器中繼資料 | 您的簽發者必須在其`/.well-known/`路徑提供自己的[RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414)中繼資料，或[!DNL OpenID Connect]探索。 您的應用程式會讀取該檔案，以找出您的簽署金鑰 |
| 具有路徑的簽發者 | 知名區段會位於路徑之前，而非路徑之後。 位於`https://auth.example.com/oauth2/default`的簽發者在`https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default`提供其中繼資料 |
| 在其他位置託管的金鑰 | 當您的簽署金鑰不在中繼資料通告的位置，請設定&#x200B;**[!UICONTROL JWKS URI]** |

## 處理常式驗證API {#handler-auth-api}

已從`@adobe/llm-apps-runtime`匯出。 每個協助程式都採用`extra`，這是您的處理常式所接收的第二個引數。

| 協助程式 | 傳回 |
|--------|---------|
| `getAuthenticatedUser(extra)` | 登入使用者的`sub`宣告，或呼叫未驗證時的`undefined` |
| `hasScope(extra, scope)` | 呼叫者的權杖攜帶`scope`時的`true` |

原始的已驗證權杖資訊位於`extra.authInfo`上，對於未驗證的呼叫而言是`undefined`。

| 屬性 | 說明 |
|----------|-------------|
| `authInfo.token` | 原始持有人權杖。 請勿將其記錄或傳回使用者端 |
| `authInfo.clientId` | `client_id`或`azp`宣告，或`unknown` |
| `authInfo.scopes` | 已授與範圍的陣列 |
| `authInfo.expiresAt` | 權杖到期，如`exp`宣告 |
| `authInfo.resource` | 權杖驗證所針對的應用程式資源識別碼 |
| `authInfo.extra` | `sub`加上您的身分提供者包含的任何其他一般宣告 |

`extra.challengeAuth(options)`僅適用於&#x200B;**[!UICONTROL 選擇性]**&#x200B;動作。 從您的處理常式傳回結果，要求使用者登入而非傳回內容。

| 選項 | 說明 |
|--------|-------------|
| `error` | [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750)持有者錯誤碼： `invalid_token`、`insufficient_scope`或`invalid_request`。 預設為`insufficient_scope` |
| `errorDescription` | 向使用者顯示的訊息。 預設為一般登入提示 |
| `scope` | 要要求的以空格分隔的範圍。 省略此專案，讓平台回覆至應用程式支援的範圍 |

>[!IMPORTANT]
>
>一律明確設定`error`。 對於沒有有效工作階段的呼叫者使用`invalid_token`，而只對語彙基元有效但缺少必要範圍的呼叫者使用`insufficient_scope`。 值會傳遞至LLM平台，由其自行決定如何輸入顯示使用者的提示。 傳送準確描述條件的程式碼，而非您想要其提示的程式碼。

## LLM平台行為 {#platform-behavior}

對驗證個別動作的支援因平台而異。 兩者設定相同方式，差異在於使用者體驗。

| 行為 | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| 粒度 | 每個動作 | 每個聯結器 |
| 混合式驗證，應用程式並未完全受限 | 支援。 只有&#x200B;**[!UICONTROL 必要]**&#x200B;動作會提示登入 | 不支援。 整個聯結器會提示登入，包括未啟用的動作 |
| 聯結器設定 | 當每個動作為&#x200B;**[!UICONTROL 無]**、**[!UICONTROL OAuth]**&#x200B;且每個動作為&#x200B;**[!UICONTROL 必要]**&#x200B;時，將&#x200B;**[!UICONTROL 驗證]**&#x200B;設為&#x200B;**[!UICONTROL 無驗證]**，否則設為&#x200B;**[!UICONTROL 混合]** | 沒有可選擇的驗證；登入從&#x200B;**[!UICONTROL 連線]**&#x200B;開始 |
| 重新驗證 | 呼叫閘道動作時對話中提示 | 提示輸入聯結器 |


## 相關

- [使用您自己的身分提供者驗證一般使用者](/help/guides/authentication.md)
- [動作和Widget欄位](/help/reference/reference-docs.md)
- [疑難排解](/help/reference/troubleshooting.md#authentication)
