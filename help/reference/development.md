---
title: 適用於Adobe LLM應用程式的開發
description: Adobe LLM應用程式處理常式程式碼的專案結構、本機開發工作流程和測試設定。
source-git-commit: 51ffb31eec82f9639bd7ade9052d61028c262d0e
workflow-type: tm+mt
source-wordcount: '318'
ht-degree: 4%

---


# 開發 {#development}

>[!IMPORTANT]
>
>**免責宣告：**&#x200B;這是[!DNL LLM Apps]的測試版本。 這裡顯示的功能、工作流程和UI不一定代表應用程式或產品的最終狀態。

本節說明處理常式專案結構、本機開發工作流程和測試設定。 如需處理常式合約和範常式式碼，請參閱[撰寫動作處理常式](/help/guides/write-action-handler.md)。

## 專案結構

您的連結存放庫會遵循此配置：

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   ├── search-products/
│   │   └── index.js           # Handler (async function)
│   ├── get-product-details/
│   │   └── index.js
│   └── echo/
│       └── index.js
├── test/
│   ├── actions/
│   │   └── search-products.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — local copy of UI metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

要點：

- **`entry.js`**&#x200B;是webpack進入點。 在建置時，它會發現每個`actions/*/index.js`檔案，並將它們整合到單一`dist/index.js`中。 請勿修改。
- **`actions.json`**&#x200B;已授權。 從UI的「動作」頁面下載，以進行本機開發。 對於部署，管道會自動從API寫入它。
- **測試**&#x200B;在`test/actions/`下存放，**不在`actions/`內**。 Webpack將`actions/`底下的所有專案整合到已部署的成品中 — 共同定位測試會將它們傳送到[!DNL Adobe I/O Runtime]。

## 本機開發

您可在沒有Adobe憑證的情況下在本機開發和測試處理常式：

```bash
npm install
npm run dev:local
```

這會使用webpack建置專案，並在`http://localhost:9080`上啟動純Node.js HTTP伺服器。 伺服器會在`actions/`下自動發現您的處理常式檔案，並將它們註冊為MCP工具。

### 下載`actions.json`

若要讓本機伺服器知道您的動作中繼資料（名稱、說明、輸入結構描述），請從[!DNL LLM Apps] UI的[動作]頁面下載`actions.json`，並將其放在存放庫根目錄。 如果沒有它，伺服器會探索您的處理常式，但使用最少的中繼資料註冊它們。

您也可以複製`actions.example.json`至`actions.json`作為起點。

### 使用curl測試

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the search-products action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search-products","arguments":{"category":"bagged-coffee"}}}'
```

### 使用MCP檢查器測試

```bash
npx @modelcontextprotocol/inspector
```

將&#x200B;**傳輸型別**&#x200B;設定為`streamable-http`並將&#x200B;**URL**&#x200B;設定為`http://localhost:9080`。

## 測試

處理常式單元測試在`test/actions/`下存放，並反映`actions/`配置：

```javascript
// test/actions/search-products.test.js
const handler = require('../../actions/search-products/index.js')

test('returns all products when no filter is given', async () => {
  const result = await handler({})
  expect(result.content[0].text).toContain('product')
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('filters by category', async () => {
  const result = await handler({ category: 'bagged-coffee' })
  expect(result.structuredContent.products.every(
    (p) => p.category === 'bagged-coffee'
  )).toBe(true)
})

test('filters by query', async () => {
  const result = await handler({ query: 'dark-roast' })
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('returns empty result for unknown category', async () => {
  const result = await handler({ category: 'nonexistent' })
  expect(result.structuredContent.products).toHaveLength(0)
})
```

使用下列專案執行測試：

```bash
npm test                                      # all tests
npx jest test/actions/search-products        # one action only
```

## 部署

您不會手動建立或部署。 如需部署管道的完整逐步說明，請參閱[部署您的應用程式](/help/guides/deploy-your-app.md)。

您的日常工作流程為：

| 步驟 | 動作 |
|------|--------|
| &#x200B;1. 寫入或編輯處理常式 | `actions/<name>/index.js` |
| &#x200B;2. 下載中繼資料 | 動作頁面→ **下載動作.json** |
| &#x200B;3. 本機測試 | `npm run dev:local` |
| &#x200B;4. 推送程式碼 | `git push` |
| &#x200B;5. 部署 | **[!UICONTROL 部署]**→應用程式詳細資料頁面 |

