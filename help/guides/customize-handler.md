---
title: 自訂產生的動作處理常式
description: 瞭解Adobe LLM應用程式處理常式合約、取代產生的範例資料，並讓處理常式輸出符合其Widget。
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 0%

---


# 自訂產生的處理常式 {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

入門代理程式會為每個產生的動作建立工作處理常式。 處理常式一開始會傳回範例資料，讓您測試完整的體驗。

使用本指南瞭解處理常式合約，並將範例資料取代為API或資料來源。

**歷程：**&#x200B;尋找產生的處理常式→瞭解其輸入和結果，→連線您的系統→使Widget合約在測試和部署→保持一致。

## 尋找產生的處理常式

開啟上線期間選取的處理常式存放庫：

```text
actions/
└── <action-name>/
    └── index.js
```

相符的測試會單獨儲存：

```text
test/
└── actions/
    └── <action-name>.test.js
```

編輯產生的`index.js`。 請勿變更執行階段檔案，例如`entry.js`。

## 處理常式合約

每個處理常式會匯出一個非同步函式：

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

函式接收`args`物件並傳回結果物件。

### 輸入： `args`

`args`包含為[!DNL LLM Apps]中的動作定義的引數。

針對具有`category`和`query`引數的動作：

```javascript
module.exports = async ({ category = '', query = '' } = {}) => {
  // Use the validated action arguments.
};
```

執行階段會在動作中繼資料包含`inputSchema`時驗證輸入結構描述，就像部署後一樣。 沒有`actions.json`的本機處理常式探索不會套用結構描述驗證。 處理常式應一律強制執行商業規則，例如支援的值、最大長度以及允許的組合。

### 輸出： `content`

一律傳回`content`。 這是由LLM平台及不顯示Widget的主機所讀取的內容部分陣列。

```javascript
content: [
  {
    type: 'text',
    text: 'Found 3 products matching your search.'
  }
]
```

請保持此回應簡潔。 不要包含認證、內部錯誤或使用者無權檢視的資料。

### 輸出： `structuredContent`

動作有Widget時傳回`structuredContent`。 它必須是純物件，而非裸陣列。

```javascript
structuredContent: {
  products: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ],
  total: 1
}
```

`structuredContent`會傳送至Widget，而非LLM。 僅傳回介面所需的欄位。

若為純文字動作，可省略`structuredContent`。

## 處理常式 — Widget合約

處理常式和Widget共用一個合約： `structuredContent`的形狀。

```text
Action arguments
      ↓
Handler
      ├── content → LLM text response
      └── structuredContent → Widget
                                  ↓
                           bridge.toolResult
```

Widget會從LLM應用程式SDK Bridge讀取處理常式結果：

```javascript
export default async function decorate(block, bridge) {
  const result = await bridge.toolResult;
  const products = result?.structuredContent?.products ?? [];

  // Render products.
}
```

如果處理常式傳回：

```javascript
structuredContent: {
  products: [...],
  total: 3
}
```

Widget必須讀取`structuredContent.products`和`structuredContent.total`。

變更欄位名稱或型別可能會中斷Widget。 一起更新處理常式、Widget和測試。

## 取代範例資料

產生的處理常式通常包含記憶體中的範例陣列。 以伺服器端呼叫您的系統來取代該資料查詢。

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

  const products = payload.products.map(({ id, name, price }) => ({
    id,
    name,
    price
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

在處理常式中保留受保護的網路存取。 切勿將API憑證放入Widget JavaScript或原始檔控制中。

## 處理預期狀態

保留每個結果的可預測輸出圖形。

### 找到個結果

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { products: [...], total: 3 }
}
```

### 沒有結果

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { products: [], total: 0 }
}
```

Widget現在可以呈現空白狀態，而不需猜測`products`是否存在。

對於服務失敗，會在不公開棧疊追蹤、權杖、內部主機或上游回應主體的情況下傳回或擲回安全錯誤。

## 測試合約

每當處理常式變更時，請更新產生的測試。 封面:

- 有效且無效的引數。
- 「結果」和「無結果」狀態。
- API失敗和逾時。
- API回應的格式錯誤。
- `content`永遠存在。
- `structuredContent`是純物件。
- Widget預期的形狀。

執行：

```bash
npm test
```

如需本機MCP測試，請參閱[本機處理常式開發與測試](/help/reference/development.md)。

## 部署變更

1. 認可並推送處理常式變更。
2. 如果資料形狀已變更，請更新並推送Widget。
3. [將應用程式](/help/guides/deploy-your-app.md)部署到Stage。
4. [測試ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)。
5. 中繼成功後，請部署至生產環境。

接下來，請參閱[自訂產生的Widget](/help/guides/widgets.md)。
