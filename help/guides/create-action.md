---
title: 從頭開始建立動作
description: 定義動作中繼資料、實作其處理常式、連線EDS Widget、測試它，以及使用Adobe LLM應用程式進行部署。
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '1137'
ht-degree: 0%

---


# 從頭開始建立動作 {#create-action-from-scratch}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

>[!NOTE]
>
>本指南假設您已基本熟悉Adobe Edge Delivery Services (EDS)。 如果您是EDS的新手，請先閱讀[EDS開發人員教學課程](https://www.aem.live/developer/tutorial)和[探索區塊](https://www.aem.live/docs/exploring-blocks)以瞭解基本知識（區塊、`decorate`函式和EDS專案結構），然後再連線Widget。

使用本指南新增平台未建立的功能。 您將在[!DNL LLM Apps]中定義動作、在連結的存放庫中寫入其處理常式、視需要新增Widget、測試並部署它。

**歷程：**&#x200B;規劃動作→建立其中繼資料，→寫入處理常式→連線Widget→在本機測試→部署並測試外掛程式。

針對您的第一個應用程式，從[自動建立您的第一個應用程式](/help/guides/create-app.md)開始。

## 開始之前

您需要：

- 現有的LLM應用程式。
- 連結的處理常式存放庫。
- 存放庫已在本機複製，並已安裝其相依性。
- EDS專案（如果動作顯示Widget）。
- 生產結果的清除API或資料來源。

## 計畫動作

動作應該會執行一個清除的使用者工作。 在開啟UI之前，請定義：

- **意圖** — 使用者嘗試達到的目的。
- **描述** — LLM平台應該何時選取此動作。
- **輸入** — 使用者需要的最少資訊。
- **結果** — 處理常式傳回的文字和結構化資料。
- **行為** — 動作會讀取資料、變更資料或呼叫外部系統。
- **Widget** — 結果是否需要視覺介面。

例如，**搜尋產品**&#x200B;動作可以使用：

```text
Intent: Find products matching a category or search phrase
Inputs:
  category: optional string
  query: optional string
Result:
  content: text summary
  structuredContent: products and total count
Behavior: read-only, idempotent, open-world
Widget: product cards
```

將相關但不同的工作分開。 產品搜尋和產品購買不應是一個動作，因為它們有不同的輸入、風險和確認需求。

## 建立動作中繼資料

開啟應用程式並選取&#x200B;**[!UICONTROL 動作]**，然後選取&#x200B;**[!UICONTROL 建立動作]**。

編輯器包含&#x200B;**[!UICONTROL 動作]**&#x200B;和&#x200B;**[!UICONTROL Widget中繼資料]**&#x200B;標籤。

### 輸入基本資訊

![建立動作 — 基本資訊](/help/assets/guide-create-action/action-basic-info.png)

輸入：

- **[!UICONTROL 動作名稱]** — 簡短的工作名稱，例如&#x200B;*搜尋產品*。
- **[!UICONTROL 描述]** — 說明何時使用動作及其傳回的內容。

有用的說明很具體：

```text
Search the product catalog by category or keyword. Returns matching
products with their names, prices, categories, and image URLs.
```

避免模糊的描述，例如&#x200B;*取得產品資訊*。 LLM平台會使用說明來選擇動作。

### 選取註解

註解會說明動作的行為：

- **破壞性提示** — 動作可以刪除或永久變更資料。
- **等冪（相同的引數=沒有額外的效果）** — 重複相同的要求會有相同的效果。
- **開放世界提示** — 動作與外部系統通訊。
- **唯讀提示** — 動作不會變更資料。

僅選取為真的註解。 例如，產品搜尋通常是唯讀、等冪和開放世界。

### 新增OpenAI中繼資料

輸入動作執行時和完成時顯示的簡短訊息：

```text
Invoking: Searching products...
Invoked: Products found
```

對於含有Widget的動作，請新增&#x200B;**[!UICONTROL Widget描述]**。 這與動作說明不同：

- **動作描述**&#x200B;可協助模型決定何時叫用動作。
- **Widget描述**&#x200B;對應至`_meta["openai/widgetDescription"]`並摘要呈現的元件所顯示的內容，減少重複的旁白。

[!DNL LLM Apps]將此套用為元件中繼資料。 請勿從處理常式傳回。

### 設定可見度

- **[!UICONTROL 公開給AI模型]**&#x200B;可讓模型選取動作。
- **[!UICONTROL 在應用程式表面顯示為Widget]**&#x200B;會顯示設定的Widget。

當動作僅傳回文字時停用Widget可見性。

### 新增輸入引數

為處理常式接受的每個值新增一個引數。 每個引數都需要：

- **名稱** — 處理常式收到的金鑰。
- **型別** — 字串、數字、整數或布林值。
- **描述** — 模型應該如何擷取值。
- **必要** — 動作是否可以在沒有它的情況下執行。

針對&#x200B;**搜尋產品**：

```text
category
  Type: String
  Required: No
  Description: Product category used to narrow the catalog.

query
  Type: String
  Required: No
  Description: Product name or search phrase.
```

使用穩定的引數名稱。 變更名稱也需要變更處理常式及其測試。

### 設定分析

當您想要分析包含導致動作的對話摘要時，啟用&#x200B;**[!UICONTROL 收集使用者意圖]**。

![建立動作 — 使用者意圖分析](/help/assets/guide-create-action/action-analytics-user-intent.png)

如需完整的欄位定義，請參閱[動作和Widget欄位](/help/reference/reference-docs.md)。

## 設定Widget

略過本節，進行純文字動作。

開啟&#x200B;**[!UICONTROL Widget中繼資料]**。

![建立動作 — Widget中繼資料](/help/assets/guide-create-action/widget-metadata.png)

設定：

- **型別** — 選取EDS。
- **Widget網域** — 主控此Widget的EDS來源。
- **偏好使用邊框** — 要求主機中的邊框容器。
- **指令碼URL** — EDS Widget進入點。
- **Widget URL** — 此動作的已發佈EDS頁面。

典型的URL為：

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

僅授予必要的瀏覽器許可權和CSP網域。

![建立動作 — 許可權和CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

如果EDS專案或Widget頁面尚不存在，請完成[自攜EDS專案](/help/guides/bring-your-own-eds.md)，然後返回動作。

## 儲存動作

選取&#x200B;**[!UICONTROL 建立新動作]**。 此動作會顯示在具有&#x200B;**未部署**&#x200B;徽章的「動作」頁面上。

此時，中繼資料已存在，但動作仍需要處理常式。

## 實作處理常式

複製連結的處理常式存放庫並安裝其相依性：

```bash
npm install
```

建立：

```text
actions/
└── search-products/
    └── index.js
```

資料夾名稱必須符合動作編輯器中顯示的動作代碼識別碼。

如需完整的結果合約與處理常式 — Widget關係，請參閱[自訂產生的處理常式](/help/guides/customize-handler.md)。

### 處理常式合約

匯出非同步函式：

```javascript
module.exports = async (args) => {
  return {
    content: [
      { type: 'text', text: 'Response for the LLM platform.' }
    ],
    structuredContent: {
      // Data for the widget.
    }
  };
};
```

處理常式會接收UI中定義的引數。

### 傳回`content`

`content`是LLM平台讀取的文字遞補：

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

一律傳回有用的`content`，即使動作具有Widget亦然。

### 傳回`structuredContent`

`structuredContent`是Widget使用的純物件：

```javascript
structuredContent: {
  products: [
    { id: 'P-100', name: 'Product A', price: '$20' }
  ],
  total: 1
}
```

形狀必須符合EDS區塊從`bridge.toolResult`讀取的內容。

### 連線API

在伺服器端處理常式中保留受保護的API存取權。 從執行階段環境載入設定，並使用固定的HTTPS來源。

```javascript
const API_ORIGIN = process.env.PRODUCT_API_ORIGIN;
const API_TOKEN = process.env.PRODUCT_API_TOKEN;

module.exports = async ({ query = '' } = {}) => {
  const normalizedQuery = String(query).trim();
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  if (!API_ORIGIN || !API_TOKEN) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(API_ORIGIN);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  url.searchParams.set('query', normalizedQuery);

  const response = await fetch(url, {
    headers: { Authorization: `Bearer ${API_TOKEN}` },
    signal: AbortSignal.timeout(8000)
  });

  if (!response.ok) {
    throw new Error('Product service request failed.');
  }

  const payload = await response.json();
  if (!payload || !Array.isArray(payload.products)
      || !payload.products.every((product) =>
        product
        && typeof product.id === 'string'
        && typeof product.name === 'string'
        && typeof product.price === 'string')) {
    throw new Error('Product service returned an unexpected response.');
  }

  const products = payload.products.map((product) => ({
    id: product.id,
    name: product.name,
    price: product.price
  }));

  return {
    content: [
      { type: 'text', text: `Found ${products.length} matching products.` }
    ],
    structuredContent: {
      products,
      total: products.length
    }
  };
};
```

請勿將API認證放入原始程式碼、動作中繼資料、Widget JavaScript、記錄檔或面對使用者的錯誤中。

對於生產程式碼，在將核准的欄位對應到`structuredContent`之前，請先驗證完整的上游回應。

## 新增處理常式測試

建立比對測試：

```text
test/
└── actions/
    └── search-products.test.js
```

至少測試：

- 有效的輸入。
- 輸入遺失或無效。
- 空白的結果。
- API逾時或失敗。
- API資料格式錯誤。
- Widget預期的`structuredContent`形狀。

執行：

```bash
npm test
```

如需專案配置與本機MCP測試，請參閱[本機處理常式開發與測試](/help/reference/development.md)。

## 在本機測試動作

執行：

```bash
npm run dev:local
```

如果沒有本機`actions.json`，伺服器會以最少的中繼資料發現處理常式，而且沒有輸入結構描述驗證。

使用MCP檢查工具或`curl`來：

1. 列出已註冊的動作。
2. 使用代表性引數呼叫新動作。
3. 驗證`content`和`structuredContent`。
4. 測試無效和空白的請求。

## 連線和測試Widget

如果動作有Widget：

1. 讓Widget讀取處理常式的`structuredContent`。
2. 使用安全DOM API （例如`textContent`）轉譯外部值。
3. 新增載入、空白和錯誤狀態。
4. 在本機預覽EDS頁面。
5. 驗證CSP、CORS和Widget URL。

參閱[自備EDS專案](/help/guides/bring-your-own-eds.md)。

## 部署和測試

1. 認可並推送處理常式和Widget變更。
2. [將應用程式](/help/guides/deploy-your-app.md)部署到Stage。
3. [測試ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)。
4. 驗證應該和不應該叫用動作的提示。
5. 中繼成功後，請部署至生產環境。

如果中繼資料不存在相符的處理常式，部署會使用預設的Stub註冊動作。 在讓使用者能夠使用動作之前，請新增處理常式。
- [指南：設定Widget (EDS)](/help/guides/widgets.md)
