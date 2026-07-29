---
title: 部署您的應用程式
description: 瞭解如何使用LLM應用程式UI將您的Adobe LLM應用程式部署到測試環境和生產環境。
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '322'
ht-degree: 0%

---


# 部署您的應用程式 {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

撰寫處理常式程式碼並將其推送至連結的存放庫後，您就可以從[!DNL LLM Apps] UI部署應用程式。

這是每個歷程的共用步驟。 部署後，繼續[測試ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)或[測試Claude聯結器](/help/guides/test-in-claude.md)。

## 開始部署

開啟[應用程式詳細資料]頁面，並選取[部署]。**&#x200B;**

選取目標環境，然後選取&#x200B;**[!UICONTROL 部署]**。

![部署 — 選取目標環境](/help/assets/guide-onboarding-agent/deploy-stage.png)

部署會執行四個步驟：

1. **正在準備** — 擷取部署應用程式所需的設定。
2. **開始部署** — 開始背景部署程式。
3. **建置應用程式** — 安裝相依性並建置最新的存放庫程式碼。
4. **發佈** — 將應用程式發佈至[!DNL Adobe I/O Runtime]。

![部署 — 部署管道正在執行](/help/assets/guide-onboarding-agent/deploy-running.png)

>[!NOTE]
>
>如果動作的UI中有中繼資料，但儲存庫中沒有任何相符的處理常式檔案，該動作仍會註冊。 叫用會使用預設的Stub處理常式，直到您新增真正的程式碼為止。

## 成功部署後

完成所有步驟後，對話方塊顯示&#x200B;**部署成功**。

![部署 — 成功部署](/help/assets/guide-onboarding-agent/deploy-successful.png)

按一下&#x200B;**關閉**&#x200B;以關閉對話方塊。 向下捲動至「應用程式詳細資料」頁面上的&#x200B;**[!UICONTROL 測試應用程式]**&#x200B;區段：

![應用程式詳細資料 — 複製MCP伺服器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

每個已部署環境都會顯示一個MCP伺服器URL。 選取「**[!UICONTROL 複製URL]**」，並使用該網址在目標LLM平台中建立外掛程式。

**部署歷史記錄**&#x200B;區段會顯示最後10個部署：

![部署歷史記錄](/help/assets/guide-deploy/deployment-history.png)

每一列會顯示目標&#x200B;**環境** （中繼或生產）、**狀態** （成功或失敗）以及&#x200B;**部署於**&#x200B;日期。 您可以使用此表格來追蹤部署的發生時間，並驗證
最新部署成功。

## 下一步

- [將部署的應用程式測試為ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)。
- [將部署的應用程式測試為Claude聯結器](/help/guides/test-in-claude.md)。

