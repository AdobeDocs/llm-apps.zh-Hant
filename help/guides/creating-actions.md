---
title: 建立動作
description: 從公用網站產生Adobe LLM應用程式動作、追蹤產生進度、檢閱產生的工作或選擇手動動作建立。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '938'
ht-degree: 0%
---

# 建立動作 {#creating-actions}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

動作是您的應用程式在LLM交談中提供的功能，例如尋找產品、檢查可用性或預訂服務。 每個動作的處理常式都會執行工作，而選用的介面工具會顯示結果。

在[基礎結構設定](/help/guides/create-app.md)之後，選擇如何從應用程式頁面建立動作。 自動產生功能適用於Adobe管理的基礎結構和您自己的存放庫。

**在本指南中：**&#x200B;瞭解如何從您的網站產生動作，或自行建立動作。 若要自動產生，請先啟動程式並檢閱結果，然後再部署和測試應用程式。

## 選擇如何建置動作

開啟您的應用程式。 如果Adobe仍在準備基礎架構，請等待設定完成。

在&#x200B;**[!UICONTROL 動作]**&#x200B;區段中，**[!UICONTROL 您要如何建置動作？]** 提供兩種選擇：

| 選擇 | 使用時機 |
|---|---|
| **[!UICONTROL 自動建置動作]** | 您希望平台分析您的網站，並產生建議的動作、處理常式程式碼、測試和Widget。 這是建議的起點。 |
| **[!UICONTROL 我自己建立動作]** | 您想要定義每個動作，並自行提供其實施。 |

在建立應用程式精靈中選擇基礎結構時，並不會為您選取這些選項中的任何一個。

## 從網站產生動作 {#generate-from-website}

1. 選取&#x200B;**[!UICONTROL 自動建置動作]**。
2. 在&#x200B;**[!UICONTROL 您的網站]**&#x200B;中，輸入代表您品牌的產品、服務或客戶工作的公用HTTPS網站。 此範例使用`https://frescopa.coffee/`。
3. 選取&#x200B;**[!UICONTROL 建置我的動作]**。

![動作 — 自動產生已選取在啟動](/help/assets/guide-creating-actions/build-choice.png)之前輸入的公用網站

使用您有權分析的網站。 請勿提供公開機密或存取控制資訊的網站。

選取&#x200B;**[!UICONTROL 建置我的動作]**&#x200B;會開始產生；單獨輸入URL則不會。 您不需要重新建立應用程式或重新開啟基礎結構精靈。

### 關注產生進度

應用程式頁面顯示&#x200B;**[!UICONTROL 正在產生]**&#x200B;個動作。 選取&#x200B;**[!UICONTROL 移至動作]**，或從導覽開啟&#x200B;**[!UICONTROL 動作]**&#x200B;以追蹤進度。

UI預估產生最多需要30分鐘。 離開頁面，並視需要稍後返回。

![動作 — 正在產生動作，在[動作]頁面](/help/assets/guide-creating-actions/generating.png)上有追蹤進度的連結

在產生期間，平台：

1. 分析網站並識別應用程式的客戶工作。
2. 建立建議的動作中繼資料，包括說明和輸入引數。
3. 在動作存放庫中產生伺服器端處理常式及測試。
4. 在EDS存放庫中產生Widget。
5. 準備產生的動作以供檢閱。

當平台分析網站時，[動作]頁面會顯示&#x200B;**[!UICONTROL 探索對話式體驗的動作]**。 當平台探索到建議的動作時，就會顯示這些動作；它們的程式碼和Widget預覽可能仍在產生中。 等待產生完成後再檢閱每個動作。

>[!IMPORTANT]
>
>產生的處理常式最初會使用代表性範例資料。 網站分析不會自動將應用程式連線至生產API、即時詳細目錄、付款服務或預訂系統。

如果產生報告失敗，請讀取可見的錯誤並檢視[疑難排解指南](/help/reference/troubleshooting.md)。 請勿僅為了略過產生錯誤而核准動作。

## 檢閱產生的動作 {#review-generated-actions}

部署前請先檢閱每個建議的動作。 確認其代表有用的任務，且產生的行為符合您應用程式要執行的操作。

![動作 — 產生的Frescopa動作已準備好檢閱，而且尚未部署](/help/assets/guide-creating-actions/actions-ready-for-review.png)

1. 在&#x200B;**[!UICONTROL 動作]**&#x200B;頁面上，針對已就緒的動作選取&#x200B;**[!UICONTROL 檢閱]**。
2. 檢閱動作的說明、輸入引數、註解、可見度和產生的處理常式。
3. 檢閱Widget設定並預覽（如果動作有Widget）。
4. 檢查產生的程式碼和測試，包括任何連結的提取請求。 檢查什麼是範例行為，哪些必須替換為實際整合。
5. 當動作就緒時，選取&#x200B;**[!UICONTROL 標籤為已檢閱]**。
6. 返回動作清單，然後對其餘動作重複此動作。

儲存的動作編輯器會將&#x200B;**[!UICONTROL LLM Widget詳細資料]**、**[!UICONTROL 動作詳細資料]**&#x200B;和&#x200B;**[!UICONTROL Analytics]**&#x200B;分開。 檢閱Widget和處理常式在其各自標籤中的提取請求。

![產生的動作 — 目前的編輯器索引標籤和標籤為已檢閱，儲存庫連結已隱藏](/help/assets/guide-creating-actions/generated-action-review.png)

選取&#x200B;**[!UICONTROL 標籤為已檢閱]**&#x200B;會合併動作產生的提取請求（如果無合併封鎖程式存在）。 如果開啟的提取請求封鎖稽核，請解決封鎖程式並重試。 請勿核准具有未解析程式碼產生或合併失敗的實作。

檢閱所有產生的動作後，[動作]頁面會顯示&#x200B;**[!UICONTROL 所有動作都已檢閱！]**。 若要返回應用程式，請選取&#x200B;**[!UICONTROL 前往應用程式頁面]**。

檢閱不是部署。 您必須先部署應用程式，才能透過應用程式的MCP伺服器使用這些動作。

## 自行建立動作 {#manual-actions}

如果您不想從網站產生動作：

1. 在應用程式頁面的動作建立選擇中，選取&#x200B;**[!UICONTROL 我將自行建立動作]**。
2. 選取&#x200B;**[!UICONTROL 我將建立我的動作]**。
3. 在[動作]頁面上，選取[**[!UICONTROL 建立動作]**]。
4. 遵循[從頭開始建立動作](/help/guides/create-action.md)以定義中繼資料、實作處理常式，並視需要連線Widget。

基礎建設保持不變。 選擇手動建立不會為您建立處理常式程式碼或連線您的企業系統。

視需要在檢閱自動產生的動作之後新增手動動作。

## 部署和測試

檢閱產生的工作後：

1. [將應用程式](/help/guides/deploy-your-app.md)部署到&#x200B;**[!UICONTROL 階段]**。
2. 使用暫存MCP伺服器URL，在[ChatGPT](/help/guides/test-in-chatgpt.md)或[Claude](/help/guides/test-in-claude.md)中測試它。
3. 測試符合每個動作的提示以及不要求該動作的不相關提示。 檢查文字回應和Widget互動。

在將應用程式提供給客戶使用之前，[自訂處理常式](/help/guides/customize-handler.md)以連線您的API和[自訂介面工具集](/help/guides/widgets.md)。 在Stage中測試應用程式，確認其如預期般運作。 只有在測試成功後才會部署到生產環境。
