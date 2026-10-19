---
title: 自訂產生的動作處理常式
description: 瞭解Adobe LLM應用程式處理常式合約、取代產生的範例資料，並讓處理常式輸出符合其Widget。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '880'
ht-degree: 0%
---

# 自訂產生的處理常式 {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

平台會為每個產生的動作建立工作處理常式。 處理常式一開始會傳回範例資料，以測試完整的體驗。

**在本指南中：**&#x200B;瞭解如何尋找產生的處理常式、以呼叫您的系統取代其範例資料，以及保持其回應與Widget相容。 然後測試和部署您的變更。

## 尋找產生的處理常式

開啟在[基礎結構設定](/help/guides/create-app.md)期間連線的處理常式存放庫。 如果您使用Adobe受管基礎架構，請先請求程式碼存取權，然後再繼續。

```text
actions/
└── <action-slug>/
    ├── index.js
    └── data.json
```

相符的測試位於不同的目錄中：

```text
test/
└── actions/
    └── <action-slug>.test.js
```

使用產生的提取請求來找出確切的檔案。 資料夾概要(Slug)使用連字型大小：平台會在`actions/search-products/`下產生代碼識別碼為`search_products`的動作。 執行階段會辨識斷字表單以及完全符合識別碼的資料夾。

編輯產生的`index.js`和任何關聯的範例資料。 請勿變更執行階段檔案，例如`entry.js`。

## 處理常式合約

每個處理常式會匯出一個非同步函式：

```javascript
module.exports = async (args, extra) => {
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

針對具有輸入結構描述的動作，函式會接收`args`物件與選擇性執行階段內容`extra`，並傳回結果物件。 針對執行階段協助程式（例如`getVariable(extra, 'PRODUCT_API_ORIGIN')`）使用`extra`；應用程式變數不會透過`process.env`自動公開。 請參閱[設定應用程式變數和密碼](/help/guides/app-variables.md)。

### 輸入： `args`

`args`包含為[!DNL LLM Apps]中的動作定義的引數。

針對具有`category`和`query`引數的動作：

```javascript
module.exports = async ({ category = '', query = '' } = {}, extra) => {
  // Validate business rules before using the arguments.
};
```

透過動作中繼資料，執行階段會將宣告的輸入結構描述轉換為支援規則的驗證，包括必要欄位、基本型別、列舉和陣列專案。 執行階段未執行完整的JSON結構描述驗證。 檢查處理常式中的限制，例如字串長度、數值範圍和巢狀物件屬性。

沒有結構描述中繼資料的本機探索不等於部署：執行階段不會以一般`(args, extra)`形式傳遞動作引數。 使用直接處理常式測試或代表性本機`actions.json`中繼資料來測試引數。 請參閱[本機處理常式開發與測試](/help/reference/development.md)。

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

保持簡明的文字回應。 不要包含認證、內部錯誤或使用者無權檢視的資料。

### 輸出： `structuredContent`

動作有Widget時傳回`structuredContent`。 它必須是純物件，而非裸陣列。

```javascript
structuredContent: {
  arguments: { query: 'coffee' },
  records: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ]
}
```

`structuredContent`是傳遞給主機的MCP動作結果的一部分。 請勿假設模型或使用者看不到它。 僅傳回體驗、永不認證或私人伺服器設定所需的授權資料。

對於純文字動作，傳回`structuredContent`為選用。

## 處理常式 — Widget合約

處理常式和Widget共用一個合約： `structuredContent`的形狀。

| 處理常式輸出 | 用途 |
|---|---|
| `content` | 模型可讀回應和僅限文字的遞補 |
| `structuredContent` | 資料已傳遞至主機，並由Widget透過`bridge.toolResult`讀取 |

Widget會從LLM應用程式SDK Bridge讀取處理常式結果：

```javascript
export default async function decorate(block, bridge) {
  if (!bridge?.isConnected || bridge.hostContext?.preview === true) {
    // Render the block's local preview data instead.
    return;
  }
  const result = await bridge.toolResult;
  const records = result?.structuredContent?.records ?? [];

  // Render records.
}
```

目前產生的處理常式使用包含`arguments`和`records`的物件。 現有的應用程式可以使用不同的形狀，因此請在編輯之前檢查您產生的檔案。 變更欄位名稱或型別可能會中斷Widget。 一起更新處理常式、Widget和測試。

## 取代範例資料

產生的處理常式會從`data.json`載入代表性記錄。 以伺服器端呼叫您的系統來取代範例資料查詢。 以下範例使用公用HTTPS目錄API，並從應用程式變數中讀取其非敏感來源。 將回應驗證調整至您的API。

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { arguments: {}, records: [] },
      isError: true
    };
  }

  const knownArguments = { query };
  try {
    const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
    if (!apiOrigin) {
      throw new Error('Missing configuration');
    }
    const origin = new URL(apiOrigin);
    if (origin.protocol !== 'https:' || origin.username || origin.password) {
      throw new Error('Invalid API origin');
    }
    const url = new URL('/v1/products', origin);
    url.searchParams.set('query', normalizedQuery);

    const response = await fetch(url, {
      signal: AbortSignal.timeout(8000)
    });
    if (!response.ok) {
      throw new Error('Request failed');
    }
    const payload = await response.json();
    if (!payload || !Array.isArray(payload.products)
        || !payload.products.every((product) =>
          product
          && typeof product.id === 'string'
          && typeof product.name === 'string'
          && typeof product.price === 'string')) {
      throw new Error('Unexpected response');
    }

    const records = payload.products.map(({ id, name, price }) => ({
      id,
      name,
      price
    }));

    return {
      content: [
        { type: 'text', text: `Found ${records.length} matching products.` }
      ],
      structuredContent: { arguments: knownArguments, records }
    };
  } catch {
    return {
      content: [{ type: 'text', text: 'Product search is unavailable. Try again later.' }],
      structuredContent: { arguments: knownArguments, records: [] },
      isError: true
    };
  }
};
```

