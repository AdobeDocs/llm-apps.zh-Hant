---
title: 本機處理常式開發與測試
description: Adobe LLM應用程式的處理常式專案結構、本機伺服器命令、MCP測試和單元測試。
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '280'
ht-degree: 1%

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

## 專案結構

您的連結存放庫會遵循此配置：

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

- **`entry.js`**&#x200B;是webpack進入點。 在建置時，它會發現每個`actions/*/index.js`檔案，並將它們整合到單一`dist/index.js`中。 請勿修改。
- **`actions.json`**&#x200B;已授權。 部署管道會自動從[!DNL LLM Apps]中的動作中繼資料寫入它。
- **測試**&#x200B;在`test/actions/`下存放，**不在`actions/`內**。 Webpack將`actions/`底下的所有專案整合到已部署的成品中 — 共同定位測試會將它們傳送到[!DNL Adobe I/O Runtime]。

## 本機開發

您可在沒有Adobe憑證的情況下在本機開發和測試處理常式：

```bash
npm install
npm run dev:local
```

這會使用webpack建置專案，並在`http://localhost:9080`上啟動純Node.js HTTP伺服器。 伺服器會在`actions/`下自動發現您的處理常式檔案，並將它們註冊為MCP工具。

### 本機中繼資料行為

目前的UI不提供`actions.json`下載。 您可以在不使用此檔案的情況下執行本機伺服器；它會在`actions/`下探索處理常式，並以最少的中繼資料進行註冊。

如果沒有`actions.json`，本機動作引數將不會針對UI輸入結構描述進行驗證。 單位和整合測試使用`test/fixtures/actions.json`作為代表性中繼資料。

### 使用curl測試

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action
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

處理常式單元測試在`test/actions/`下存放，並反映`actions/`配置：

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
npm test                                      # all tests
npx jest test/actions/echo                   # one action only
```

本機測試通過後，推送變更並遵循[部署變更](/help/guides/deploy-your-app.md)。

