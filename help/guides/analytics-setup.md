---
title: 設定Adobe Analytics和Customer Journey Analytics
description: 透過Adobe資料流，將Adobe Experience Platform LLM應用程式事件轉送至您自己的Adobe Analytics報表套裝和Customer Journey Analytics資料檢視。
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '2962'
ht-degree: 2%
---

# 設定Adobe Analytics和Customer Journey Analytics {#analytics-setup}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

將應用程式的事件傳送至[!DNL Adobe Analytics]、[!DNL Customer Journey Analytics]或兩者。 [!DNL LLM Apps]透過您擁有的資料流將事件轉送到[!DNL Adobe Experience Platform] Edge Network。 資料流會將事件路由至您新增的每個服務。

- **[!DNL Adobe Analytics]**&#x200B;會報告LLM平台呼叫的動作、使用者選取的Widget連結，以及每個呼叫背後的目的。
- **[!DNL Customer Journey Analytics]**&#x200B;在您以[!DNL Experience Platform]資料建置的資料檢視中，依個人分析相同事件。

此設定與[!DNL LLM Apps]中的內建分析不同。

**在本指南中：**&#x200B;瞭解[!DNL LLM Apps]在[!DNL Experience Platform]、[!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]中需要哪些設定，以及每個產品畫面的Experience League程式連結。 接下來，請在應用程式中啟用Analytics、部署至Stage，並在部署到生產環境之前驗證資料。

## 設定檢查清單 {#setup-checklist}

針對您使用的服務，完成標籤為&#x200B;**必要**&#x200B;的每個步驟。 跳過的步驟很少顯示錯誤。 相反地，資料並未送達。

