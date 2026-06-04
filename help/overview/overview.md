---
title: 概觀
description: 瞭解什麼是Adobe LLM應用程式、其運作方式，以及您需要啟動哪些應用程式。
source-git-commit: f144ccfc0ede6c556ccf4d99173f91d372add6f7
workflow-type: tm+mt
source-wordcount: '863'
ht-degree: 1%

---


>[!NOTE]
>
>[!DNL Adobe LLM Apps]目前在Beta中。 此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

## 什麼是[!DNL Adobe LLM Apps]？

[!DNL Adobe LLM Apps]可讓您的品牌直接在AI助理（例如[!DNL ChatGPT]或Claude）中公開關鍵動作，例如產品探索、可用性檢查或服務預訂。 您的品牌不必在AI產生的答案中被被動提及，而是可以引導客戶完成真實的業務流程，永遠不離開對話。

[!DNL LLM Apps]位於[experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)。

## 您可以使用[!DNL LLM Apps]做什麼

- **建立品牌擁有的LLM動作** — 定義您要在AI助理內啟用的特定業務流程（例如，*排程測試磁碟機*、*比較產品*、*預約服務*）。
- **建立互動式LLM Widget** — 建立視覺化UI元件（產品卡、預訂表單、商店位置），在您的[!DNL GitHub]存放庫中管理為AEM元件。
- **維持集中式品牌控管** — 作者和開發人員可透過AEM管理核准，完整控制LLM平台內公開的所有內容、復本和視覺效果。
- **部署到中繼和生產環境** — 控制的部署管道可讓您在升級到生產環境之前，先在中繼環境中測試體驗。
- **動作層級的控制項可見性** — 部署後，可以在不重新部署整個應用程式的情況下開啟或關閉個別動作。
- **測量推動決定的因素** — 內建分析（由Adobe Customer Journey Analytics提供技術支援）表面動作觸發計數、成功率、放棄率、熱門使用者提示和可見度分數。

## 為什麼[!DNL LLM Apps]重要

LLM互動與傳統搜尋截然不同。 平均[!DNL ChatGPT]工作階段的持續時間是傳統搜尋工作階段的四倍。 超過40%的消費者仰賴AI工具做出複雜的購買決策。 如果沒有[!DNL LLM Apps]，您可能會贏得提及但失去客戶。 [!DNL LLM Apps]可確保您的品牌不僅可見，而且可在使用者準備要決定的確切時刻操作。

## 重要概念

**LLM應用程式** — 使用者在[!DNL ChatGPT]或其他LLM平台中互動的品牌化助理。 它將您所有的動作組成群組，並以單一單元進行部署。

**動作** — 您的應用程式所提供的功能。 例如，「尋找經銷商」或「瀏覽產品」。 使用者提出相關問題時，LLM會叫用每個動作。 每個動作有兩個部分：在[!DNL LLM Apps] UI中管理的中繼資料（名稱、說明、引數），以及在[!DNL GitHub]中的處理常式（您的程式碼）。

**動作處理常式** — 叫用動作時執行的程式碼。 它可以呼叫您的API、擷取即時資料或傳回靜態資料。 在`actions/<name>/index.js`的[!DNL GitHub]存放庫中有處理常式。

**Widget** — 顯示給使用者的視覺回應 — 卡片、輪播、表格或任何與LLM文字回覆一起呈現的自訂UI。 Widget是在[!DNL Edge Delivery Services] (EDS)網站上託管的HTML頁面。

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

## 先決條件

### Adobe Developer Console

您需要存取具有您Adobe IMS組織中&#x200B;**開發人員**&#x200B;角色（或&#x200B;**系統管理員**&#x200B;角色）的[Adobe Developer Console](https://developer.adobe.com/console)。 確保您的組織可以存取[[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)。

若要驗證，請移至[developer.adobe.com/console](https://developer.adobe.com/console)。 如果您看見「快速入門」畫面，表示您的許可權設定正確。

![Adobe Developer Console — 確認開發人員存取的快速入門畫面](/help/assets/overview/dev-console-access-granted.png)

如果您看到&#x200B;**限制存取**&#x200B;訊息，則表示您沒有開發人員角色。 請聯絡您的IMS組織管理員以請求存取權。

![Adobe Developer Console — 限制存取訊息](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

您需要在組織中擁有下列許可權的[!DNL GitHub]帳戶：

- **建立存放庫** — 您需要在組織中建立兩個存放庫：一個用於應用程式程式碼，另一個用於EDS專案。 若要確認，請移至[github.com/new](https://github.com/new) — 如果您可從&#x200B;**擁有者**&#x200B;下拉式清單中選取您的組織，則表示您擁有許可權。

  ![GitHub新存放庫擁有者下拉式清單，顯示組織選擇](/help/assets/overview/github-repo-owner-dropdown.png)

- **安裝[!DNL GitHub]應用程式** — 您需要適當的許可權，才能在您的組織上安裝[!DNL GitHub]應用程式。 請參閱[安裝GitHub應用程式的需求](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app)。

### 具有[!DNL Edge Delivery Services]的AEM Sites

動作Widget託管於&#x200B;**Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**。 您的組織需要包含[!DNL Edge Delivery Services]的AEM Sites授權。 您的EDS組織中必須有&#x200B;**管理員**&#x200B;角色。

若要驗證，請移至[EDS使用者管理工具](https://tools.aem.live/tools/user-admin/index.html)，輸入您的組織名稱，將&#x200B;**網站**&#x200B;留白，然後按一下&#x200B;**擷取使用者**。 在清單中尋找您的帳戶，並確認它顯示&#x200B;**管理員**&#x200B;徽章。

![EDS使用者管理員工具顯示具有管理員角色的使用者](/help/assets/overview/eds-user-admin.png)

### LLM Platform （用於測試）

若要測試您部署的應用程式，您需要支援的訂閱層級，以允許自訂MCP應用程式和啟用&#x200B;**開發人員模式**。 例如，[!DNL ChatGPT]需要&#x200B;**Pro**、**Business**&#x200B;或&#x200B;**Enterprise / Edu**&#x200B;訂閱。

## 快速入門

選擇符合您情況的路徑：

| | **Beta參與者** | **一般可用性** |
|---|---|---|
| **您有** | 您參與Beta計畫，並已從Adobe收到應用程式程式碼封存、EDS專案封存和應用程式設定參考 | 以使用案例為原則 — Adobe會引導您建置和部署應用程式 |
| **從這裡開始** | [Beta上線](/help/beta-onboarding/beta-onboarding.md) | [建立應用程式](/help/guides/create-app.md) |