在處理常式中保留受保護的網路存取。 切勿將API憑證放入Widget JavaScript或原始檔控制中。 若是代表登入使用者提出的要求，請參閱[驗證一般使用者](/help/guides/authentication.md)。

如需您的處理常式所需的非敏感設定（例如服務URL），請參閱[設定應用程式變數和密碼](/help/guides/app-variables.md)。 分別為每個環境設定變數。 變數變更會在下次部署時生效。 目前的UI僅支援新增變數；請勿使用變數來儲存API認證。

## 處理預期狀態

保留每個結果的可預測輸出圖形。

### 找到個結果

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [...] }
}
```

### 沒有結果

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [] }
}
```

可預測的結果圖形可讓Widget呈現空白狀態，而不需猜測`records`是否存在。

對於服務失敗，傳回包含`isError: true`的安全錯誤結果，如範例所示。 檢查Widget中的`result.isError`，以便將失敗的要求顯示為成功的空白搜尋。 請勿公開棧疊追蹤、認證、內部主機或上游回應主體。

## 測試合約

每當處理常式變更時，請更新產生的測試。 封面:

- 有效且無效的引數。
- 「結果」和「無結果」狀態。
- API失敗和逾時。
- API回應的格式錯誤。
- 每個結果中都有`content`存在。
- `structuredContent`的純物件。
- Widget預期的形狀。

執行存放庫已設定的測試套件：

```bash
npm test
```

如果產生的測試匯入`node:test`而非Jest，請使用`node --test test/actions/<action-slug>.test.js`執行它，並驗證其匯入路徑是否從`test/actions/`解析。 使用適合檔案的測試執行器；樣版的`npm test`命令會執行Jest。

如需本機MCP測試，請參閱[本機處理常式開發與測試](/help/reference/development.md)。

## 部署變更

1. 認可並推送處理常式變更。
2. 如果您變更資料形狀，請更新並推送Widget。
3. [將應用程式](/help/guides/deploy-your-app.md)部署到Stage。
4. 在支援的LLM平台中測試。 檢視ChatGPT中的[測試](/help/guides/test-in-chatgpt.md)或Claude中的[測試](/help/guides/test-in-claude.md)。
5. 在測試通過中繼後，部署至生產環境。

接下來，請參閱[自訂產生的Widget](/help/guides/widgets.md)。
