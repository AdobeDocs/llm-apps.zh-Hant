---
title: 在ChatGPT中測試
description: 瞭解如何將您部署的Adobe LLM應用程式新增到ChatGPT，並在真實交談中測試。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '804'
ht-degree: 2%

---


# 在[!DNL ChatGPT]中測試

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

>[!NOTE]
>
>本指南以[!DNL ChatGPT]為例。 一般步驟（註冊MCP伺服器URL和在交談中進行測試）同樣適用於其他LLM平台，不過設定流程和UI會有所不同。

使用[!DNL Adobe LLM Apps]成功部署後，您的應用程式會在[!DNL Adobe I/O Runtime]上執行並公開MCP伺服器URL。 本指南會向您說明如何將其新增到[!DNL ChatGPT]並在真實交談中測試。

## 計畫需求

將自訂開發人員應用程式新增到[!DNL ChatGPT]受OpenAI的訂閱層級控制 — 這不是[!DNL LLM Apps]限制，而是OpenAI目前管理自訂MCP應用程式存取的方式。

| [!DNL ChatGPT]個計畫 | 自訂MCP應用程式 |
|--------------|-----------------|
| 免費 | 無法使用 |
| 前往 | 無法使用 |
| 加號 | 無法使用 |
| Pro | 可使用 |
| 商務 | 可使用 |
| 企業/教育 | 可使用 |

>[!NOTE]
>
>如果您是免費的、Go或Plus計畫，則&#x200B;**無法將您部署的應用程式**&#x200B;新增到[!DNL ChatGPT]。 升級為&#x200B;**Pro**，或要求貴組織的管理員在&#x200B;**企業**&#x200B;或&#x200B;**企業**&#x200B;工作區上啟用它。

## 啟用開發人員模式

若要新增自訂MCP應用程式，您必須在[!DNL ChatGPT]帳戶中啟用&#x200B;**開發人員模式**。 關注
以下步驟可驗證及啟用此功能。

### 開啟設定

按一下左下角的設定檔頭像，然後按一下&#x200B;**[!UICONTROL 設定]**。

![ChatGPT — 設定功能表](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

### 導覽至應用程式

在[設定]對話方塊中，選取左側邊欄中的&#x200B;**[!UICONTROL 應用程式]**。 按一下底部的&#x200B;**[!UICONTROL 進階設定]**。

![ChatGPT — 應用程式設定](/help/assets/guide-test-chatgpt/chatgpt-apps-settings.png)

### 開啟開發人員模式

請確定&#x200B;**[!UICONTROL 開發人員模式]**&#x200B;切換已開啟（藍色）。 這可讓您註冊自訂、未驗證的MCP伺服器URL。

>[!NOTE]
>
>開發人員模式標示為&#x200B;*提升的風險*，因為它允許尚未由OpenAI檢閱的應用程式。 [!DNL ChatGPT]會自動停用使用開發人員模式App之交談的記憶體。

![ChatGPT — 開發人員模式已啟用](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

## 將您的應用程式新增至[!DNL ChatGPT]

### 複製MCP伺服器URL

移至[!DNL LLM Apps]中的&#x200B;**應用程式詳細資料**&#x200B;頁面，並找到&#x200B;**[!UICONTROL 測試應用程式]**&#x200B;區段。 複製&#x200B;**暫存**&#x200B;或&#x200B;**生產** URL — 它看起來像：

```
https://<namespace>.adobeioruntime.net/api/v1/web/llm-apps/mcp
```

### 開啟應用程式頁面

在[!DNL ChatGPT]中，移至[!UICONTROL 應用程式&#x200B;]**→的**[!UICONTROL &#x200B;設定]。

![ChatGPT — 應用程式頁面](/help/assets/guide-test-chatgpt/chatgpt-apps-page.png)

### 建立新的應用程式

按一下「進階設定」列中的&#x200B;**[!UICONTROL 建立應用程式]**。

![ChatGPT — 建立應用程式對話方塊](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

填入下列內容：

| 欄位 | 值 |
|-------|-------|
| **圖示** | 選擇性 — 上傳128x128 PNG （最大10 KB） |
| **名稱** | 應用程式的顯示名稱（例如，*我的品牌應用程式*） |
| **說明** | 應用程式功能的簡短說明 |
| **MCP伺服器URL** | 貼上來自[!DNL LLM Apps]的URL |
| **[!UICONTROL 驗證]** | 選取&#x200B;*無驗證* |

核取&#x200B;**我瞭解並想要繼續**核取方塊 — 這表示已認可MCP伺服器
尚未由OpenAI檢閱 — 然後按一下[建立]。****

### 確認應用程式已啟用

建立後，您的應用程式會顯示在具有&#x200B;**[!UICONTROL DEV]**&#x200B;徽章的&#x200B;**[!UICONTROL 已啟用應用程式]**&#x200B;下，確認其為作用中。

>[!NOTE]
>
>您的應用程式也會顯示在&#x200B;**草稿**&#x200B;下 — 這些是您以開發人員模式建立的私人應用程式，僅對您的帳戶可見。

您的應用程式現在可以在[!DNL ChatGPT]個交談中使用。

![ChatGPT — 已啟用應用程式](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

## 在交談中測試

應用程式啟用後，請在[!DNL ChatGPT]中開始新的交談。 在詢問問題之前，請使用以下兩種方法之一附加您的應用程式。

### 選項1 — 從選單中選取

按一下聊天輸入中的&#x200B;**+**&#x200B;按鈕，然後按&#x200B;**更多**&#x200B;以展開可用工具的完整清單。 從清單中選取您的應用程式，將其附加至目前的交談。

![ChatGPT — 從功能表選取應用程式](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

### 選項2 — 使用@mention

在聊天輸入中輸入&#x200B;**@**，並從下拉式清單中選取您的應用程式。 這會附加內嵌應用程式，而您可以繼續在同一則訊息中輸入問題。

>[!NOTE]
>
>在相同應用程式上再次使用&#x200B;**@mention**&#x200B;將會取消選取它，並將其從交談中移除。

![ChatGPT —@mention啟應用程式](/help/assets/guide-test-chatgpt/chatgpt-mention-app.png)

選取後，應用程式會內嵌附加，而您可以在相同的訊息中鍵入您的問題：

![ChatGPT — 透過@mention](/help/assets/guide-test-chatgpt/chatgpt-mention.png)附加的應用程式

### 檢視結果

附加應用程式後，請輸入與您其中一個已設定動作對齊的問題 — 例如，*「顯示您的產品」。* [!DNL ChatGPT]會比對相關動作、擷取輸入引數、在[!DNL Adobe I/O Runtime]上呼叫您的處理常式，並呈現結果：

![ChatGPT — 動作結果](/help/assets/guide-test-chatgpt/chatgpt-response.png)

回應包括：

- **EDS Widget** — 包含影像、評分和動作按鈕的豐富UI元件。
- **文字回應** — 在Widget下方，[!DNL ChatGPT]會使用您的處理常式傳回的`content`
以制定結果的自然語言摘要。
- **狀態指標** — 您在[建立動作]對話方塊中設定的&#x200B;*叫用的狀態文字*。

## 後續步驟

- **新增更多動作** — 在UI中定義其他動作、編寫其處理常式，然後重新部署。
- **部署至生產環境** — 如果您在預備環境中測試，請部署至生產環境以提供即時體驗。
- **與您的團隊共用** — 在[應用程式詳細資料]頁面上使用&#x200B;**複製URL**，與團隊成員共用MCP伺服器URL。

