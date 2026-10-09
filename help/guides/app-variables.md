---
title: 設定應用程式變數和密碼
description: 將環境特定的變數新增至您的Adobe LLM應用程式應用程式、在動作處理常式中閱讀這些變數、部署，以及對常見問題進行疑難排解。
source-git-commit: 141d7a263a6937299b3ff52bdcc7c16197e55632
workflow-type: tm+mt
source-wordcount: '1158'
ht-degree: 1%
---

# 設定應用程式變數和密碼 {#app-variables}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

使用變數和密碼來設定應用程式，而不需在其動作處理常式中硬式編碼值。 例如，使用Stage中的測試服務和Production中的即時服務，設定應用程式呼叫之產品目錄API的URL，而不變更處理常式程式碼。

變數保留不敏感的設定。 秘密適用於敏感值，例如API金鑰和存取權杖。

>[!IMPORTANT]
>
>目前僅支援&#x200B;**個變數**。 已規劃在未來版本提供秘密支援。 在此之前，**請勿在變數**&#x200B;中儲存&#x200B;**密碼、API金鑰、存取權杖或其他敏感資訊**：它們的值在設定表中可見且可複製。

**歷程：**&#x200B;新增變數或密碼→在您的處理常式中讀取，→部署→測試。

## 開始之前 {#before-you-begin}

您需要：

- **存取您應用程式的處理常式存放庫**，以便您可以視需要更新處理常式以讀取變數。
- **`@adobe/llm-apps-runtime`1.1.0或更新版本**。 自2026年8月起建立的應用程式已包含該應用程式。

若要檢查版本，請在您的處理常式存放庫中執行：

```bash
npm ls @adobe/llm-apps-runtime
```

如果版本早於1.1.0，請將它升級，然後認可並推送`package.json`和`package-lock.json`：

```bash
npm install @adobe/llm-apps-runtime@latest
```

## 管理變數 {#manage-variables}

每個環境&#x200B;**[!UICONTROL 階段]**&#x200B;或&#x200B;**[!UICONTROL 生產]**&#x200B;都有自己的變數，因此請在新增、更新或刪除變數之前先檢查&#x200B;**[!UICONTROL Workspace]**。 變更會在您下次將應用程式部署至該環境時生效。

### 新增變數 {#add-variable-to-app}

本指南使用名為`GREETING_PREFIX`且值為`Good day`的變數作為非敏感範例，覆寫處理常式的預設問候語`Hello`。 新增變數&#x200B;**不會**&#x200B;自動變更動作：處理常式&#x200B;**必須**&#x200B;讀取它。

#### 步驟1：在使用者介面中新增變數 {#add-a-variable}

1. 開啟您的應用程式，並在左側導覽中選取&#x200B;**[!UICONTROL 設定]**。
2. 開啟&#x200B;**[!UICONTROL 變數和密碼]**&#x200B;標籤。



3. 在&#x200B;**[!UICONTROL Workspace]**&#x200B;中，選取&#x200B;**[!UICONTROL 階段]**&#x200B;或&#x200B;**[!UICONTROL 生產]**。
4. 選取「**[!UICONTROL 新增]**」。

   ![變數和密碼 — 使用「新增」按鈕清空階段工作區](/help/assets/guide-app-variables/variables-empty.png)

5. 在對話方塊中，輸入：
   - **[!UICONTROL 名稱]**： `GREETING_PREFIX`。
   - **[!UICONTROL 型別]**：保留&#x200B;**[!UICONTROL 變數]**&#x200B;已選取。
   - **[!UICONTROL 值]**： `Good day`。

   ![新增變數或密碼 — GREETING_PREFIX設定為[好日]](/help/assets/guide-app-variables/add-variable-dialog.png)

6. 選取&#x200B;**[!UICONTROL 新增]**&#x200B;以儲存。

變數出現在具有其&#x200B;**[!UICONTROL 名稱]**、**[!UICONTROL 型別]**、**[!UICONTROL 值]**&#x200B;和&#x200B;**[!UICONTROL 上次更新日期]**&#x200B;的資料表中。 如果您需要複製值，請使用值旁的複製控制項。

![變數與密碼 — GREETING_PREFIX儲存在Stage工作區](/help/assets/guide-app-variables/variable-added.png)



>[!IMPORTANT]
>
>儲存會將變數新增至所選環境的設定。 它&#x200B;**不會**&#x200B;更新已部署的應用程式，直到您再次部署為止。

#### 步驟2：讀取處理常式中的變數 {#use-a-variable}

