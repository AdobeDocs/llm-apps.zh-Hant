---
title: 本機處理常式開發與測試
description: Adobe LLM應用程式的處理常式專案結構、本機伺服器命令、MCP測試和單元測試。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '664'
ht-degree: 0%
---

# 本機處理常式開發與測試 {#development}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

在本機開發處理常式時，請使用此參考。 如需處理常式結果合約，請參閱[自訂產生的處理常式](/help/guides/customize-handler.md)。

## 要求

- Node.js 24或更新版本。
- npm.
- 連結的處理常式存放庫的本機複製。

若為Adobe管理基礎結構，請先要求存放庫存取權，再參考本內容。 本機處理常式執行不需要Adobe憑證，但受保護的上游服務仍需要其自己的授權存取權。

## 專案結構

目前的處理常式樣版遵循此配置：

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   └── echo/
│       └── index.js           # Example handler
├── test/
│   ├── actions/
│   │   └── echo.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — optional local metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

要點：

- **`entry.js`**&#x200B;是webpack進入點。 在建置時，它會發現每個`actions/*/index.js`檔案，並將它們整合到單一`dist/index.js`中。 請勿修改進入點。
- **`actions.json`**&#x200B;已授權。 部署管道會自動從[!DNL LLM Apps]中的動作中繼資料寫入它。
- **測試**&#x200B;在`test/actions/`下存放，**不在`actions/`內**。 組建會探索處理常式進入點並整合其匯入；將測試保留在該探索樹之外。
- **動作路徑**&#x200B;使用程式碼識別碼或其連字格式。 例如，`search_products`解析為`actions/search_products/index.js`，或針對產生的程式碼`actions/search-products/index.js`。 顯示名稱不是查詢鍵。
- **處理常式匯入支援的資料** （例如產生的`data.json`）時，會將其整合。

## 本機開發

若要在本機開發和測試沒有Adobe憑證的處理常式，請執行：

```bash
npm install
npm run dev:local
```

開發命令會使用webpack建置專案，並在`http://localhost:9080`上啟動純Node.js HTTP伺服器。 在處理常式或本機中繼資料變更後，停止並重新執行`npm run dev:local`；命令不會監視檔案。

使用不同的連線埠：

```bash
PORT=9081 npm run dev:local
```

### 本機中繼資料行為

目前的UI不提供`actions.json`下載，即使樣板README和執行階段啟動橫幅提及一個。 本機伺服器也會在沒有填入檔案的情況下啟動：當檔案遺失時，組建會建立`{"actions":[]}`，而執行階段會退回處理常式探索。

探索對於列出處理常式很有用，但不是完整的引數測試模式。 如果沒有輸入結構描述，MCP SDK會呼叫具有執行階段內容作為其第一個引數的處理常式，而不是使用一般`(args, extra)`簽章。 SDK不會轉送提供的動作引數。

針對具有引數的MCP呼叫，建立代表性中繼資料符合動作編輯器的&#x200B;**僅限本機** `actions.json`。 對於樣版`echo`處理常式：

```json
{
  "actions": [
    {
      "name": "echo",
      "description": "Echo the supplied message.",
      "inputSchema": {
        "type": "object",
        "properties": {
          "message": { "type": "string" }
        },
        "required": ["message"]
      }
    }
  ]
}
```

當`actions`陣列非空白時，執行階段只會登入陣列中命名的動作。 使用每個動作的程式碼識別碼做為`name`。 即使是沒有引數的動作，也需要有`"properties": {}`的物件結構描述以保留一般處理常式簽章。

本機中繼資料不會更新UI或部署設定。 執行階段會驗證支援的型別、必填欄位、列舉和陣列專案，但不是每個JSON結構描述限制；驗證處理常式中的長度、範圍、巢狀物件和商業規則。 樣板整合測試使用`test/fixtures/actions.json`作為代表性中繼資料。

### 本機應用程式變數

應用程式變數透過`extra.variables`而非`process.env`到達處理常式。 從`@adobe/llm-apps-runtime`使用`getVariable(extra, name)`閱讀。 若要在建立後模擬非敏感設定：

```bash
npm run build
node server/local.js \
  --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' \
  --param GREETING_PREFIX=Howdy
```

提供JSON資訊清單和具名值。 在測試其一般`(args, extra)`合約時，使用輸入結構描述在本機`actions.json`中包含處理常式。 命令列標幟不會從平台載入應用程式設定。 請參閱[設定應用程式變數和密碼](/help/guides/app-variables.md)以取得特定於環境的部署設定。

### 使用curl測試

新增上述本機`echo`中繼資料後，建置並啟動伺服器，然後執行：

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action (returns "Echo: hello")
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"echo","arguments":{"message":"hello"}}}'
```

### 使用MCP檢查器測試

```bash
npx @modelcontextprotocol/inspector
```

將&#x200B;**傳輸型別**&#x200B;設定為`streamable-http`並將&#x200B;**URL**&#x200B;設定為`http://localhost:9080`。

## 測試

處理常式單元測試在`test/actions/`下執行，並直接呼叫匯出的函式，而不使用MCP結構描述驗證：

```javascript
// test/actions/echo.test.js
const handler = require('../../actions/echo/index.js')

test('echoes the message', async () => {
  const result = await handler({ message: 'hello' })
  expect(result.content[0].text).toBe('Echo: hello')
})

test('always returns content parts', async () => {
  const result = await handler({})
  expect(Array.isArray(result.content)).toBe(true)
})
```

使用下列專案執行測試：

```bash
npm test                                 # configured Jest suite
npm test -- --runInBand test/actions/echo.test.js  # one boilerplate action
```

有些產生的測試會使用Node的內建測試執行器，而非Jest。 檢查測試匯入，並使用相符的執行器，例如，匯入`node:test`的檔案`node --test test/actions/search-products.test.js`。 將檔案放置在`test/actions/`下後，驗證產生的相對匯入。

單元測試本身不會驗證中繼資料、MCP驗證、Widget載入或主機行為。 使用代表性中繼資料測試本機端點，然後在支援的LLM平台上測試已部署的應用程式。

本機測試通過後，推送變更並遵循[部署變更](/help/guides/deploy-your-app.md)。
