---
title: 撰寫動作處理常式
description: 瞭解如何為您的Adobe LLM應用程式編寫動作處理常式，包括處理常式合約、structuredContent和使用範例。
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '714'
ht-degree: 0%

---


# 撰寫動作處理常式

>[!IMPORTANT]
>
>**免責宣告：**&#x200B;這是[!DNL LLM Apps]的測試版本。 這裡顯示的功能、工作流程和UI不一定代表應用程式或產品的最終狀態。

在UI中建立動作後，中繼資料會儲存在[!DNL LLM Apps] API中 — 但尚未有程式碼在其後。 本指南會逐步引導您撰寫處理常式函式，當LLM平台（例如[!DNL ChatGPT]或Claude）叫用您的動作時，該函式會執行。

如需專案版面配置、本機開發及測試詳細資訊，請參閱[開發](/help/reference/development.md)。

## 開發人員合約

您只會撰寫處理常式。 其他所有專案 — 動作名稱、說明、輸入結構描述、註解、Widget可見度、許可權、CSP — 都存在於[!DNL LLM Apps] UI中，並在部署時自動傳送到執行階段。 您絕對不會在存放庫中手動編輯中繼資料，也絕對不會在程式碼中註冊工具。

| 關注 | 其所在位置 |
|---------|----------------|
| 中繼資料（名稱、說明、結構、Widget設定） | [!DNL LLM Apps] UI — 儲存在API中 |
| 處理常式程式碼（執行的函式） | 您的[!DNL GitHub]存放庫 — `actions/<name>/index.js` |
| `actions.json` （中繼資料快照） | 由部署管道撰寫；從UI下載以供本機開發 |

## 快速入門

連結的存放庫需要專案結構才能編寫處理常式。 複製&#x200B;**[Adobe LLM Apps範本](https://github.com/Adobe-AIFoundations/llm-apps-boilerplate)**，以空的起點開始。

將內容推播到您在建立應用程式期間連結的存放庫（例如，`your-org/your-repo`）。

程式碼就緒後，執行：

```bash
npm install
```

