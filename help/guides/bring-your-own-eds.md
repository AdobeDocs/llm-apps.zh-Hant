---
title: 自備Edge Delivery Services專案
description: 將現有的Adobe Edge Delivery Services專案連線至Adobe LLM Apps動作。
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '472'
ht-degree: 2%

---


# 自備EDS專案 {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

如果您已有Edge Delivery Services (EDS)專案，或您建立應用程式而未自動建立，請使用此指南。

如果平台自動建立您的Widget，請改為遵循[自訂產生的Widget](/help/guides/widgets.md)。 產生的專案已包含此處說明的SDK檔案、區塊、內容和動作設定。

**歷程：**&#x200B;準備EDS專案→安裝SDK →建置並發佈區塊→設定動作→部署和測試。

## 開始之前

您需要：

- 已安裝[AEM程式碼同步](https://github.com/apps/aem-code-sync)的EDS存放庫。
- 在該存放庫中新增相依性和建立區塊的許可權。
- 為EDS網站設定回應標頭的許可權。
- [!DNL LLM Apps]中的動作，具有傳回`structuredContent`的處理常式。

## 安裝LLM應用程式SDK

從EDS專案根目錄：

```bash
npm install @adobe/llmapps-sdk
```

此套件會將Widget入口點和橋接實作複製到專案中：

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

動作使用的指令碼URL指向`scripts/aem-embed.js`。

## 建立Widget區塊

建立動作的區塊：

```text
blocks/
└── search-products/
    ├── search-products.js
    └── search-products.css
```

匯出標準EDS `decorate`函式，並將連線橋接器匯出為其第二個引數：

```javascript
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }

  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];

  const list = document.createElement('ul');
  products.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product.name ?? 'Product');
    list.append(item);
  });

  block.replaceChildren(list);

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

使用可對文字值進行編碼的DOM API。 請勿將外部資料串連至HTML。

## 製作及發佈Widget頁面

為Widget建立一個EDS頁面，並將區塊新增至該頁面。 發佈頁面。

即時頁面URL會變成動作的Widget URL：

```text
https://main--<repo>--<owner>.aem.live/<widget-page>
```

頁面路徑不需要符合動作名稱，但一致的慣例可讓專案更容易維護。

## 設定CORS

此Widget會載入EDS頁面，以及各原始版本的指令碼、樣式、區塊和媒體。 設定EDS網站的標題：

```json
{
  "/**": [
    {
      "key": "access-control-allow-origin",
      "value": "<allowed-host-origin>"
    }
  ]
}
```

使用您支援的LLM平台所需的特定主機來源。 只有當該Widget刻意為公開、不使用認證的跨原始碼請求且您的安全性要求允許時，才使用`*`。

如需EDS組態詳細資訊，請參閱[組態服務](https://aem.live/docs/config-service-setup)。

## 設定動作

在[!DNL LLM Apps]中，開啟動作並選取&#x200B;**[!UICONTROL Widget中繼資料]**。

輸入：

- **[!UICONTROL 指令碼URL]**

  ```text
  https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js
  ```

- **[!UICONTROL Widget URL]**

  ```text
  https://main--<repo>--<owner>.aem.live/<widget-page>
  ```

使用最低許可權設定CSP網域和瀏覽器許可權。 僅新增Widget所需的原始項和功能。

如需欄位定義，請參閱[動作和Widget欄位](/help/reference/reference-docs.md)。

## 測試整合

1. 直接預覽EDS頁面並驗證其範例資料遞補。
2. 在本機測試處理常式，並將其`structuredContent`與區塊預期的形狀進行比較。
3. 將應用程式部署至測試環境。
4. 從[!DNL ChatGPT]叫用動作。
5. 驗證載入、成功、空白和錯誤狀態。

如果頁面直接運作但無法在LLM平台中運作，請檢查CORS、CSP、HTTPS URL和`structuredContent`圖形。 請參閱[疑難排解](/help/reference/troubleshooting.md)。
