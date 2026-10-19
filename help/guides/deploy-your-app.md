---
title: 部署您的應用程式
description: 瞭解如何使用LLM應用程式UI將您的Adobe LLM應用程式部署到測試環境和生產環境。
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '412'
ht-degree: 0%
---

# 部署您的應用程式 {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

檢閱產生的動作或將您自己的處理常式程式碼推送至連結的存放庫後，請從[!DNL LLM Apps]部署應用程式。 針對產生的動作，在部署之前先檢閱並合併產生的工作。

**在本指南中：**&#x200B;瞭解如何將應用程式部署到Stage、檢查部署結果，並複製其MCP伺服器URL以進行測試。 測試成功後部署到生產環境。

## 開始部署

開啟[應用程式詳細資料]頁面，並選取[部署]。****

選取目標環境，然後選取&#x200B;**[!UICONTROL 部署]**。

如果您的處理常式使用[應用程式變數](/help/guides/app-variables.md)，請在部署之前先為目標環境設定這些變數。 新增、更新或刪除的變數會在此部署中生效；「中繼」和「生產」都有獨立的值。

![部署 — 選取目標環境](/help/assets/guide-onboarding-agent/deploy-stage.png)

部署會執行四個步驟：

1. **正在準備** — 擷取部署應用程式所需的設定。
2. **開始部署** — 開始背景部署程式。
3. **建置應用程式** — 安裝相依性並建置最新的存放庫程式碼。
4. **發佈** — 將應用程式發佈至[!DNL Adobe I/O Runtime]。

>[!NOTE]
>
>如果動作的UI中有中繼資料，但存放庫中沒有相符的處理常式檔案，部署仍會註冊該動作。 叫用會使用預設的Stub處理常式，直到您新增真正的程式碼為止。

## 成功部署後

完成所有步驟後，對話方塊顯示&#x200B;**部署成功**。

若要關閉對話方塊，請選取&#x200B;**關閉**。 在「應用程式詳細資料」頁面上，捲動至&#x200B;**[!UICONTROL 測試應用程式]**：

![應用程式詳細資料 — 複製MCP伺服器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

每個已部署環境都會顯示一個MCP伺服器URL。 選取「**[!UICONTROL 複製URL]**」，並使用它來在目標LLM平台中建立外掛程式或聯結器。

**部署歷史記錄**&#x200B;區段會顯示最後10個部署：

![部署歷史記錄](/help/assets/guide-deploy/deployment-history.png)

每一列會顯示目標&#x200B;**環境** （中繼或生產）、**狀態** （成功或失敗）以及&#x200B;**部署於**&#x200B;日期。 請使用表格來追蹤部署日期，並確認最新部署是否成功。

## 部署設定變更

動作中繼資料、應用程式頁面的&#x200B;**[!UICONTROL 可見度]**&#x200B;切換、驗證和應用程式變數的變更，會在下次部署至該環境時生效。 儲存設定本身不會更新已部署的MCP伺服器。

每次都檢查目標環境。 部署到中繼不會將相同的變更部署到生產環境。

## 下一步

- [將部署的應用程式測試為ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)。
- [將部署的應用程式測試為Claude聯結器](/help/guides/test-in-claude.md)。
