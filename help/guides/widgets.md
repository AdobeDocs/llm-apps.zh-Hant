---
title: 自訂產生的EDS Widget
description: 瞭解並自訂Adobe LLM應用程式上線代理程式建立的Edge Delivery Services Widget。
source-git-commit: 4c259a4587c0a84bb634a9a56c043dfe1cfc31fb
workflow-type: tm+mt
source-wordcount: '650'
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
>本指南假設您已基本熟悉Adobe Edge Delivery Services (EDS)。 如果您是EDS的新手，請先閱讀[EDS開發人員教學課程](https://www.aem.live/developer/tutorial)和[探索區塊](https://www.aem.live/docs/exploring-blocks)以瞭解基本知識（區塊、`decorate`函式和EDS專案結構），然後再自訂Widget。

入門代理程式會為每個產生的動作建立EDS Widget。 Widget已接收動作結果、轉譯範例資料、套用主機樣式，並連結至[!DNL LLM Apps]中的動作。

首先，測試產生的Widget。 然後自訂其資料合約、互動和視覺化設計。

**歷程：**&#x200B;尋找產生的區塊→調整其資料合約→安全地自訂→在本機預覽→進行部署和測試。

## 尋找產生的Widget

開啟您建立應用程式時選取的EDS存放庫。 每個產生的Widget都是一個EDS區塊：

```text
blocks/
└── <action-name>/
    ├── <action-name>.js
    └── <action-name>.css
```

- JavaScript檔案會讀取動作結果並建置介面。
- CSS檔案可控制版面、回應式行為和視覺化設計。
- 產生的提取請求會顯示為該動作建立的精確檔案。

入門代理程式也會設定Widget URL和支援的SDK檔案。 您不需要建立第二個EDS專案或重新輸入這些值，即可自訂產生的Widget。

## LLM應用程式SDK如何連線Widget

`@adobe/llmapps-sdk`套件會將EDS Widget連線到LLM主機。 產生的EDS存放庫包括：

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

您不會匯入區塊中的SDK。 已自動提供連線的`bridge`。 它可讓介面工具集：

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
  content: [{ type: 'text', text: `Found ${products.length} products.` }],
  structuredContent: { products, total: products.length }
};
```

```javascript
// EDS block
export default async function decorate(block, bridge) {
  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];
  // Render products.
}
```

當您變更`structuredContent`時，請一併更新處理常式和Widget。 請參閱[自訂產生的處理常式](/help/guides/customize-handler.md)，以取得完整的傳回合約。

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
  button.addEventListener('click', () => {
    if (bridge && product.id) {
      bridge.sendMessage(`Show me details for product ${String(product.id)}`);
    }
  });

  card.append(title, button);
  return card;
}
```

將URL指派給`href`或`src`之前，請先驗證URL，並僅允許體驗所需的通訊協定。

## 使用主機橋接器

EDS將連線的橋接器傳遞至`decorate(block, bridge)`。 保護橋接器呼叫，以便在直接EDS預覽期間也呈現區塊。

### 套用主機樣式

```javascript
if (bridge) {
  bridge.applyHostStyles();
}
```

這會套用主機印刷樣式和主題變數。 您的Widget CSS應同時支援淺色和深色主機主題。

### 傳送後續追蹤訊息

```javascript
await bridge.sendMessage('Show me similar products.');
```

當互動應繼續交談時，請使用`sendMessage`。

### 呼叫其他動作

```javascript
const result = await bridge.callTool('get-product-details', {
  id: product.id
});
```

針對需要其他動作結果的明確互動，請使用`callTool`。 僅傳遞驗證的值並處理失敗，而不會公開內部詳細資訊。

### 保持Widget大小同步

```javascript
if (bridge) {
  bridge.autoResize(block);
}
```

在初始轉譯後呼叫`autoResize`，讓主機可以回應內容變更。

## 預覽您的變更

產生的區塊應包含範例資料，以便在`bridge`無法使用時直接預覽。

若要在本機預覽EDS專案：

```bash
npm install -g @adobe/aem-cli
aem up
```

在`http://localhost:3000`開啟產生的Widget頁面。 驗證：

- 空白、載入、成功和錯誤狀態。
- 長文字和缺少選用欄位。
- 鍵盤導覽和顯示焦點。
- 淺色和深色主題。
- 窄而寬的版面。

然後將應用程式部署到暫存環境，並在LLM平台中使用即時`structuredContent`進行測試。

## 發佈自訂

1. 認可並推播EDS變更。
2. 如果您變更了資料形狀，請認可並推播相符的處理常式變更。
3. 將應用程式部署至測試環境。
4. 在[!DNL ChatGPT]中測試動作和Widget。
5. 將驗證的版本升級至生產環境。

## 其他EDS設定

如果您未使用入門代理程式或想要整合現有的EDS網站，請參閱[自備EDS專案](/help/guides/bring-your-own-eds.md)。
