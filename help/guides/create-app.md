---
title: 建立應用程式
description: 瞭解如何建立您的第一個LLM應用程式，並將其連結至您的GitHub存放庫。
source-git-commit: 344c5457eb79a19b1dae823732a1cd9866dcd9dc
workflow-type: tm+mt
source-wordcount: '720'
ht-degree: 0%

---


# 建立應用程式

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

>[!NOTE]
>
>開始之前，請確定已符合所有[必要條件](/help/overview/overview.md#prerequisites)。

本指南會逐步引導您建立第一個[!DNL Adobe LLM Apps] — 從空白狀態到連結至[!DNL GitHub]存放庫的完整設定專案。

## 開啟[!DNL LLM Apps]

導覽至[experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)。 如果尚未建立任何應用程式，您會看到第一個載入頁面，提示您建立第一個應用程式。

![應用程式頁面 — 尚未建立任何應用程式](/help/assets/guide-create-app/first-load.png)

左側邊欄可讓您在&#x200B;**[!UICONTROL 應用程式]**&#x200B;與&#x200B;**[!UICONTROL 動作]**&#x200B;之間導覽。 按一下&#x200B;**[!UICONTROL 建立應用程式]**&#x200B;以開始。

## 填寫應用程式詳細資料

「建立應用程式」對話方塊會開啟全熒幕。

![建立應用程式對話方塊](/help/assets/guide-create-app/app-details-1.png)

輸入下列內容：

- **[!UICONTROL LLM應用程式名稱]** （必要） — 您應用程式的顯示名稱。 只允許使用字母、數字和空格。
- **[!UICONTROL LLM應用程式描述]** — 應用程式的簡短描述。 例如，*協助使用者透過LLM平台*&#x200B;探索產品和預約服務。
- **[!UICONTROL 您的網站]** （必要） — 您品牌網站的URL。 [!DNL LLM Apps]使用此自動建立預先設定的動作。

## 選取分析資料區域

選擇此應用程式的分析資料儲存區域。

>[!IMPORTANT]
>
>應用程式建立後，就無法變更分析資料區域。

![分析資料區域下拉式清單](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

**Analytics區域**&#x200B;下拉式清單預設為&#x200B;**美國(US)**。 可用的選項為&#x200B;**美國(US)**&#x200B;和&#x200B;**歐洲(EU)**。 請先選取最符合您資料駐留要求的區域，再繼續進行。

## 連結[!DNL GitHub]存放庫

在應用程式詳細資訊下方，您可以連結[!DNL GitHub]存放庫。 此存放庫是您動作處理常式程式碼所在的位置 — 當LLM平台叫用您的應用程式時，JavaScript會在[!DNL Adobe I/O Runtime]上執行的`actions/`資料夾下運作。

如果您是第一次這樣做，清單中不會出現任何存放庫。 您必須在您的組織上安裝&#x200B;**[!DNL Adobe LLM Apps Link]** [!DNL GitHub]應用程式：

1. 按一下對話方塊底部的Github **上的**&#x200B;管理存放庫。
2. 這會在新索引標籤中開啟[!DNL Adobe LLM Apps Link] [!DNL GitHub]應用程式頁面。

   ![Adobe LLM應用程式連結 — GitHub應用程式安裝頁面](/help/assets/guide-create-app/github-app-install.png)

3. 按一下「安裝&#x200B;**[!UICONTROL 」]**&#x200B;並選取您的[!DNL GitHub]組織。
4. 在&#x200B;**[!UICONTROL 存放庫存取權]**&#x200B;下，選擇&#x200B;**僅選取存放庫**，並選取將裝載應用程式程式碼的存放庫。

   ![Adobe LLM應用程式連結 — 存放庫存取權](/help/assets/guide-create-app/github-repo-access.png)

5. 按一下&#x200B;**[!UICONTROL 儲存]**。 返回[建立應用程式]對話方塊 — 您的存放庫現在會顯示在&#x200B;**選取存放庫**&#x200B;下拉式清單中。
6. 選擇您要使用的存放庫。

![建立應用程式對話方塊 — 已連結的存放庫](/help/assets/guide-create-app/app-details-repo-linked.png)

>[!NOTE]
>
>您可以在建立應用程式期間略過連結存放庫，稍後再從應用程式設定中略過連結。 但是，在連結存放庫之前，您無法部署。

## 建立應用程式

按一下&#x200B;**[!UICONTROL 建立應用程式]**。 在Developer Console中建立專案時，畫面會隨即顯示載入畫面。

![正在建立應用程式 — 正在載入畫面](/help/assets/guide-create-app/app-loading.png)

完成後，您會被重新導向至&#x200B;**應用程式詳細資料**&#x200B;頁面。

## 應用程式詳細資訊頁面

「應用程式詳細資料」頁面是管理應用程式的中心樞紐。

![應用程式詳細資料頁面 — 最上層區段](/help/assets/guide-create-app/app-detail-top.png)

### 應用程式橫幅

![應用程式橫幅](/help/assets/guide-create-app/app-banner.png)

頂端的彩色橫幅會顯示目前選取的應用程式，包括應用程式頭像、名稱、說明，以及可在應用程式之間切換的下拉式清單。 捲動時，橫幅會固定在頂端。

### 頁面標題和動作

![應用程式橫幅](/help/assets/guide-create-app/page-title.png)

在橫幅下方，您會看到應用程式名稱當作標題，其中包含下列動作按鈕：

- **...** （更多動作） — 建立新的應用程式或刪除目前的應用程式。
- **[!UICONTROL 設定]** — 設定連結的存放庫和其他選項。
- **[!UICONTROL 部署]** — 將您的應用程式部署到[!DNL Adobe I/O Runtime] （在連結存放庫之前為停用）。

### 應用程式資訊卡

![應用程式資訊卡](/help/assets/guide-create-app/app-info-card.png)

此卡片會摘要您應用程式的金鑰中繼資料：名稱、說明、狀態徽章（**未部署**&#x200B;或&#x200B;**已部署**）、應用程式ID和建立日期。 它也會顯示兩個連結的存放庫：

- **處理常式存放庫** — 動作處理常式程式碼所在的位置（JavaScript會在[!DNL Adobe I/O Runtime]上運作）。
- **EDS存放庫** — 存放Widget UI （[!DNL Edge Delivery Services]提供的區塊和樣式）。

### 動作、測試應用程式和部署歷史記錄

![應用程式詳細資料頁面 — 底部區段](/help/assets/guide-create-app/app-detail-bottom.png)

在資訊卡下方，您會找到三個區段：

- **[!UICONTROL 動作]** — 列出為您的應用程式定義的動作處理常式。 按一下&#x200B;**移至動作**&#x200B;以瀏覽至動作頁面。
- **[!UICONTROL 測試應用程式]** — 部署後，顯示中繼和生產環境的MCP伺服器URL。
- **部署歷史記錄** — 追蹤跨具有狀態和日期的環境的每個部署。

## 後續步驟

- [指南：建立動作](/help/guides/create-action.md) — 定義具有中繼資料和Widget設定的動作。