在您的處理常式存放庫中，開啟`actions/<action-name>/index.js`。 請確定處理常式接受第二個引數`extra`，並以`getVariable`讀取變數：

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ name = 'there' } = {}, extra) => {
  const prefix = getVariable(extra, 'GREETING_PREFIX') || 'Hello';

  return {
    content: [{ type: 'text', text: `${prefix}, ${name}!` }]
  };
};
```

使用值`Good day`，將`name`設定為`Ada`的呼叫會傳回`Good day, Ada!`，而非預設值`Hello, Ada!`。 如果您稍後將值變更為`Howdy`，問候語會在您重新部署後變更，而不需要其他處理常式編輯。

您的程式碼&#x200B;**中的名稱必須**&#x200B;與UI中的名稱完全相符。 如果未設定變數，`getVariable`會傳回`undefined`。 決定您的動作是否可以使用適當的預設值（如範例），或應該傳回明確的錯誤，因為它需要設定。

>[!NOTE]
>
>變數只能在應用程式的動作處理常式中使用；它們&#x200B;**不是**&#x200B;會自動提供給Widget。

當您的處理常式變更準備就緒時，請提交這些變更，並將其推送到您應用程式的處理常式存放庫。 下一個部署會使用最新推送的程式碼。 若要進一步瞭解如何編輯處理常式，請參閱[自訂產生的處理常式](/help/guides/customize-handler.md)。

#### 步驟3：部署和測試 {#deploy-and-verify}

1. [將您的應用程式](/help/guides/deploy-your-app.md)部署至您在&#x200B;**[!UICONTROL Workspace]**&#x200B;中選取的相同環境。
2. 從支援的LLM平台呼叫`name`設為`Ada`的動作。 請參閱[測試ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)或[測試Claude聯結器](/help/guides/test-in-claude.md)。
3. 確認回應為`Good day, Ada!`。 如此可確認處理常式會讀取您設定的變數，並覆寫其預設問候語。

若要設定其他環境，請在&#x200B;**[!UICONTROL Workspace]**&#x200B;中選取該環境，以適當的值重複進行設定，然後在此部署及驗證。


>[!NOTE]
>
>中繼和生產有&#x200B;**個獨立的組態**。 一個環境中的變更&#x200B;**不會**&#x200B;影響另一個環境。 如有必要，請在兩個環境中使用相同的名稱，並為每個環境選擇適當的值。

>[!TIP]
>
>若要在部署之前先在本機測試，請參閱[本機處理常式開發與測試](/help/reference/development.md)，並將變數傳遞至本機伺服器：
>
>`node server/local.js --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' --param GREETING_PREFIX=Howdy`

### 更新變數 {#update-or-delete}

1. 選取變數列中的編輯控制項。
2. 檢閱變數的&#x200B;**[!UICONTROL 目前值]**&#x200B;並輸入&#x200B;**[!UICONTROL 新值]**。

   ![更新GREETING_PREFIX — 將值從「好日」變更為「好時」](/help/assets/guide-app-variables/update-variable-dialog.png)

3. 選取「**[!UICONTROL 更新]**」。
4. 將應用程式再次部署至相同環境，並驗證變更後的行為。

更新只會變更值。 變數&#x200B;**無法**&#x200B;重新命名；若要使用其他名稱，請刪除現有變數並新增變數，然後更新您的處理常式以讀取新名稱。

### 刪除變數 {#delete-a-variable}

1. 檢查是否有任何動作仍需要變數。 如有必要，請先更新並推播處理常式&#x200B;**1&rbrace;。**
2. 選取變數列中的刪除控制項。 若要刪除數個專案，請選取其核取方塊，然後選擇&#x200B;**[!UICONTROL 刪除]**。
3. 檢閱確認對話方塊中的名稱，然後選取&#x200B;**[!UICONTROL 刪除]**。

   ![刪除GREETING_PREFIX — 確認永久刪除](/help/assets/guide-app-variables/delete-variable-dialog.png)

4. 將應用程式再次部署至相同環境。

>[!IMPORTANT]
>
>刪除&#x200B;**無法復原**，請確定您選取了正確的變數。 已部署的應用程式會保留其現有設定，直到下一次部署為止。 部署之後，處理常式&#x200B;**不再接收**&#x200B;已刪除的變數，因此需要它的動作可能會失敗。

## 規則和限制 {#good-to-know}

| 項目 | 規則 |
|------|------|
| 名稱 | 最多64個字元：大寫字母、數字和底線，開頭非數字。 **每個應用程式和環境都必須**&#x200B;是唯一的，而且必須符合處理常式。 |
| 保留名稱 | 以`LLMA_`和`MCP_SERVER_URL`開頭的名稱。 |
| 值 | **必要**，最多500個字元。 前置和結尾空格會被移除。 |
| 限制 | 每個應用程式和環境有50個變數。 |
| 可見度 | 變數值是可見且可複製的。 計畫的秘密支援會隱藏儲存的值。 |
| 變更 | 在下一個部署至選取的環境時生效。 |

## 疑難排解 {#verify-configuration}

| 您所看到的內容 | 該做什麼 |
|--------------|------------|
| **[!UICONTROL 新增]**&#x200B;已停用，且頁面顯示已達到&#x200B;**[!UICONTROL Workspace限制]** | 刪除您不再需要的變數。 |
| *僅使用大寫字母、數字和底線* | 重新命名，例如`API_BASE_URL`。 |
| *此名稱由平台保留* | 選擇不以`LLMA_`開頭且不是`MCP_SERVER_URL`的名稱。 |
| *已有此名稱的變數存在* | 請改為更新現有變數。 |
| *此變數已在其他地方修改* | 請重新整理頁面，然後再試一次。 |
| *無法載入變數* | 重新載入頁面。 如果持續發生，請檢查您是否有應用程式的存取權。 |
| 動作未使用新值 | 檢查您是否已在儲存變數&#x200B;**之後將**&#x200B;部署至您測試的相同環境、處理常式變更是否已推送，以及名稱是否與&#x200B;**完全相符**。 **[!UICONTROL 上次更新時間]**&#x200B;顯示值儲存時間，而非部署時間。 |
| `getVariable is not a function` | 您的應用程式使用1.1.0之前的執行階段。 請依照[中的說明進行升級。在您開始](#before-you-begin)之前，請先進行部署。 |
| 刪除變數後，動作會失敗 | 將變數新增回系統，或更新處理常式，讓它不再需要它，然後部署。 |

## 後續步驟 {#whats-next}

- [自訂產生的處理常式](/help/guides/customize-handler.md)
- [部署您的應用程式](/help/guides/deploy-your-app.md)
