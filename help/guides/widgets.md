---
title: 設定Widget (EDS)
description: 瞭解如何設定Edge Delivery Services Widget專案，以及實作區塊合約，以在LLM平台內呈現視覺回應。
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '1214'
ht-degree: 1%

---


# 設定Widget (EDS)

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。 此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。

本指南說明如何建立端對端的EDS Widget：從在[!DNL LLM Apps] UI中設定您的動作，到設定您的EDS專案，再到撰寫區塊程式碼以在LLM平台中呈現您的資料。 如需高階概觀，請參閱[核心概念](/help/overview/overview.md#widgets-eds)。

## [!DNL LLM Apps] SDK

所有內容都以[`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk) npm套件開始。 SDK是JavaScript程式庫，可支援Widget與LLM主機之間的雙向通訊通道。

SDK也提供`aem-embed.js` — EDS專用的進入點，可將SDK插入標準EDS區塊管道。 當您`npm install @adobe/llmapps-sdk`時，安裝後指令碼會自動將兩個檔案複製到您的專案中：

```
scripts/
└── llm-apps/
    ├── aem-embed.js     ← EDS widget entry point, ships with the SDK
    └── llmapps-sdk.js   ← core SDK, loaded internally by aem-embed.js
```

在EDS專案中，**您絕對不會在區塊代碼中直接使用SDK。** `aem-embed.js`會建立和管理SDK連線，並將完全連線的`LLMApp`執行個體傳遞至您的區塊，做為`decorate(block, bridge)`中的`bridge`引數。 `bridge`上有完整的SDK API可用 — 不需要匯入。

如果您正在建立不含EDS **（標準套件組合器或TypeScript專案）的Widget**，可以直接使用SDK：

```javascript
import { LLMApp } from '@adobe/llmapps-sdk';

const app = new LLMApp({ appInfo: { name: 'MyWidget', version: '1.0.0' } });
await app.connect();

const { structuredContent } = await app.toolResult;
```

## 整合方式

當AI呼叫您的動作且處理常式傳回`structuredContent`時，LLM平台會在交談中呈現互動式Widget。 三件事讓此共同運作：

**[!DNL LLM Apps] UI** — 當您建立動作時，請在Widget中繼資料標籤中輸入&#x200B;**[!UICONTROL 指令碼URL]**&#x200B;和&#x200B;**[!UICONTROL Widget URL]**。 指令碼URL指向`aem-embed.js` — SDK隨附的檔案，且位於`scripts/llm-apps/aem-embed.js`的EDS存放庫中。 這會告訴LLM平台叫用動作時要載入的指令碼。

**`aem-embed.js`** — LLM平台將此指令碼載入沙箱Widget表面。 `aem-embed.js`是自訂HTML元素(`<aem-embed>`)，可作為您的Widget的EDS感知進入點。 它會使用SDK與LLM主機執行交握、隱藏正常的EDS頁面管道（無頁首/頁尾）、從Widget URL擷取您的EDS頁面內容、執行EDS區塊管道，並將即時`bridge`物件傳送至每個區塊的`decorate()`函式。

**您的區塊代碼** — 您編寫了匯出`decorate(block, bridge)`函式的標準EDS區塊。 `bridge`是連線的SDK執行個體 — 它為您提供動作的結構化結果，並讓您傳回訊息至交談。

## 新增至現有的EDS專案

如果您已有EDS專案，則只有兩個步驟才能開始寫入區塊。

1. 安裝`@adobe/llmapps-sdk`。 安裝後指令碼將`aem-embed.js`和`llmapps-sdk.js`複製到`scripts/llm-apps/`：

   ```bash
   npm install @adobe/llmapps-sdk
   ```

2. 設定CORS標頭，讓LLM平台可以跨原始載入您的Widget頁面和指令碼 — 請參閱下方的[設定CORS標頭](#configure-cors-headers)。

然後依照[`decorate(block, bridge)`合約](#the-decorateblock-bridge-contract)建立您的區塊、編寫Widget頁面，並在「建立動作」對話方塊中輸入URL。

## 設定新的EDS專案

### 建立存放庫

1. 根據[AEM範本](https://github.com/adobe/aem-boilerplate)範本建立新的[!DNL GitHub]存放庫。
2. 將[AEM程式碼同步GitHub應用程式](https://github.com/apps/aem-code-sync)新增至存放庫。
3. 安裝本機開發的AEM CLI： `npm install -g @adobe/aem-cli`。
4. 安裝`@adobe/llmapps-sdk`。 安裝後指令碼將`aem-embed.js`和`llmapps-sdk.js`複製到`scripts/llm-apps/`：

   ```bash
   npm install @adobe/llmapps-sdk
   ```

如需EDS專案的完整指南，請參閱[AEM開發人員教學課程](https://www.aem.live/developer/tutorial)和[專案剖析](https://www.aem.live/developer/anatomy-of-a-project)。

設定後，您的EDS網站將可在此取得：

- **預覽：**`https://main--<repo>--<owner>.aem.page/`
- **即時：**`https://main--<repo>--<owner>.aem.live/`

### 存放庫結構

```
my-brand-eds/
├── scripts/
│   ├── llm-apps/
│   │   ├── aem-embed.js           # Widget entry point — copied by post-install
│   │   └── llmapps-sdk.js         # Core SDK — copied by post-install
│   ├── aem.js                     # AEM core library
│   └── scripts.js                 # Site-level decoration and loading
├── blocks/
│   └── search-products/           # One folder per widget block
│       ├── search-products.js
│       └── search-products.css
├── styles/
│   └── styles.css
├── head.html
└── package.json
```

### 設定CORS標頭

您的EDS Widget頁面會由LLM平台載入沙箱Widget表面中。 EDS網站必須傳回正確的`access-control-allow-origin`標頭，主機才能跨來源擷取您的Widget內容。

標頭是透過`admin.hlx.page`的AEM管理面板使用[設定服務](https://aem.live/docs/config-service-setup)設定的。 為您的Widget頁面和SDK指令碼上線的路徑新增自訂回應標題：

```json
{
  "/<your-widget-pages-path>/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ],
  "/scripts/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ]
}
```

>[!NOTE]
>
>使用`*`作為原始值對於`.aem.live`網域上的公用Widget內容是可接受的。 如果您的網站包含受保護的內容，請將來源限製為特定網域。

### 建立Widget頁面

在您的EDS編寫工具中建立頁面，並將您的區塊新增到其中。 頁面URL會變成您在動作中設定的&#x200B;**[!UICONTROL Widget URL]** — 這是動作和區塊之間的唯一連線。 您的區塊與動作名稱之間沒有命名需求。

![EDS編寫 — 區塊已新增至Widget頁面](/help/assets/guide-widget/aem-author.png)

### 在建立動作對話方塊中輸入URL

設定EDS存放庫後，在建立動作時移至&#x200B;**Widget中繼資料→範本URL**：

**[!UICONTROL 指令碼URL]** — 指向您EDS存放庫中的`aem-embed.js`。 對於相同EDS專案中的每個動作，這是相同的值：

```
https://main--<repo>--<owner>.aem.live/scripts/llm-apps/aem-embed.js
```

**[!UICONTROL Widget URL]** — 您為此Widget建立之EDS頁面的URL。 每個動作不重複：

```
https://main--<repo>--<owner>.aem.live/<path-to-your-widget-page>
```

LLM平台從指令碼URL載入`aem-embed.js`。 然後`aem-embed.js`會從Widget URL擷取`.plain.html`以取得您的區塊內容。

## 資料流程

從您的處理常式到轉譯Widget的完整路徑：

1. **動作處理常式**&#x200B;傳回`structuredContent`：

```javascript
// actions/search-products/index.js
return {
  structuredContent: {
    products: [
      { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
      { id: 'COF-002', name: 'Colombia Huila Natural', price: '$22', rating: 4.5 },
    ],
    total: 2,
    category: 'coffee'
  }
};
```

1. **LLM平台**&#x200B;會開啟Widget介面，並從指令碼URL載入`aem-embed.js`。

1. **`aem-embed.js`**&#x200B;透過SDK連線至主機、從Widget URL擷取`.plain.html`、執行EDS區塊管道，以及在您的區塊上呼叫`decorate(block, bridge)`。

1. **您的區塊**&#x200B;會從`bridge.toolResult`讀取資料並轉譯UI。

1. **使用者互動**&#x200B;會觸發`bridge.sendMessage(...)`或`bridge.callTool(...)`，傳送後續資訊到交談中。

## `decorate(block, bridge)`合約

每個EDS Widget區塊都應該匯出預設的`decorate`函式。 這是標準EDS區塊簽章，以第二個引數擴充 — 連線的`bridge`，也就是具有完整API可用的[`LLMApp`](https://www.npmjs.com/package/@adobe/llmapps-sdk) SDK執行個體：

```javascript
export default async function decorate(block, bridge) {
  // ...
}
```

只有在LLM平台Widget表面內執行時，`bridge`才會出現。 請一律保護您的橋接呼叫，這樣當您的區塊直接在瀏覽器或本機開發伺服器中預覽時，也會轉譯。

### 從動作結果轉譯資料

`bridge.toolResult`是Promise，會以您的處理常式傳回的完整結果解析，包括`structuredContent`。

```javascript
const SAMPLE_PRODUCTS = [
  { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
];

export default async function decorate(block, bridge) {
  let products = SAMPLE_PRODUCTS;

  if (bridge) {
    const result = await bridge.toolResult;
    products = result?.structuredContent?.products ?? [];
  }

  block.innerHTML = products.map(p => `
    <div class="product-card">
      <h3>${p.name}</h3>
      <p class="price">${p.price}</p>
      <button data-id="${p.id}">Tell me more</button>
    </div>
  `).join('');
}
```

### 套用主機主題

在`decorate`中及早呼叫`bridge.applyHostStyles()`，將主機的CSS變數和字型（淺色/深色主題、印刷樣式）插入Widget。 這可讓您的Widget視覺上與周圍的LLM平台UI保持一致。

```javascript
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }
  // ...
}
```

在執行階段對主題變更做出反應（例如，當使用者在淺色和深色模式之間切換時）：

```javascript
if (bridge) {
  bridge.onContextChange(ctx => {
    block.dataset.theme = ctx.theme; // 'light' | 'dark'
  });
}
```

### 傳送後續追蹤訊息

`bridge.sendMessage(text)`將使用者訊息插入交談中。 這是Widget觸發進一步AI互動的主要方式，例如，當使用者按一下產品卡詢問詳細資訊時。

```javascript
block.querySelectorAll('button[data-id]').forEach(btn => {
  btn.addEventListener('click', () => {
    bridge.sendMessage(`Show me details for product ${btn.dataset.id}`);
  });
});
```

### 直接呼叫另一個動作

`bridge.callTool(name, args)`會從Widget內叫用另一個動作，而不需檢視使用者訊息。 用於隨選載入相關資料。

```javascript
btn.addEventListener('click', async () => {
  const result = await bridge.callTool('get-product-details', { id: product.id });
  renderDetails(result.structuredContent);
});
```

### 自動調整Widget大小

LLM平台會根據您報告的內容調整介面工具集的大小。 使用`bridge.autoResize(element)`可讓您在內容變更時保持Widget高度同步 — 它在內部使用`ResizeObserver`。 在初始轉譯後呼叫它：

```javascript
export default async function decorate(block, bridge) {
  // ... render content ...

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

或手動報告固定大小：

```javascript
bridge.reportSize(block.offsetWidth, block.offsetHeight);
```

### 預覽模式和本機開發

直接在瀏覽器或本機開發伺服器上預覽EDS頁面時，`bridge`是`undefined`。 使用上述範例資料遞補模式，讓您的區塊立即呈現，而不需要即時處理常式。

若要啟動本機開發伺服器：

```bash
npm install -g @adobe/aem-cli
aem up
```

這會開啟`http://localhost:3000`，您可在此導覽至您的Widget頁面，並檢視使用範例資料呈現的區塊。 封鎖JS和CSS的變更會立即反映出來。

## 後續步驟

- [指南：撰寫動作處理常式](/help/guides/write-action-handler.md)

