---
title: Adobe LLM應用程式概觀
description: 瞭解什麼是Adobe LLM應用程式、其運作方式，以及您需要啟動哪些應用程式。
source-git-commit: 8b4027d0fd73b8134a7478a5044f992e6cf03024
workflow-type: tm+mt
source-wordcount: '972'
ht-degree: 1%

---


# Adobe LLM應用程式 — 概觀 {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

## 什麼是[!DNL Adobe LLM Apps]？

[!DNL Adobe LLM Apps]可讓您的品牌在AI助理（例如[!DNL ChatGPT]）內提供有用的動作，例如產品探索、可用性檢查或服務預訂。

[!DNL LLM Apps]可在[experience.adobe.com](https://experience.adobe.com/#/@llmapps/llm-apps/)取得。

## 您可以使用[!DNL LLM Apps]做什麼

- **建立品牌擁有的LLM動作** — 定義您要在AI助理內啟用的特定業務流程（例如，*排程測試磁碟機*、*比較產品*、*預約服務*）。
- **建立互動式LLM Widget** — 建立視覺化UI元件（產品卡、預訂表單、商店位置），在您的[!DNL GitHub]存放庫中管理為AEM元件。
- **維持集中式品牌控管** — 作者和開發人員可透過AEM管理核准，完整控制LLM平台內公開的所有內容、復本和視覺效果。
- **部署到中繼和生產環境** — 控制的部署管道可讓您在升級到生產環境之前，先在中繼環境中測試體驗。
- **動作層級的控制項可見性** — 部署後，可以在不重新部署整個應用程式的情況下開啟或關閉個別動作。
- **測量推動決定的因素** — 內建分析（由Adobe Customer Journey Analytics提供技術支援）表面動作觸發計數、成功率、放棄率、熱門使用者提示和可見度分數。

## 為什麼[!DNL LLM Apps]重要

LLM互動與傳統搜尋截然不同。 平均[!DNL ChatGPT]工作階段的持續時間是傳統搜尋工作階段的四倍。 超過40%的消費者仰賴AI工具做出複雜的購買決策。 如果沒有[!DNL LLM Apps]，您可能會贏得提及但失去客戶。 [!DNL LLM Apps]可確保您的品牌不僅可見，而且可在使用者準備要決定的確切時刻操作。

## 重要概念 {#key-concepts}

### LLM應用程式

您的品牌助理，使用者可在[!DNL ChatGPT]或其他LLM平台內互動。 它將您所有的動作組成群組，並以單一單元進行部署。

### 入門代理程式

引導式應用程式建置工作流程由&#x200B;**[!UICONTROL 自動建置我的應用程式]**&#x200B;啟動。 它會分析您的網站、建議動作，並為每個動作產生處理常式和Widget。

### 動作 {#actions}

您的應用程式所提供的功能，例如&#x200B;*尋找散發者*&#x200B;或&#x200B;*瀏覽產品*。 當要求符合其描述時，LLM平台會叫用動作。 在[!DNL LLM Apps]中管理動作中繼資料，而其處理常式是您[!DNL GitHub]存放庫中的程式碼。

### 動作處理常式

叫用動作時執行的伺服器端函式。 它可以驗證輸入、呼叫API並傳回文字加上結構化資料。

### 小工具 {#widgets-eds}

與LLM回覆一起顯示的視覺回應，例如卡片、輪播或表格。 產生的Widget是您擁有的[!DNL Edge Delivery Services] (EDS)存放庫中的區塊。

### MCP伺服器

部署後公開的端點。 支援的LLM平台會連線至此端點，以探索並叫用您的動作。

## 運作方式

下圖說明各個片段如何結合在一起 — 從UI中的定義應用程式，到在LLM平台中看到即時結果。

```
┌─────────────────────────────────────────────────────────────┐
│                      LLM Apps UI                            │
│  ┌──────────┐   ┌──────────┐   ┌───────────────────────┐    │
│  │   App    │──▶│ Actions  │──▶│ Metadata + Widget cfg │    │
│  └──────────┘   └──────────┘   └───────────┬───────────┘    │
└─────────────────────────────────────────── │ ────────────-──┘
                                             │ deploy
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Adobe I/O Runtime                          │
│               MCP Server (auto-generated)                   │
│  ┌───────────────┐ ┌──────────────────┐ ┌───────────────┐   │
│  │ search-       │ │ get-product-     │ │ find-where-   │   │
│  │ products      │ │ details          │ │ to-buy        │   │
│  └───────────────┘ └──────────────────┘ └───────────────┘   │
└──────────────────────────────┬──────────────────────────────┘
                               │ MCP protocol
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                        ChatGPT                              │
│  Conversation                                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  EDS Widget                                           │  │
│  │  Product carousel, store locator, detail card ...     │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## 要求 {#requirements}

請先完成下列所有需求，再建立應用程式。

### Adobe Developer Console

您的Adobe IMS組織必須擁有[[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)的存取權。 您需要&#x200B;**開發人員**&#x200B;或&#x200B;**系統管理員**&#x200B;角色。

若要驗證您的存取權，請開啟[Adobe Developer Console](https://developer.adobe.com/console)。 「快速入門」畫面會確認您具有所需的存取權。

![Adobe Developer Console — 確認開發人員存取的快速入門畫面](/help/assets/overview/dev-console-access-granted.png)

如果您看到&#x200B;**受限存取**，請聯絡您的IMS組織管理員並請求開發人員角色。

![Adobe Developer Console — 限制存取訊息](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

您需要一個[!DNL GitHub]帳戶，該帳戶可以：

- 在擁有應用程式的帳戶或組織中建立兩個存放庫。
- 安裝或要求安裝Adobe LLM Apps [!DNL GitHub]應用程式。
- 安裝或要求為EDS存放庫安裝AEM Code Sync。

若要驗證存放庫建立存取權，請開啟[github.com/new](https://github.com/new)，並確認預期的帳戶或組織出現在&#x200B;**擁有者**&#x200B;之下。

![GitHub — 選取存放庫擁有者](/help/assets/overview/github-repo-owner-dropdown.png)

針對組織擁有的存放庫，組織管理員可能需要核准[!DNL GitHub]應用程式。 僅將每個應用程式存取權授與LLM應用程式使用的存放庫。

### AEM Sites與Edge Delivery Services

您的組織需要包含Edge Delivery Services (EDS)的Adobe Experience Manager Sites授權。 您還需要具有從Widget存放庫建立之EDS網站的管理員存取權。

若要驗證存取權，請開啟[EDS使用者管理工具](https://tools.aem.live/tools/user-admin/index.html)，輸入組織名稱，然後擷取使用者。 確認您的帳戶具有&#x200B;**管理員**&#x200B;徽章。

### 網站

您需要公開HTTPS網站，以代表應用程式應支援的產品、服務或工作。 入門代理程式會分析此網站以建議動作並建立代表性範例資料。

請勿使用公開機密或存取控制資訊的網站。

### [!DNL ChatGPT]以進行測試

若要完成快速入門教學課程，請使用支援的[!DNL ChatGPT]計畫並啟用開發人員模式。 Workspace管理員可以限制存取權。 檢視ChatGPT中的[測試](/help/guides/test-in-chatgpt.md#plan-requirements)。

## 選擇您的歷程 {#choose-your-journey}

### &#x200B;1. 建置並啟動您的第一個應用程式

從[建置並啟動您的第一個應用程式](/help/guides/create-app.md)開始。 此歷程從兩個空的存放庫開始，以測試為[!DNL ChatGPT]外掛程式的生產就緒應用程式結束。

### &#x200B;2. 自訂產生的應用程式

當上線代理程式建立應用程式且您想要取代範例行為時，請選擇此歷程：

1. [自訂產生的處理常式](/help/guides/customize-handler.md)以連線您的API並定義每個動作傳回的資料。
2. [自訂產生的Widget](/help/guides/widgets.md)以使用該資料，並套用您的互動和設計。

### &#x200B;3. 從頭開始新增動作

選擇[從頭開始新增動作](/help/guides/create-action.md)以定義新的中繼資料、撰寫處理常式、連線Widget、測試及部署動作。

### &#x200B;4. 連線現有的EDS專案

若您已有EDS網站或未使用入門代理程式，請選擇[連線現有的EDS專案](/help/guides/bring-your-own-eds.md)。

每個歷程都使用共用的[部署](/help/guides/deploy-your-app.md)和[ChatGPT外掛程式測試](/help/guides/test-in-chatgpt.md)步驟。

