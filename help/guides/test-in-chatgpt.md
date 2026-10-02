---
title: 將您的LLM應用程式測試為ChatGPT外掛程式
description: 從您的Adobe LLM應用程式MCP伺服器URL建立ChatGPT外掛程式，並在交談中測試。
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '378'
ht-degree: 1%
---

# 將您的LLM App測試為[!DNL ChatGPT]外掛程式 {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

部署後，您的LLM應用程式會公開MCP伺服器URL。 將此URL新增至[!DNL ChatGPT]作為外掛程式，然後測試產生的動作和Widget。

這是建置、自訂或擴充應用程式後的最終驗證步驟。

## 計畫需求

Pro、Plus、Business、Enterprise和Education帳戶可使用開發人員模式。 Workspace管理員可以限制存取權。

## 啟用開發人員模式

在[!DNL ChatGPT]：

1. 開啟&#x200B;**[!UICONTROL 設定] → [!UICONTROL 安全性與登入]**。
2. 開啟&#x200B;**[!UICONTROL 開發人員模式]**。

只有啟用開發人員模式後，「外掛程式」頁面上的加號按鈕才會建立MCP支援的外掛程式。 請參閱[ChatGPT開發人員模式](https://developers.openai.com/api/docs/guides/developer-mode)。

## 複製MCP伺服器URL

在[!DNL LLM Apps]：

1. 開啟「應用程式詳細資料」頁面。
2. 尋找&#x200B;**[!UICONTROL 測試應用程式]**。
3. 在&#x200B;**[!UICONTROL 中繼環境]**&#x200B;下，選取&#x200B;**[!UICONTROL 複製URL]**。

## 建立外掛程式

1. 開啟[chatgpt.com/plugins](https://chatgpt.com/plugins)。
2. 在&#x200B;**[!UICONTROL 外掛程式]**&#x200B;索引標籤上，選取搜尋欄位旁的&#x200B;**+**。

   ![ChatGPT — 外掛程式頁面](/help/assets/guide-onboarding-agent/chatgpt-plugins-page.png)

3. 在&#x200B;**[!UICONTROL 新外掛程式]**&#x200B;中，輸入：
   - **[!UICONTROL 名稱]** — 外掛程式名稱。
   - **[!UICONTROL 描述]** — 選擇性。
   - **[!UICONTROL 連線]** — 選取&#x200B;**[!UICONTROL 伺服器URL]**&#x200B;並貼上MCP伺服器URL。
   - **[!UICONTROL 驗證]** — 選取&#x200B;**[!UICONTROL 無驗證]**。

   >[!NOTE]
   >
   >**[!UICONTROL 應用程式上的每一個動作都是公開的，則不會套用任何驗證]**。 如果您已開啟一般使用者驗證，請在每個動作設定為&#x200B;**[!UICONTROL 必要]**&#x200B;時，選取&#x200B;**[!UICONTROL OAuth]**，若為任何其他組合，則選取&#x200B;**[!UICONTROL 混合]** — 請參閱[使用您自己的身分識別提供者來驗證一般使用者](/help/guides/authentication.md)。

4. 選取&#x200B;**[!UICONTROL 我瞭解並想要繼續]**。
5. 選取「**[!UICONTROL 建立]**」。

   ![ChatGPT — 使用MCP伺服器URL](/help/assets/guide-onboarding-agent/chatgpt-new-plugin.png)建立外掛程式

6. 在確認對話方塊中，選取&#x200B;**[!UICONTROL 連線]**。

   ![ChatGPT — 連線新的外掛程式](/help/assets/guide-onboarding-agent/chatgpt-plugin-connect.png)

## 測試外掛程式

1. 開始新的聊天。
2. 從[加號]功能表，選擇&#x200B;**[!UICONTROL 開發人員模式]**&#x200B;並選取外掛程式。
3. 提出符合其中一個產生之動作的問題。 例如： *給我看點咖啡。*

![ChatGPT — 產生的LLM應用程式外掛程式回應](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

確認：

- [!DNL ChatGPT]會叫用預期的動作。
- Widget會顯示預期的範例資料。
- 文字回應符合Widget。
- Widget控制項如預期般運作。

## 後續步驟

- [自訂產生的Widget](/help/guides/widgets.md)。
- [從頭開始建立動作](/help/guides/create-action.md)。
