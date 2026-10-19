---
title: 自訂產生的EDS Widget
description: 瞭解並自訂Adobe LLM應用程式自動建立的Edge Delivery Services Widget。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1065'
ht-degree: 0%
---

# 自訂產生的Widget {#customize-generated-widget}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

>[!NOTE]
>
>本指南假設您已基本熟悉Adobe Edge Delivery Services (EDS)。 如果您是EDS的新手，請先閱讀[EDS開發人員教學課程](https://www.aem.live/developer/tutorial)和[探索區塊](https://www.aem.live/docs/exploring-blocks)，然後再自訂Widget。 這些資源涵蓋區塊、`decorate`函式和EDS專案結構。

成功產生之後，平台會提供連結至[!DNL LLM Apps]中動作的EDS Widget。 當連線至支援的LLM平台時，Widget會呈現代表性資料以供預覽，並讀取處理常式結果。 在自訂Widget之前，請先檢閱產生的設計和行為。

首先，測試產生的Widget。 然後自訂其資料合約、互動和視覺化設計。

**在本指南中：**&#x200B;瞭解如何尋找產生的Widget，並將其調整為符合您動作的資料和您品牌的設計。 然後，在本機預覽您的變更，並在部署的應用程式中測試它們。

## 尋找產生的Widget

開啟在[基礎結構設定](/help/guides/create-app.md)期間連線的EDS存放庫。 如果您使用Adobe受管基礎架構，請先請求程式碼存取權，然後再繼續。 每個產生的Widget都是一個EDS區塊：

```text
blocks/
└── <action-slug>/
    ├── <action-slug>.js
    └── <action-slug>.css
```

- JavaScript檔案會讀取動作結果並建置介面。
- CSS檔案可控制版面、回應式行為和視覺化設計。
- 產生的提取請求會顯示為該動作建立的精確檔案。

概要(Slug)使用連字型大小：程式碼識別碼為`search_products`的動作使用區塊，例如`blocks/search-products/`。 產生的Widget頁面通常是`/eds-widgets/search-products-demo`；在動作中繼資料中使用實際的&#x200B;**[!UICONTROL Widget URL]**。

平台也會設定Widget URL和支援的SDK檔案。 您不需要建立第二個EDS專案或重新輸入Widget URL來自訂產生的Widget。

## LLM應用程式SDK如何連線Widget

`@adobe/llmapps-sdk`套件會將EDS Widget連線到LLM主機。 它在產生的EDS存放庫中的進入點包括：

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

`aem-embed.js`建立主機連線、載入EDS頁面，並呼叫您的區塊：

```javascript
export default async function decorate(block, bridge) {
  // Customize the widget here.
}
```

內嵌會擷取Widget頁面的`.plain.html`內容，並匯入其區塊。 您不會匯入區塊中的SDK。 內嵌會自動提供`bridge`；正常的EDS頁面載入不會提供。 橋接器可讓Widget：

- 讀取含有`bridge.toolResult`的處理常式結果。
- 使用`bridge.applyHostStyles()`套用主機樣式。
- 繼續與`bridge.sendMessage()`的交談。
- 使用`bridge.callTool()`叫用另一個動作。
- 保持其大小與`bridge.autoResize()`同步。

本指南說明常見的橋接器方法。 如需完整API，請參閱[`@adobe/llmapps-sdk`套件](https://www.npmjs.com/package/@adobe/llmapps-sdk)。

## 瞭解資料合約

動作處理常式傳回`structuredContent`，區塊從`bridge.toolResult`讀取它。

```javascript
// Handler result
return {
  content: [{ type: 'text', text: `Found ${records.length} products.` }],
  structuredContent: { arguments: { query: 'coffee' }, records }
};
```

```javascript
// EDS block
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];
  // Render records or an error state when result?.isError is true.
}
```

在此範例中，`PREVIEW_RESULT`是區塊的本機範例資料物件。 目前產生的Widget使用`structuredContent.arguments`和`structuredContent.records`；檢查您產生的檔案是否為正確形狀。 切勿以預覽記錄取代空白的即時結果。

`bridge.toolResult`只解析第一個結果。 如果Widget保持已掛接狀態，且必須處理之後的結果，請使用`bridge.onToolResult(callback)`。

當您變更`structuredContent`時，請一併更新處理常式和Widget。 請勿假設在模型中隱藏結構化資料；它是主機動作結果的一部分。 請參閱[自訂產生的處理常式](/help/guides/customize-handler.md)，以取得完整的傳回合約。

## 安全地呈現外部資料

將處理常式輸出視為不受信任的資料。 偏好使用`textContent`之類的DOM API，而非將回應值插入`innerHTML`。

```javascript
function createProductCard(product, bridge) {
  const card = document.createElement('article');
  card.className = 'product-card';

  const title = document.createElement('h3');
  title.textContent = String(product.name ?? 'Product');

  const button = document.createElement('button');
  button.type = 'button';
  button.textContent = 'Tell me more';
  button.disabled = !bridge?.isConnected;
  const status = document.createElement('p');
  status.setAttribute('role', 'status');
  button.addEventListener('click', async () => {
    if (bridge?.isConnected && typeof product.id === 'string') {
      try {
        await bridge.sendMessage(`Show me details for product ${product.id}`);
      } catch {
        status.textContent = 'Unable to send the request. Try again.';
      }
    }
  });

  card.append(title, button, status);
  return card;
}
```

將URL指派給`href`或`src`之前，請先驗證URL，並僅允許體驗所需的通訊協定。

## 使用主機橋接器

內嵌傳遞橋接器給`decorate(block, bridge)`。 保護與`bridge?.isConnected`的互動式呼叫，以便在直接EDS預覽期間也呈現區塊。 橋接器物件可以獨立模式存在，而無需連線；在獨立模式下等待`toolResult`永遠不會解析。 LLM應用程式預覽也設定`bridge.hostContext.preview`讓區塊選擇範例資料。

### 套用主機樣式

```javascript
if (bridge?.isConnected) {
  bridge.applyHostStyles();
}
```

方法會套用可用的主機CSS變數和字型樣式。 套用主機樣式為選用：產生的Widget可以使用自己的設計代號。 內嵌在其陰影主體上維護`.light`和`.dark`類別。 當您的區塊也需要回應主題變更時，請使用`bridge.hostContext.theme`和`bridge.onContextChange(callback)`。

### 傳送後續追蹤訊息

```javascript
if (bridge?.isConnected) {
  await bridge.sendMessage('Show me similar products.');
}
```

使用`sendMessage`在互動後繼續交談。

### 直接呼叫動作

```javascript
if (bridge?.isConnected) {
  const result = await bridge.callTool('search_products', { query: 'coffee' });
  // Re-render this widget from result.structuredContent, or show result.isError.
}
```

針對需要動作結果的明確互動，請使用`callTool`。 傳遞實際目標動作的程式碼識別碼，而非其區塊資料夾名稱或顯示名稱；`search_products`為範例。 主機必須支援動作呼叫，且目標動作必須允許應用程式可見性。 呼叫動作不會自動重新演算目前的Widget。 僅傳遞驗證的值並處理拒絕的呼叫，而不會公開內部詳細資訊。

如果目標動作的可見度需要變更，請僅在&#x200B;**[!UICONTROL 動作詳細資料]**&#x200B;下編輯該動作、儲存變更，然後重新部署到您正在測試的環境。

### 保持Widget大小同步

```javascript
if (bridge?.isConnected) {
  bridge.autoResize(block);
}
```

若要讓主機回應內容變更，請在初始轉譯後呼叫`autoResize`。 方法會傳回清除函式，此函式會在Widget移除時停止觀察。

## 預覽您的變更

產生的區塊包含範例資料，可在橋接器遺失、中斷連線或標示為預覽時直接預覽。 將範例資料遞補與即時空白和錯誤狀態分開。

若要在本機預覽EDS專案：

```bash
npm install -g @adobe/aem-cli
aem up
```

從本機伺服器上的動作Widget URL開啟路徑，例如`http://localhost:3000/eds-widgets/search-products-demo`，而不僅僅是網站根目錄。 驗證：

- 空白、載入、成功和錯誤狀態。
- 長文字和缺少選用欄位。
- 鍵盤導覽和顯示焦點。
- 淺色和深色主題。
- 窄而寬的版面。

直接EDS預覽不會測試主機交握或即時動作資料。 發佈EDS變更，並在支援的LLM平台上使用即時`structuredContent`進行測試。

## 發佈自訂

1. 認可並推播EDS變更。
2. 確認Widget頁面和變更的指令碼和樣式可在設定的EDS URL中使用。 透過EDS發佈工作流程發佈內容變更。
3. 如果您變更了處理常式或動作中繼資料，請推送這些變更並將應用程式部署至中繼環境。
4. 在支援的LLM平台上測試動作和Widget。 檢視ChatGPT中的[測試](/help/guides/test-in-chatgpt.md)或Claude中的[測試](/help/guides/test-in-claude.md)。
5. 測試成功後，視需要將處理常式和中繼資料變更部署到生產環境。

EDS出版物與應用程式部署不同。 如果Stage和Production使用相同的EDS URL，則這些URL上的Widget變更會影響這兩個環境；部署到Stage不會建立獨立的Widget副本。

## 其他EDS設定

如果您自行建立動作，或想要整合相容的現有EDS網站，請參閱[自備EDS專案](/help/guides/bring-your-own-eds.md)。