| 步驟 | [!DNL Adobe Analytics] | [!DNL Customer Journey Analytics] |
|---|---|---|
| [1. 建立身分名稱空間](#identity-namespace) | 必要 | 必要 |
| [2. 建立結構描述](#schema) | 必要 | 必要 |
| [3. 建立資料集](#dataset) | 不需要 | 必要 |
| [4. 準備報表套裝](#report-suite) | 必要 | 不需要 |
| [5. 建立資料串流](#datastream) | 必要 | 必要 |
| [6. 啟用Analytics並部署到Stage](#enable-analytics) | 必要 | 必要 |
| [7. 建立連線和資料檢視](#cja) | 不需要 | 必要 |
| [8. 驗證資料並部署至生產環境](#verify) | 必要 | 必要 |

在同一[!DNL Experience Platform]沙箱中建立名稱空間、結構描述、資料集和資料流。 在生產環境中使用您計畫報告的沙箱和資料流。 在應用程式中儲存資料串流後，請聯絡LLM應用程式團隊進行變更。

## 開始之前 {#before-you-begin}

您需要：

- **支援的執行階段和SDK。** 您的處理常式存放庫必須使用`@adobe/llm-apps-runtime` 1.2.0或更新版本。 若是EDS Widget，請使用LLM應用程式SDK 0.1.13或更新版本。 若要在啟用Analytics之前將舊版應用程式更新為最新的執行階段和SDK，請依照[升級現有應用程式](/help/guides/upgrade-existing-app.md)中的指示進行。 符合這些需求的應用程式不需要升級。
- **代理欄位群組。** 您的組織必須啟用「代理資訊」和「交談事件」欄位群組。 如果您將欄位群組新增至結構描述時，這些欄位群組未出現，請聯絡您的Adobe客戶團隊。 請參閱[實作交談見解](https://experienceleague.adobe.com/en/docs/analytics-platform/using/conversation-insights/implement#schema-and-datasets)。 動作呼叫記錄在代理資訊的`agenticExperience.tools`欄位中，該欄位位於[!DNL Experience Platform]的Beta中。
- **[!DNL Experience Platform]許可權**，可在您的沙箱中建立身分識別名稱空間、結構描述、資料集和資料串流。
- **針對[!DNL Adobe Analytics]：**&#x200B;管理報表套裝和處理規則的許可權。 產品管理員預設會同時擁有兩者。
- **針對[!DNL Customer Journey Analytics]：**&#x200B;產品管理員存取權，以建立連線和資料檢視。

## 您的應用程式會傳送哪些內容 {#what-your-app-sends}

啟用Analytics並部署後，您的應用程式會傳送兩種事件：

| 事件 | `eventType` | 傳送時間 | 包括 |
|---|---|---|---|
| 動作呼叫 | `mcp.tool_call` | LLM平台會呼叫應用程式的其中一個動作，包括失敗或傳回登入提示的呼叫。 | 動作的程式碼識別碼以及呼叫是否失敗。 使用者意圖（當動作收集且平台提供意圖時）。 平台提供時：平台名稱、交談ID、平台的使用者ID、地區設定和組織，以及使用者的大致位置。 |
| Widget互動 | `web.webinteraction.linkClicks` | 有人在Edge Delivery Services Widget中選取連結或按鈕。 | 連結或按鈕標籤，最多100個字元。 當動作呼叫提供時，其交談ID和結束ID。 Adobe Web SDK也會新增Widget框架URL、畫面和瀏覽器視窗大小、當地時間和時區，以及自己的版本。 |

在[!DNL Adobe Analytics]中，每個動作呼叫都會計為名為`mcp:<code identifier>`之頁面的頁面檢視，而每個Widget互動都會計為連結點選。 檢視Adobe Analytics](https://experienceleague.adobe.com/en/docs/analytics/implementation/aep-edge/hit-types)中的[Edge Network事件型別。

每個動作呼叫和每個Widget互動都是您[!DNL Experience Platform]資料集裡的一個[!DNL Adobe Analytics]點選和一個事件。 在高流量應用程式中，啟用Analytics之前，請檢查您的合約是否涵蓋此流量。

閱讀報表時，請牢記以下詳細資訊：

- **失敗的呼叫包含登入提示。** 如果某些動作需要登入，預期會有較高的失敗率。
- **未記錄部分被拒絕的通話。** 當[!DNL ChatGPT]以外的平台呼叫需要在沒有有效登入或所需範圍的情況下登入的動作時，呼叫會在動作執行前被拒絕，且不會傳送任何事件。 若應用程式中的每個動作都需要登入，則任何未登入的呼叫都會以相同方式遭到拒絕。
- **使用者意圖經常遺失。** 平台會決定是否提供此功能，因此許多呼叫沒有並會顯示為&#x200B;**未指定**。
- **平台詳細資料不盡相同。** 目前，[!DNL ChatGPT]提供所有這些。 對於[!DNL Claude]，僅記錄平台名稱。
- **Widget標籤來自頁面。** 若要設定穩定標籤，請新增`data-analytics`屬性至連結或按鈕。 否則，會使用其可見文字。

若要選擇收集使用者意圖的動作，請參閱[設定分析](/help/guides/create-action.md#configure-analytics)。

>[!NOTE]
>
>這些事件可包含平台使用者ID和大概的位置。 啟用Analytics之前，請先檢閱您的隱私權政策和資料控管設定。

### 事件如何識別人員 {#identities}

| 事件 | 主要身分 | 命名空間 |
|---|---|---|
| 動作呼叫 | LLM平台提供的使用者ID時 | `MCPHOSTUSER` |
| Widget互動 | Edge Network在動作呼叫期間發出的Experience Cloud ID (ECID)。 `MCPHOSTUSER` ID在可用時包含為次要身分識別。 | `ECID` |

有些平台（例如[!DNL Claude]）不提供使用者ID。 來自這些平台的動作呼叫沒有身分，因此[!DNL Customer Journey Analytics]會略過它們。

>[!NOTE]
>
>每個動作呼叫都會取得新的ECID，而Widget互動會使用顯示Widget之動作呼叫的ECID。 因此：
>
>- [!DNL Customer Journey Analytics]會將使用者的動作呼叫計為一個人，但將其Widget互動計為每個動作呼叫的不同人。
>- [!DNL Adobe Analytics]可能會將每個動作呼叫計為新訪客。

## 步驟1：建立身分名稱空間 {#identity-namespace}

**需要：** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]。

動作呼叫會識別`MCPHOSTUSER`名稱空間中的人員，因此您的沙箱中必須存在名稱空間。 使用下列設定建立自訂名稱空間：

| 設定 | 值 |
|---|---|
| 顯示名稱 | `MCP host user`或您選擇的其他名稱 |
| 身分符號 | `MCPHOSTUSER`，與所示完全相同 |
| 身分識別類型 | **[!UICONTROL 個別跨裝置識別碼]** |

如需程式，請參閱[建立自訂名稱空間](https://experienceleague.adobe.com/en/docs/experience-platform/identity/features/namespaces#create-namespaces)。

## 步驟2：建立結構 {#schema}

**需要：** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]。

資料串流和資料集都使用此結構描述。 使用下列設定建立結構描述：

| 設定 | 值 |
|---|---|
| 結構描述型別 | **[!UICONTROL 標準]**，使用&#x200B;**[!UICONTROL 手動]**&#x200B;選項建立 |
| 基底類別 | **[!UICONTROL 體驗活動]** |
| 欄位群組 | 代理資訊、交談事件、Web詳細資訊、環境詳細資訊和實作詳細資訊 |
| 顯示名稱 | 任何名稱，例如`AEM LLM Apps` |

>[!IMPORTANT]
>
>新增全部五個欄位群組。 [!DNL LLM Apps]需要代理資訊和交談事件才能進行動作呼叫。 其他三個則包含事件也包含的頁面、連結、裝置、環境和實作欄位。 如果欄位群組遺失，[!DNL Experience Platform]可以捨棄這些欄位或拒絕事件。

您不需要Adobe Analytics ExperienceEvent欄位群組。 使用者意圖改為透過處理規則達到[!DNL Adobe Analytics]。 請參閱[步驟4](#report-suite)。

如需程式，請參閱[建立新結構描述](https://experienceleague.adobe.com/en/docs/experience-platform/xdm/ui/resources/schemas#create)和[新增欄位群組](https://experienceleague.adobe.com/en/docs/experience-platform/xdm/ui/resources/schemas#add-field-groups)。

## 步驟3：建立資料集 {#dataset}

**需要：** [!DNL Customer Journey Analytics]。

資料集會將您的應用程式事件儲存在[!DNL Experience Platform]中。 使用下列設定建立資料集：

| 設定 | 值 |
|---|---|
| 建立方法 | **[!UICONTROL 從結構描述建立資料集]** |
| 結構描述 | 來自[步驟2](#schema)的結構描述 |
| 名稱 | 任何名稱，例如`AEM LLM Apps` |

如需程式，請參閱[建立資料集](https://experienceleague.adobe.com/en/docs/experience-platform/catalog/datasets/user-guide#create)。

## 步驟4：準備報表套裝 {#report-suite}

**需要：** [!DNL Adobe Analytics]。

[!DNL Adobe Analytics]會自動對應頁面名稱和連結欄位，因此`mcp:<code identifier>`頁面和Widget連結點選不需要額外設定。 使用者意圖需要eVar和處理規則。

### 建立或選擇報表套裝 {#create-report-suite}

建立報表套裝，或選擇現有報表套裝。 記下完整的報表套裝ID，包括[!DNL Adobe Analytics]新增的前置詞。 您需要[步驟5](#datastream)中的識別碼。

如需此程式，請參閱[建立報表套裝](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/c-new-report-suite/t-create-a-report-suite)。

### 啟用使用者目的eVar {#user-intent-evar}

在報表套裝的轉換變數中，啟用具有下列設定的eVar：

| 設定 | 值 |
|---|---|
| eVar | EVAR3 |
| 名稱 | `User Intent` |
| 類型 | **[!UICONTROL 文字字串]** |
| 配置 | **[!UICONTROL 最近（最後一個）]** |
| 有效期限 | **[!UICONTROL 造訪]** |
| 狀態 | **[!UICONTROL 已啟用]** |

如果報表套裝已使用eVar3，請選擇另一個未使用的eVar，並在處理規則中使用該eVar。

如需程式，請參閱[新增或編輯轉換變數](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/conversion-variables/conversion-var-admin#add-or-edit-conversion-variables)。

### 建立處理規則 {#processing-rule}

[!DNL Adobe Analytics]會以內容資料變數（而非eVar）接收使用者目的。 若要瞭解原因，請參閱[隱含對應](https://experienceleague.adobe.com/en/docs/analytics/implementation/aep-edge/xdm-var-mapping#implicit-mapping)。 建立處理規則，將使用者意圖複製到eVar：

- **條件：**&#x200B;內容資料變數`a.x.conversation.prompt.raw.0.purpose`等於`intents`。
- **動作：**&#x200B;以內容資料變數`a.x.conversation.prompt.raw.0.text`覆寫&#x200B;**使用者意圖(eVar3)**&#x200B;的值。

若沒有此規則，**使用者意圖**&#x200B;維度會保持空白。 此規則僅適用於儲存後所收集的資料。 Widget互動沒有使用者意圖，因此規則不會變更這些意圖。

其他欄位（例如呼叫是否失敗或平台名稱）也會以內容資料變數的形式送達。 若要在[!DNL Adobe Analytics]中報告這些規則，請以相同方式建立更多處理規則。

如需程式，請參閱[處理規則總覽](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-overview)和[處理規則使用案例](https://experienceleague.adobe.com/en/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-use-cases)。

## 步驟5：建立資料串流 {#datastream}

**需要：** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]。

資料流會接收您應用程式的事件，並將其轉送給您新增的每個服務。

### 建立資料串流 {#create-datastream}

使用下列設定建立資料流：

| 設定 | 值 |
|---|---|
| 名稱 | 任何名稱，例如`AEM LLM Apps` |
| 對應結構描述 | 來自[步驟2](#schema)的結構描述 |

在與結構描述的相同沙箱中建立資料串流。 您不能在建立資料流的沙箱後變更它。

如需程式，請參閱[建立資料流](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#create)。

### 新增服務 {#add-services}

為每個目的地新增服務：

| 服務 | 設定 | 下列專案需要： |
|---|---|---|
| **[!UICONTROL Adobe Analytics]** | **[!UICONTROL 已啟用]**。 在&#x200B;**[!UICONTROL 報表套裝ID]**&#x200B;中，輸入[步驟4](#report-suite)中的ID。 | [!DNL Adobe Analytics] |
| **[!UICONTROL Adobe Experience Platform]** | **[!UICONTROL 已啟用]**。 在&#x200B;**[!UICONTROL 事件資料集]**&#x200B;中，從[步驟3](#dataset)中選取資料集。 保留其他選項為清除狀態。 | [!DNL Customer Journey Analytics] |

![新增服務 — Adobe Analytics已啟用範例報表套裝ID](/help/assets/guide-analytics/datastream-analytics-service.png)

![Adobe Experience Platform服務 — 已選取事件資料集並清除其他選項](/help/assets/guide-analytics/datastream-platform-service.png)

如需程式，請參閱[將服務新增至資料流](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#add-services)、[Adobe Analytics設定](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#analytics)和[Adobe Experience Platform設定](https://experienceleague.adobe.com/en/docs/experience-platform/datastreams/configure#aep)。

然後從資料流詳細資料複製&#x200B;**[!UICONTROL 資料流識別碼]**。 在[步驟6](#enable-analytics)中需要它。

![資料流詳細資料 — 已啟用Adobe Analytics和Adobe Experience Platform服務，側面板中有範例資料流ID和對應結構描述](/help/assets/guide-analytics/datastream-services.png)

## 步驟6：啟用Analytics並部署至中繼 {#enable-analytics}

**需要：** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]。

>[!IMPORTANT]
>
>儲存後，資料流在「應用程式設定」中為唯讀。 請聯絡LLM應用程式團隊以變更或關閉Analytics。 請先檢查資料串流ID，然後再儲存。 您仍然可以變更資料流本身的報告套裝和資料集。

1. 開啟您的應用程式，並在左側導覽中選取&#x200B;**[!UICONTROL 設定]**。
2. 選取&#x200B;**[!UICONTROL Analytics]**&#x200B;索引標籤。
3. 開啟&#x200B;**[!UICONTROL 啟用分析]**。
4. 在&#x200B;**[!UICONTROL 資料串流識別碼]**&#x200B;中，貼上[步驟5](#datastream)中的識別碼。
5. 在&#x200B;**[!UICONTROL 需求]**&#x200B;底下，選取&#x200B;**[!UICONTROL 檢視詳細資料]**，並確認您的應用程式符合每個需求。

   ![應用程式設定，Analytics標籤 — 啟用Analytics已開啟，空白的資料流ID欄位和展開的需求清單](/help/assets/guide-analytics/analytics-settings.png)

6. 選取&#x200B;**[!UICONTROL 儲存變更]**。
7. [將您的應用程式](/help/guides/deploy-your-app.md)部署到Stage。 此設定會在下次部署到每個環境時生效。

>[!NOTE]
>
>資料流會套用至整個應用程式。 「暫存」和「生產」會將事件傳送至相同的資料流，而事件不會記錄傳送它們的環境。 您在Stage上的測試呼叫會出現在與生產流量相同的報表中。

## 步驟7：建立連線和資料檢視 {#cja}

**需要：** [!DNL Customer Journey Analytics]。

### 建立連線 {#connection}

建立連線，並使用這些設定從[步驟3](#dataset)新增資料集：

| 設定 | 值 |
|---|---|
| 沙箱 | 包含您資料集的沙箱 |
| 每日事件平均數量 | 符合您預期磁碟區的範圍 |
| 資料集 | 來自[步驟3](#dataset)的資料集 |
| 人員 ID | **[!UICONTROL 身分對應]** |
| 使用主要身分識別命名空間 | 已選取 |
| 時間戳記 | **[!UICONTROL 時間戳記]** |
| 資料來源型別 | **[!UICONTROL 其他]** |
| 資料來源說明 | 任何描述，例如`LLM Apps events` |
| 匯入新資料 | **[!UICONTROL 匯入所有新資料]**&#x200B;已開啟 |
| 資料集回填 | **[!UICONTROL 如果資料集已包含您要分析的事件，則回填所有現有的資料]**&#x200B;已開啟 |

>[!IMPORTANT]
>
>開啟&#x200B;**[!UICONTROL 匯入所有新資料]**。 此設定會建立持續連線，以便在Workspace中自動提供新事件。

透過此人員ID，[!DNL Customer Journey Analytics]會使用每個事件的主要身分做為人員，並略過沒有主要身分的事件。 檢視[事件如何識別人員](#identities)。

如需程式，請參閱[建立連線](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#create-connection)、[事件資料集設定](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#event-dataset-settings)和[使用身分對應作為人員ID](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-connections/create-connection#id-map)。

### 建立資料檢視 {#data-view}

使用這些設定在連線上建立資料檢視：

| 設定 | 值 |
|---|---|
| 連線 | 您剛才建立的連線 |
| 名稱 | 任何名稱，例如`LLM Apps` |
| 元件 | 您要報告的維度和量度。 請參閱下表。 |

將這些結構欄位新增為元件：

| 報告 | 結構描述欄位 | 新增為 |
|---|---|---|
| 動作 | `agenticExperience.tools.name` | 維度 |
| 失敗的動作呼叫 | `agenticExperience.tools.failed` | 維度 |
| 失敗原因 | `agenticExperience.tools.errorReason` | 維度 |
| Platform | `agenticExperience.agents.name` | 維度 |
| 使用者意圖 | `conversation.prompt.raw.text` | 維度 |
| 對話 | `conversation.conversationID` | 維度 |
| Widget標籤 | `web.webInteraction.name` | 維度 |

新增文字欄位（例如使用者意圖）作為維度。 新增為量度的文字欄位只能計算，依其值劃分時，顯示&#x200B;**無效**。

如需程式，請參閱[設定資料檢視](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-dataviews/create-dataview#configure)和[元件](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-dataviews/create-dataview#components)。

## 步驟8：驗證資料並部署至生產環境 {#verify}

**需要：** [!DNL Adobe Analytics]和[!DNL Customer Journey Analytics]。

從支援的LLM平台呼叫中繼應用程式中的動作，然後選取其介面工具集中的連結。 檢視ChatGPT中的[測試](/help/guides/test-in-chatgpt.md)或Claude中的[測試](/help/guides/test-in-claude.md)。 然後檢查您設定的每項服務：

| 服務 | 檢查位置 | 有何期望 |
|---|---|---|
| [!DNL Experience Platform] | 從[步驟3](#dataset)針對資料集進行的&#x200B;**[!UICONTROL 資料集活動]** | 新擷取的批次，沒有失敗的批次 |
| [!DNL Adobe Analytics] | 使用來自[步驟4](#report-suite)之報表套裝的Workspace專案，並具有自由表格 | **[!UICONTROL 頁面]**&#x200B;名為`mcp:<code identifier>`的資料列，**[!UICONTROL 自訂連結]**&#x200B;資料列，用於Widget互動，以及&#x200B;**使用者意圖**&#x200B;維度中的意圖文字 |
| [!DNL Customer Journey Analytics] | 使用[步驟7](#cja)資料檢視的Workspace專案 | 您的動作呼叫與Widget互動的&#x200B;**[!UICONTROL 事件]** |

每個服務都會延遲處理資料，因此不會立即顯示新事件。

若要建置報表，請參閱[在Adobe Analytics中建立專案](https://experienceleague.adobe.com/en/docs/analytics/analyze/analysis-workspace/build-workspace-project/create-projects)、[自由表格概觀](https://experienceleague.adobe.com/en/docs/analytics/analyze/analysis-workspace/visualizations/freeform-table/freeform-table)和[在Customer Journey Analytics中建立專案](https://experienceleague.adobe.com/en/docs/analytics-platform/using/cja-workspace/build-workspace-project/create-projects)。

資料正確時，[將應用程式](/help/guides/deploy-your-app.md)部署至生產環境。

## 限制 {#limitations}

- **Widget互動會分割成許多人員。** 每個動作呼叫都會獲得新的ECID，因此[!DNL Customer Journey Analytics]會將不同動作呼叫的Widget互動計為不同人員。 檢視[事件如何識別人員](#identities)。
- **沒有您網站的連結。** 當有人瀏覽至您網站的Widget連結時，該網站造訪未連結至對話。
- **中繼和生產共用報告。** 兩個環境都會傳送至相同的資料流，而事件不會記錄傳送它們的環境。
- **某些平台未傳送任何身分。** 來自[!DNL Customer Journey Analytics]中不提供使用者ID之平台（例如[!DNL Claude]）的動作呼叫會被略過。
- **每個動作呼叫一圈。** 每個動作呼叫都會記錄為自己的交談輪迴，因此觸發數個動作的提示會計為數個輪迴。

## 疑難排解 {#troubleshooting}

| 您所看到的內容 | 檢查內容 |
|---|---|
| 資料集未收到任何批次 | 將Analytics設定儲存至測試環境後，您便已部署應用程式。 資料串流ID正確。 資料流有&#x200B;**[!UICONTROL Adobe Experience Platform]**&#x200B;服務與您的資料集。 |
| 批次失敗 | 結構描述包含[步驟2](#schema)中的所有五個欄位群組。 |
| [!DNL Adobe Analytics]沒有資料 | 資料流已使用正確的報表套裝ID啟用&#x200B;**[!UICONTROL Adobe Analytics]**&#x200B;服務。 |
| **頁面**&#x200B;顯示`mcp:<code identifier>`，但&#x200B;**使用者意圖**，而且動作欄位是空的 | 您的處理常式存放庫使用`@adobe/llm-apps-runtime` 1.2.0或更新版本，而且您已在升級後部署。 開始之前](#before-you-begin)請參閱[。 |
| 某些動作的&#x200B;**使用者意圖**&#x200B;是空的 | 來自[步驟4](#processing-rule)的處理規則會使用與所示完全相同的內容資料變數（以小寫字母表示），以及正確的eVar。 此動作已開啟&#x200B;**[!UICONTROL 收集使用者意圖]**。 此規則僅適用於儲存後所收集的資料。 |
| [!DNL Customer Journey Analytics]顯示的事件比資料集還少 | **[!UICONTROL 匯入所有新資料]**&#x200B;已開啟。 系統會略過沒有主要身分的事件，例如來自未提供使用者ID之平台的動作呼叫。 |
| 您無法變更資料串流ID或關閉Analytics | 啟用後，這些設定在「應用程式設定」中為唯讀。 請連絡LLM應用程式團隊以取得協助。 若要將資料路由到其他地方，請變更資料流本身的報告套裝或資料集。 |

## 後續步驟 {#whats-next}

- [設定動作分析](/help/guides/create-action.md#configure-analytics)
- [部署您的應用程式](/help/guides/deploy-your-app.md)
