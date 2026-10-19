---
title: 自備Edge Delivery Services專案
description: 將現有的Adobe Edge Delivery Services專案連線至Adobe LLM Apps動作。
hidefromtoc: true
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '866'
ht-degree: 1%
---

# 自備EDS專案 {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

使用本指南連線相容的現有Edge Delivery Services (EDS)專案，或將Widget新增至您自行建立的動作。 現有專案為選用。 如果您設定自己的基礎結構，預設路徑會以兩個空白存放庫開始，平台會在[基礎結構設定](/help/guides/create-app.md)期間初始化。

如果平台自動建立您的Widget，請改為遵循[自訂產生的Widget](/help/guides/widgets.md)。 產生的專案已包含此處說明的SDK檔案、區塊、內容和動作設定。

**在本指南中：**&#x200B;瞭解如何準備EDS專案、安裝SDK，以及建置和發佈Widget。 然後，將Widget連線至動作，並在您部署的應用程式中測試。

## 開始之前

您需要：

- 與AEM樣板相容的EDS存放庫，已安裝[AEM Code Sync](https://github.com/apps/aem-code-sync)，且有已設定的內容來源。
- 在該存放庫中新增相依性和建立區塊的許可權。
- 為EDS網站設定回應標頭的許可權。
- [!DNL LLM Apps]中的動作，具有傳回`structuredContent`的處理常式。

當您連結現有的存放庫時，平台會檢查`scripts/aem.js`、`blocks/`、`styles/styles.css`和`head.html`。 內嵌也會匯入`scripts/scripts.js`並呼叫其匯出的`decorateMain`函式以準備區塊。 保留EDS區塊載入合約不變。 僅需必要的檔案無法確認大量自訂的專案可正確轉譯。

## 安裝LLM應用程式SDK

從EDS專案根目錄：

```bash
npm install @adobe/llmapps-sdk
```

套件的安裝後掛接會將Widget入口點、橋接實作和支援的Adobe程式庫複製到專案中：

```text
scripts/
├── aem-embed.js
├── llmapps-sdk.js
├── alloy-base.min.js
└── alloy-standalone.min.js
```

動作的指令碼URL指向`scripts/aem-embed.js`，而非`scripts/llmapps-sdk.js`。 確認安裝程式已複製SDK檔案，然後提交並推播它們與專案。 當您安裝或更新SDK時，掛接會覆寫複製的檔案；請勿自訂複製的SDK檔案。

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
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  if (bridge?.isConnected) {
    bridge.applyHostStyles();
  }

  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];

  if (result?.isError) {
    const message = document.createElement('p');
    message.textContent = 'Product search is unavailable. Try again later.';
    block.replaceChildren(message);
    bridge.autoResize(block);
    return;
  }

  const list = document.createElement('ul');
  records.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product?.name ?? 'Product');
    list.append(item);
  });

  if (records.length === 0) {
    const item = document.createElement('li');
    item.textContent = 'No matching products were found.';
    list.append(item);
  }
  block.replaceChildren(list);

  if (bridge?.isConnected) {
    bridge.autoResize(block);
  }
}
```

使用可對文字值進行編碼的DOM API。 請勿將外部資料串連至HTML。

範例需要`structuredContent.records`。 符合處理常式傳回的屬性名稱和記錄欄位。 檢查`bridge.isConnected`，而不只是檢查橋接器是否存在： `toolResult`從未以獨立模式解析。 在LLM應用程式預覽中，當`bridge.hostContext.preview`表示預覽模式時，請使用區塊的範例資料。

## 製作及發佈Widget頁面

為Widget建立一個EDS頁面，並將區塊新增至該頁面。 發佈頁面。

頁面URL會變成動作的Widget URL。 例如，預設產生的配置使用EDS預覽原點：

```text
https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
```

EDS即時來源（例如`https://main--<repo>--<owner>.aem.live`）也會在發佈頁面和資產時運作。 使用實際的網站來源；設定的EDS網站可以有與GitHub存放庫和擁有者不同的網站和組織名稱。

確認將`.plain.html`附加至Widget URL會傳回頁面片段，且該頁面包含`search-products`區塊。 頁面路徑不需要符合動作識別碼，但一致的慣例可讓專案更容易維護。

## 設定CORS

此Widget會載入跨來源的EDS頁面、指令碼、樣式、區塊和媒體。 設定EDS網站的標題：

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

使用受支援LLM平台所需的實際Widget沙箱原點，而不僅僅是聊天網站的原點。 驗證`.plain.html`回應及匯入的JavaScript、樣式、字型和媒體上的CORS。

平台的自動EDS設定會針對公用Widget傳遞設定`access-control-allow-origin: *`，並可取代較窄的現有值。 該原則僅適用於刻意使用公共資源，且沒有經過認證的跨原始碼請求，且您的安全要求允許時。 如果您管理受限制的現有網站，請先檢閱其原則，再將其連線；請勿公開私人網站內容以使Widget運作。

如需EDS組態詳細資訊，請參閱[組態服務](https://aem.live/docs/config-service-setup)。

## 設定動作

在[!DNL LLM Apps]中，開啟動作並選取&#x200B;**[!UICONTROL LLM Widget詳細資料]**。 在&#x200B;**[!UICONTROL Widget資訊]**&#x200B;下，將&#x200B;**[!UICONTROL Widget型別]**&#x200B;設定為&#x200B;**[!UICONTROL EDS]**，然後展開&#x200B;**[!UICONTROL 範本URL]**。

輸入：

- **[!UICONTROL 指令碼URL]**

  ```text
  https://main--<repo>--<owner>.aem.page/scripts/aem-embed.js
  ```

- **[!UICONTROL Widget URL]**

  ```text
  https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
  ```

以您上述驗證的相同工作網站原點和頁面取代範例。 使用最低許可權設定CSP網域和瀏覽器許可權。 EDS來源必須允許用於兩個連線（例如頁面片段擷取）和資源（例如區塊指令碼和樣式）。 只有在Widget要求時才新增其他來源。

選取「**[!UICONTROL 儲存變更]**」，然後重新部署到您正在測試的環境。 儲存的Widget設定在部署前不會更新正在執行的MCP伺服器。

如需欄位定義，請參閱[動作和Widget欄位](/help/reference/reference-docs.md)。

## 測試整合

1. 直接預覽EDS頁面並驗證其範例資料遞補。
2. 在本機測試處理常式，並將其`structuredContent`與區塊預期的形狀進行比較。
3. 將處理常式和動作中繼資料部署到Stage。 EDS程式碼和內容發佈是獨立的步驟。
4. 從支援的LLM平台叫用動作。 檢視ChatGPT中的[測試](/help/guides/test-in-chatgpt.md)或Claude中的[測試](/help/guides/test-in-claude.md)。
5. 驗證載入、成功、空白和錯誤狀態。

如果頁面直接運作但無法在LLM平台中運作，請檢查CORS、CSP、HTTPS URL和`structuredContent`圖形。 請參閱[疑難排解](/help/reference/troubleshooting.md)。
