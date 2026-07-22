---
title: 建立動作
description: 瞭解如何在LLM應用程式UI中定義動作，包括中繼資料、輸入引數和Widget設定。
source-git-commit: ae2748319b5401555c3a616971f5697c17e74ac3
workflow-type: tm+mt
source-wordcount: '900'
ht-degree: 1%

---


# 建立動作

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

本指南會逐步引導您定義[!DNL LLM Apps] UI中的動作。 如需動作及其運作方式的背景資訊，請參閱[核心概念](/help/overview/overview.md#actions)。

## 開啟「動作」頁面

導覽至左側邊欄中的&#x200B;**[!UICONTROL 動作]**，或按一下「應用程式詳細資料」頁面上的&#x200B;**前往動作**。 如果尚未有任何動作，頁面會顯示空白狀態。

![動作頁面 — 尚未有動作](/help/assets/guide-create-action/actions-empty.png)

按一下&#x200B;**+建立動作**&#x200B;以開啟全熒幕對話方塊。

## 動作卡

每個動作都會顯示為一個卡片，其中顯示：

- 動作&#x200B;**名稱**&#x200B;和&#x200B;**描述**
- **介面工具集預覽影像** — 自動產生自介面工具集，顯示LLM平台內的動作輸出外觀
- **徽章**： Widget型別(**[!UICONTROL EDS]**)、部署狀態（**未部署**、**已部署到中繼環境**、**已部署到生產環境**）、上次部署後修改動作時未部署&#x200B;**變更，以及引數計數**
- **可見度**&#x200B;切換 — 啟用或停用即時端點上的動作，而不重新部署
- 右上角的&#x200B;**檢閱**&#x200B;連結可開啟動作編輯器

![動作頁面 — 動作卡片](/help/assets/guide-create-action/action-card.png)

自上次部署後，當一或多個動作已修改時，「動作」頁面頂端會出現「需要部署&#x200B;**」**&#x200B;橫幅。 重新部署應用程式以套用變更。

## 動作標籤

此對話方塊有兩個標籤： **動作**&#x200B;和&#x200B;**[!UICONTROL Widget中繼資料]**。

### 基本資訊

![建立動作 — 基本資訊](/help/assets/guide-create-action/action-basic-info.png)

- **動作名稱** （必要） — 您動作的識別碼（例如，*搜尋產品*）。
- **描述** （必要） — 動作功能的明確說明。 LLM平台會使用此專案來決定何時叫用您的動作。 例如： *依關鍵字搜尋產品目錄。 傳回與名稱、類別、影像和價格相符的產品。*
- **註解** — 描述動作行為的可選提示：

  | 註解 | 說明 |
  |-----------|-------------|
  | **破壞性提示** | 動作會修改或刪除資料 |
  | **等冪** | 使用相同的引數多次呼叫動作會產生相同的結果 |
  | **開放世界提示** | 動作會與外部系統互動 |
  | **唯讀提示** | 動作只會讀取資料，不會寫入 |

  如需詳細資訊，請參閱[參考：中繼資料欄位](/help/reference/reference-docs.md)。

### OpenAI中繼資料

- **叫用狀態文字** — 動作執行時顯示在LLM平台中的訊息（最多64個字元）。 範例： *正在載入產品……*
- **叫用的狀態文字** — 動作完成後顯示的訊息（最多64個字元）。 範例： *載入的產品。*

### 可見度和輸入引數

**可見度**&#x200B;控制動作可用位置：

- **公開給AI模型** — AI模型可叫用動作。
- **在應用程式表面中顯示為Widget** — 動作會呈現視覺化Widget。

**輸入引數**&#x200B;是LLM平台傳送給您的處理常式的值。 模型會自動從使用者的訊息中擷取它們。 針對&#x200B;*搜尋產品*，我們定義：

- **類別** （字串，選擇性） — 用於縮小結果的類別篩選器（例如，產品型別或部門）。
- **查詢** （字串，選擇性） — 任意文字搜尋字詞。

每個引數都有&#x200B;**Name**、**Type** (String， Number， Integer， Boolean)、**Description**&#x200B;和&#x200B;**Required**&#x200B;核取方塊。 按一下「**+新增**」以新增更多引數。

如需詳細資訊，請參閱[參考：動作引數](/help/reference/reference-docs.md)。

### 分析

![建立動作 — analytics使用者意圖](/help/assets/guide-create-action/action-analytics-user-intent.png)

- **使用者意圖** — 啟用時，會要求[!DNL ChatGPT]總結導致呼叫此動作的交談。 該摘要會收集並出現在Analytics中，為您提供insight在動作觸發時使用者嘗試完成的動作。

## Widget中繼資料標籤

此索引標籤會設定在LLM平台中呈現動作視覺回應的方式。 如需Widget運作方式的完整說明，請參閱[指南：設定Widget (EDS)](/help/guides/widgets.md)。

![建立動作 — Widget中繼資料](/help/assets/guide-create-action/widget-metadata.png)

### Widget資訊

- **型別** — Widget技術（目前為&#x200B;**[!UICONTROL EDS]**）。
- **Widget網域（沙箱來源）** — 託管Widget的來源。 應用程式提交至OpenAI的必要條件；每個應用程式必須是唯一的。
- **偏好使用邊框** — 將介面卡中的Widget轉譯。

### 範本URL

- **[!UICONTROL 指令碼URL]** — 啟動程式Widget並在所有動作中共用的進入點：
  `https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js`
- **Widget內嵌URL** — 此特定動作的EDS頁面：
  `https://main--<repo>--<owner>.aem.live/eds-widgets/<action-name>`

### 權限

Widget可以存取的硬體和瀏覽器API：

| 權限 | 說明 |
|-----------|-------------|
| **攝影機** | 存取裝置攝影機 |
| **麥克風** | 存取裝置麥克風 |
| **地理位置** | 存取使用者的位置 |
| **剪貼簿** | 讀取或寫入剪貼簿 |

### CSP設定

![建立動作 — 許可權和CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

控制Widget iframe可連絡的外部網域。 每個外部網域都必須明確加入允許清單。

| 指示詞 | 說明 |
|-----------|-------------|
| **資源網域** | 靜態資產的網域 — 影像、字型、指令碼、樣式 |
| **連線網域** | Widget可以透過`fetch`、`XHR`或`WebSocket`聯絡的網域 |
| **框架網域** | 巢狀iframe允許來源；新增專案會觸發OpenAI更嚴格的應用程式檢閱 |
| **重新導向網域** | `openExternal`個重新導向連結的受信任目標（[!DNL ChatGPT]個特定） |
| **基礎URI網域** | `base-uri` CSP指示詞（僅限MCP Apps SDK，不受[!DNL ChatGPT]支援） |

按一下&#x200B;**建立新動作**&#x200B;以儲存。

## 建立動作後

您的動作會在「動作」頁面上以卡片形式顯示：

![動作頁面 — 已建立動作](/help/assets/guide-create-action/actions-with-action.png)

每個卡片都會顯示動作名稱、說明、型別徽章(**[!UICONTROL EDS]**)、部署狀態（**未部署**）以及引數計數。 您可以按一下&#x200B;**...**&#x200B;來編輯或刪除，或按一下&#x200B;**檢閱**&#x200B;來檢查組態。

![應用程式詳細資料 — 未部署](/help/assets/guide-create-action/app-detail-not-deployed.png)

動作中繼資料已儲存，但尚未部署任何程式碼。 若要讓動作正常運作，您必須：

1. **設定EDS Widget** — 請參閱[指南：設定Widget (EDS)](/help/guides/widgets.md)。
2. **寫入處理常式** — 請參閱[指南：寫入動作處理常式](/help/guides/write-action-handler.md)。
3. **[!UICONTROL 部署]** — 請參閱[指南：部署您的應用程式](/help/guides/deploy-your-app.md)。

## 後續步驟

- [指南：設定Widget (EDS)](/help/guides/widgets.md)
