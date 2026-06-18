---
title: 部署您的應用程式
description: 瞭解如何使用LLM應用程式UI將您的Adobe LLM應用程式部署到測試環境和生產環境。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '359'
ht-degree: 0%

---


# 部署您的應用程式

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

撰寫處理常式程式碼並將其推送至連結的存放庫後，您就可以從[!DNL LLM Apps] UI部署應用程式。

## 開始部署

導覽至「應用程式詳細資料」頁面。 按一下右上角的&#x200B;**[!UICONTROL 部署]**&#x200B;按鈕：

![應用程式詳細資料 — 準備部署](/help/assets/guide-deploy/app-detail-deploy-ready.png)

這樣會開啟部署對話方塊。 從下拉式清單中選取目標環境：

![部署對話方塊 — 選取目標環境](/help/assets/guide-deploy/deploy-pipeline-dropdown.png)

按一下&#x200B;**[!UICONTROL 部署]**&#x200B;以啟動管道。 四個步驟為：

1. **收集認證** — 讀取應用程式中繼資料、產生[!DNL GitHub]權杖，以及從主控台API擷取執行階段認證。
2. **觸發組建管道** — 傳送所有引數到組建管道。
3. **複製並建置** — 管道會複製您的存放庫、從UI中繼資料產生`actions.json`、執行`npm install`並執行Webpack以產生`dist/index.js`。
4. **部署至執行階段** — 將套件組合部署至應用程式的[!DNL Adobe I/O Runtime]名稱空間。

管道在啟動後會自動執行並顯示即時進度：

![部署管道正在執行](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

>[!NOTE]
>
>如果動作的UI中有中繼資料，但儲存庫中沒有任何相符的處理常式檔案，該動作仍會註冊。 叫用會使用預設的Stub處理常式，直到您新增真正的程式碼為止。

## 成功部署後

完成所有步驟後，對話方塊會顯示&#x200B;**部署成功**&#x200B;的確認訊息，其中包含已部署的URL和成品詳細資料：

![部署成功](/help/assets/guide-deploy/app-detail-deploy-finish.png)

按一下&#x200B;**關閉**&#x200B;以關閉對話方塊。 向下捲動至「應用程式詳細資料」頁面上的&#x200B;**[!UICONTROL 測試應用程式]**&#x200B;區段：

![測試應用程式 — 已部署的URL](/help/assets/guide-deploy/test-app-deployed.png)

每個環境（**暫存**&#x200B;和&#x200B;**生產**）都會在[!DNL Adobe I/O Runtime]上顯示MCP伺服器URL。 這是您在註冊應用程式時提供給LLM平台的URL。 按一下&#x200B;**複製URL**&#x200B;以將其複製到剪貼簿。

以下&#x200B;**部署歷史記錄**&#x200B;區段會保留跨環境每個部署的完整記錄：

![部署歷史記錄](/help/assets/guide-deploy/deployment-history.png)

每一列會顯示目標&#x200B;**環境** （中繼或生產）、**狀態** （成功或失敗）以及&#x200B;**部署於**日期。 您可以使用此表格來追蹤部署的發生時間，並驗證
最新部署成功。

