---
title: Adobe LLM應用程式概觀
description: 瞭解什麼是Adobe LLM應用程式、其運作方式，以及如何開始建立您的應用程式。
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '621'
ht-degree: 0%
---

# Adobe LLM應用程式 — 概觀 {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

## 什麼是[!DNL Adobe LLM Apps]？

[!DNL Adobe LLM Apps]可讓您的品牌在受支援的AI助理（例如[!DNL ChatGPT]和[!DNL Claude]）內提供有用的動作，例如產品探索、可用性檢查或服務預訂。

[!DNL LLM Apps]位於[experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps)。

## 您可以使用[!DNL LLM Apps]做什麼

- **建立品牌擁有的LLM動作** — 定義您要在AI助理內啟用的特定業務流程（例如，*排程測試磁碟機*、*比較產品*、*預約服務*）。
- **建立互動式LLM Widget** — 建立視覺化UI元件（產品卡、預訂表單、商店位置），在您的[!DNL GitHub]存放庫中管理為AEM元件。
- **維持集中式品牌控管** — 作者和開發人員可透過AEM管理核准，完整控制LLM平台內公開的所有內容、復本和視覺效果。
- **部署到中繼和生產環境** — 控制的部署管道可讓您在升級到生產環境之前，先在中繼環境中測試體驗。
- **動作層級的控制項可見性** — 啟用或停用個別動作，然後部署應用程式以套用變更。
- **測量推動決定的因素** — 內建分析（由Adobe Customer Journey Analytics提供技術支援）表面動作觸發計數、成功率、放棄率、熱門使用者提示和可見度分數。

## 為什麼[!DNL LLM Apps]重要

LLM互動與傳統搜尋截然不同。 平均LLM工作階段的長度是傳統搜尋工作階段的四倍。 超過40%的消費者仰賴AI工具做出複雜的購買決策。 如果沒有[!DNL LLM Apps]，您可能會贏得提及但失去客戶。 [!DNL LLM Apps]可確保您的品牌不僅可見，而且可在使用者準備要決定的確切時刻操作。

## 重要概念 {#key-concepts}

### LLM應用程式

您的品牌助理，使用者可在[!DNL ChatGPT]或其他LLM平台內互動。 它將您所有的動作組成群組，並以單一單元進行部署。

### 動作 {#actions}

您的應用程式所提供的功能，例如&#x200B;*尋找散發者*&#x200B;或&#x200B;*瀏覽產品*。 當要求符合其描述時，LLM平台會叫用動作。 [!DNL LLM Apps]管理動作中繼資料，且[!DNL GitHub]儲存庫儲存處理常式程式碼。

### 動作處理常式

LLM平台叫用動作時所執行的伺服器端函式。 使用處理常式來驗證輸入、呼叫API，並傳回文字加上結構化資料。

### 小工具 {#widgets-eds}

與LLM回覆一起顯示的視覺回應，例如卡片、輪播或表格。 產生的Widget是[!DNL Edge Delivery Services] (EDS)存放庫中的區塊。 透過Adobe受管基礎架構，Adobe會準備並託管存放庫以進行試用。 或者，連線您自己的存放庫。

### MCP伺服器

部署後公開的端點。 支援的LLM平台會連線至此端點，以探索並叫用您的動作。

## 運作方式

您在[!DNL LLM Apps]中定義您品牌的功能。 平台會將這些功能設為動作，而AI助理會使用這些動作來回應對話中的客戶請求。

```
┌────────────────────┐          ┌────────────────────┐          ┌────────────────────┐
│     Your brand     │          │      LLM Apps      │          │    AI assistant    │
│                    │          │                    │          │                    │
│   What you offer   │   ───▶   │  Turns that into   │   ───▶   │ Answers with your  │
│  and how you help  │          │    something AI    │          │    brand, live     │
│  customers today   │          │     can act on     │          │  inside the chat   │
└────────────────────┘          └────────────────────┘          └────────────────────┘
```

如需建置內容和元件如何共同運作的技術詳細資訊，請參閱[應用程式如何連線](/help/guides/app-architecture.md)。

## 快速入門 {#choose-your-journey}

建立應用程式和建立其動作是獨立的步驟：

1. [設定基礎結構](/help/guides/create-app.md)。 使用Adobe受管理基礎架構進行試用，或透過建立應用程式精靈提供兩個空白的[!DNL GitHub]存放庫。 平台會分別初始化一個動作處理常式和一個介面工具集。
2. [建立您的動作](/help/guides/creating-actions.md)。 請平台分析您的網站並產生動作，或自行建立。
3. [部署並測試應用程式](/help/guides/deploy-your-app.md)。 檢閱產生的工作、部署至中繼，並在支援的LLM平台中測試。

自動產生可為您提供範例行為的起點。 在將應用程式提供給客戶使用之前，請[將您的處理常式連線至您的系統](/help/guides/customize-handler.md)以及[自訂介面工具集](/help/guides/widgets.md)。