這會安裝所有相依性，包括[`@adobe/llm-apps-runtime`](https://www.npmjs.com/package/@adobe/llm-apps-runtime) — 處理MCP通訊協定通訊、動作探索及要求路由的執行階段。 您不會直接與執行階段互動；它會在建置時由`entry.js`使用。

>[!TIP]
>
>如果您使用[克勞德程式碼](https://claude.ai/code)或[游標](https://cursor.com)，樣板會在`.claude/skills/llm-apps-action-author/`加入現成的克勞德技能。 它可以架構新動作、產生測試檔案、驗證處理常式形狀，並引導您完成處理常式合約 — 所有這些都是從您的編輯器完成。 若要使用它，請要求Claude *「新增稱為search-products的動作」*，它會自動遵循正確的專案慣例。

## 處理常式合約

處理常式是位於`actions/<name>/index.js`的單一檔案，可匯出單一非同步函式：

```javascript
module.exports = async (args) => {
  return {
    content: [{ type: 'text', text: 'response for the LLM' }],
    structuredContent: { /* data for the widget */ }
  }
}
```

函式會以純物件接收動作的輸入引數 — 這些是您在「建立動作」對話方塊中定義的引數。 伺服器會在呼叫您的處理常式之前，根據輸入結構描述來驗證它們。

### `content` （必要）

傳送至LLM和純文字主機的內容部分陣列。 這是LLM平台讀取的內容，以制定回應。

```javascript
content: [
  { type: 'text', text: 'Found 5 products matching category "bagged-coffee".' }
]
```

一律傳回`content` — 這是任何主機的通用後援。

### `structuredContent`

傳送至Widget的純JavaScript物件。 此資料具有&#x200B;**零權杖成本** — 它由EDS Widget區塊使用，以呈現豐富的UI，例如產品輪播或地圖。

```javascript
structuredContent: {
  products: [
    { name: 'Product A', category: 'bagged-coffee', imageUrl: '...' },
    { name: 'Product B', category: 'bagged-coffee', imageUrl: '...' }
  ],
  total: 2,
  category: 'bagged-coffee'
}
```

結構由您決定 — 它必須透過`bridge.toolResult`符合您的EDS Widget區塊的預期。

>[!IMPORTANT]
>
>`structuredContent`必須是純物件，而不是空陣列。

### `_meta` (可選)

與結果一併傳送的其他中繼資料。 `openai/widgetDescription`索引鍵會告訴LLM平台如何呈現Widget：

```javascript
_meta: {
  'openai/widgetDescription': 'The widget displays a scrollable product carousel. '
    + 'Do NOT repeat the product list. Instead, highlight one or two recommendations.'
}
```

## 範例：搜尋產品處理常式

以下是`search-products`處理常式範例。 它會接受選用的`category`篩選器和任意文字的`query`、搜尋產品目錄，並傳回LLM的文字摘要和Widget轉盤的結構化資料。

>[!NOTE]
>
>此範例使用硬式編碼的產品陣列來簡化。 在真實的應用程式中，您通常會呼叫自己的產品API或資料庫，以動態擷取結果。

```javascript
// actions/search-products/index.js

const PRODUCTS = [
  {
    name: 'Product A',
    description: 'A short description of Product A.',
    category: 'bagged-coffee',
    sub_category: 'dark-roast',
    image_url: 'https://www.example.com/products/product-a/hero.jpg',
    url: 'https://www.example.com/products/product-a',
    productId: 'PROD-001',
    rating: 4.7,
    reviewCount: 58
  },
  // ... more products
];

const WIDGET_DESCRIPTION = 'The widget displays a scrollable product carousel '
  + 'with images, star ratings, and review counts. Do NOT repeat the product list.';

module.exports = async ({ category = '', query = '' } = {}) => {
  let results = PRODUCTS;

  if (category) {
    const categoryLower = category.toLowerCase();
    results = results.filter((p) =>
      p.category.toLowerCase().includes(categoryLower)
      || p.sub_category.toLowerCase().includes(categoryLower)
    );
  }

  if (query) {
    const queryLower = query.toLowerCase();
    results = results.filter((p) =>
      p.name.toLowerCase().includes(queryLower)
      || p.description.toLowerCase().includes(queryLower)
    );
  }

  const products = results.map((p) => ({
    productId: p.productId,
    name: p.name,
    shortDescription: p.description,
    category: p.category,
    rating: p.rating,
    reviewCount: p.reviewCount,
    imageUrl: p.image_url,
    productUrl: p.url,
  }));

  if (products.length === 0) {
    return {
      content: [{ type: 'text', text: `No products found for "${category}".` }],
      structuredContent: { products: [], total: 0, category: null },
      _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
    };
  }

  return {
    content: [
      { type: 'text', text: `Found ${products.length} product(s) in "${category}".` }
    ],
    structuredContent: { products, total: products.length, category },
    _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
  };
};
```

**執行階段發生的情況：**

1. 使用者要求LLM平台&#x200B;*「顯示您的咖啡產品」。*
2. LLM平台符合&#x200B;*搜尋產品*&#x200B;的意圖，並擷取`category`。
3. MCP伺服器使用`{ category: 'bagged-coffee' }`呼叫您的處理常式。
4. 您的處理常式會篩選目錄並傳回`content` （LLM的文字摘要） + `structuredContent` （Widget的產品陣列）。
5. LLM平台會顯示文字回應，並將結構化資料傳遞至EDS Widget，以轉譯產品輪播。

## 如果缺少處理常式，該怎麼辦？

如果您在UI中定義了動作，但尚未建立處理常式檔案，該動作仍會在部署時註冊。 叫用作業會使用傳回空白內容的預設虛設常式處理常式，直到您新增實際程式碼為止。 這表示您可以先在UI中定義所有動作，並以漸進方式實作。

