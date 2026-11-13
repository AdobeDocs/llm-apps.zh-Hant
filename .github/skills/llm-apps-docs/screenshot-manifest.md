---
source-git-commit: 0276ae4e4627e49157911ad579a42c0e77045eee
workflow-type: tm+mt
source-wordcount: '2314'
ht-degree: 1%
---
# 熒幕擷圖資訊清單

使用瀏覽器自動化擷取這些狀態
[熒幕擷取畫面.md](screenshots.md)。 選取與文章相關的查核點；
此資訊清單未授權資源建立、產生、核准或
部署。

原始擷取保留在`docs-captures/<run-id>/`內。 僅發佈視覺化檢閱、
`help/assets/`下的已淨化PNG。 當發生以下情況時，保持檔案名稱穩定：
重新整理影像。

## 基礎架構設定

文章： `/help/guides/create-app.md`

輸出目錄： `help/assets/guide-infrastructure/`

| 檔案名稱 | 狀態與有用的內容 | 建議的替代文字 |
|---|---|---|
| `setup-choice.png` | 開始使用&#x200B;**讓我們處理它**&#x200B;和&#x200B;**使用我的Cloud Manager設定**， callout on the own-repository option | 建立您的LLM應用程式 — 開始使用讓我們來處理，並使用我的Cloud Manager設定 |
| `action-repository.png` | 已選取Cloud Manager程式和存放庫，**已驗證存放庫** | 動作基礎結構設定 — 已選取Cloud Manager方案和存放庫 |
| `widget-source.png` | 含有&#x200B;**的Widget來源手動選取Widget存放庫** （Cloud Manager EDS標示為即將推出） | Widget基礎結構設定 — Widget來源 |
| `widget-repository.png` | 已驗證非GitHub Widget存放庫，**Edge Delivery Services網站**&#x200B;備註 | Widget基礎結構設定 — 已驗證Widget存放庫及其連結的EDS網站 |
| `app-details.png` | 應用程式名稱，從存放庫設定的AEM程式，「應用程式分析」資料區域 | 應用程式詳細資料 — 名稱、AEM程式和應用程式見解資料區域 |
| `settings-repositories.png` | 設定>包含Cloud Manager存放庫卡和連結網站的存放庫 | 應用程式設定 — 存放庫區段 |
| `provisioning.png` | 核准的LLM應用程式管理安裝進行中 | 應用程式設定 — Adobe正在準備基礎結構 |

上次擷取（除了`provisioning.png`和`da-write-permissions.png`以外）： 2026-11-13，階段
(`experience-stage.adobe.com/llm-apps`)、GitLab動作和Widget存放庫與攜入
自己的Git。 方案、存放庫、場地和來源值會遮罩；紅色外框是圖說文字。
未擷取： GitHub Widget路徑（安裝AEM程式碼同步、驗證AEM設定）。 尚未
已在生產環境中驗證。

排除帳戶詳細資料、組織ID、真實計畫名稱、存放庫
識別碼和應用程式ID。 使用空白欄位或中性夾具名稱，其中
有可能。 存放庫驗證可以初始化程式碼，因此請先取得核准
擷取驗證結果。

## 建立動作並檢閱產生的工作

文章： `/help/guides/creating-actions.md`

輸出目錄： `help/assets/guide-creating-actions/`

| 檔案名稱 | 狀態與有用的內容 | 建議的替代文字 |
|---|---|---|
| `build-choice.png` | 自動選取建置動作、輸入的公用網站，以及穩定的啟動控制項（若包含） | 動作 — 選擇自動產生並輸入公用網站 |
| `generating.png` | 正在執行的產生、時間估計和移至動作 | 動作 — 產生執行中 |
| `actions-ready-for-review.png` | 夾具動作名稱、稽核徽章和稽核控制項 | 動作 — 產生的動作已可供檢閱且未部署 |
| `generated-action-review.png` | 動作詳細資料、LLM Widget詳細資料、Application Insights、ChatGPT擴充功能、**已推送分支**&#x200B;通知（自己的存放庫）和標籤為已檢閱。 上次擷取：2026-11-13， Stage， GitLab app；應用程式名稱已遮罩 | 產生的動作檢閱 — 已推送分支並標籤為已檢閱 |

遮罩私人存放庫和提取請求連結。 裁切掉產生的個人或
當文章只需要評論狀態時的聯絡詳細資訊。 不要選取
**標示為已檢閱** （未經核准）。

僅當核准在範圍內且符合下列條件時，擷取所有動作已複查狀態
狀態確實存在。 不要核准產生的工作以滿足熒幕擷圖
檢查清單。

## 從頭開始建立動作

文章： `/help/guides/create-action.md`

輸出目錄： `help/assets/guide-create-action/`

| 檔案名稱 | 狀態與有用的內容 | 建議的替代文字 |
|---|---|---|
| `action-basic-info.png` | 動作詳細資料：搜尋產品、其代碼識別碼、說明，以及`category`和`query`引數 | 建立新動作 — 含有動作資訊和輸入引數的動作詳細資料 |
| `action-llm-settings.png` | 展開的LLM設定：唯讀、等冪、開放世界註解、狀態文字、可見度 | 建立新動作 — LLM設定 |
| `action-analytics-user-intent.png` | 應用程式分析：收集使用者意圖和預設指示 | 建立新動作 — Application Insights |
| `widget-metadata.png` | LLM Widget詳細資料： Edge Delivery Services區塊，範例Widget網域、Widget說明和範本URL | 建立新動作 — LLM Widget詳細資料 |
| `widget-csp.png` | 範本URL和CSP設定網域清單 | 建立新動作 — CSP設定 |

上次擷取：2026-11-13， Stage，對話方塊重構後(與動作編輯器相同的索引標籤：
動作詳細資料、LLM Widget詳細資料、應用程式深入分析、ChatGPT擴充功能；許可權、
偏好使用邊框，且「檔案引數」區段已移除)。 僅限範例值；對話方塊
已取消而未儲存。 紅色輪廓是圖說文字。

## 驗證

文章： `/help/guides/authentication.md`

輸出目錄： `help/assets/guide-authentication/`

| 檔案名稱 | 狀態與有用的內容 | 建議的替代文字 |
|---|---|---|
| `auth-core-settings.png` | 階段、啟用驗證、`https://auth.example.com`、`catalog:read`、`profile:read`和進階設定 | 驗證 — 啟用驗證並完成核心設定 |
| `auth-per-action.png` | 顯示「無」、「必要」、「可選」和指定範圍的夾具動作 | 驗證 — 選擇每個動作的驗證模式和範圍 |

使用未儲存的設定並在之後捨棄它們。 驗證已儲存的驗證
保持不變。 資源識別碼步驟會重複使用
`help/assets/guide-onboarding-agent/app-mcp-url.png`.

如果UI顯示與部署的執行階段或目前相衝突的警告
平台行為，記錄差異。 僅限表格的裁切方式較為適用
教學模式時，請勿在未說明的情況下隱藏相關警告。

## 應用程式變數

文章： `/help/guides/app-variables.md`

輸出目錄： `help/assets/guide-app-variables/`

使用`GREETING_PREFIX`搭配`Good day`作為範例值，使用`Howdy`作為
取代值。 絕對不要擷取實際的設定值。

| 檔案名稱 | 狀態與有用的內容 | 建議的替代文字 |
|---|---|---|
| `variables-empty.png` | 清空舞台工作區、Workspace選取器並新增 | 變數和密碼 — 空白的階段工作區 |
| `add-variable-dialog.png` | 未儲存的GREETING_PREFIX變數與目前的密碼支援通知 | 新增變數或密碼 — 輸入GREETING_PREFIX和Good Day |
| `variable-added.png` | 具有名稱、型別、值、時間戳記及列控制項的已核准夾具變數 | 變數和密碼 — Stage工作區中的GREETING_PREFIX |
| `update-variable-dialog.png` | 目前值好日子和未儲存的新值好日子 | 更新GREETING_PREFIX — 將「好日子」變更為「好日子」 |
| `delete-variable-dialog.png` | 確認對話方塊而不確認刪除 | 刪除GREETING_PREFIX — 檢查刪除確認 |

開啟對話方塊不會授權儲存或刪除。 重複使用安全的現有
夾具變數或是在建立夾具變數前詢問。 僅保留較舊的影像之後
正在驗證其欄位和控制項是否仍符合目前的UI。

## ChatGPT擴充功能

文章： `/help/guides/chatgpt-extensions.md`

輸出目錄： `help/assets/guide-chatgpt-extensions/`

全域和執行緒進入點熒幕擷取畫面、顯示設定和撰寫者提及
設定熒幕擷取畫面已於2026-11-10提供，並發佈為
記錄於下方。 其他查核點仍維持規劃中，而非現有資產。 不要
新增文章影像連結，直到真正的擷取經過清理並透過視覺化方式呈現為止
已檢閱。

| 檔案名稱 | 狀態與有用的內容 | 建議的替代文字 |
|---|---|---|
| `extensions-tab.png` | 已儲存的Widget支援動作，並選取ChatGPT擴充功能 | ChatGPT擴充功能 — 進入點、顯示模式和撰寫者提及 |
| `global-thread-entrypoints.png` | 一起為「首頁」Widget選取「全域」和「執行緒」，並取消勾選「檔案」 | ChatGPT擴充功能 — 為Home動作選取的全域和執行緒 |
| `global-entrypoint-menu.png` | 使用者端導覽功能表中的Home進入點 | 顯示Home全域進入點的ChatGPT導覽功能表 |
| `global-entrypoint-open.png` | 完整使用者端視窗，其中Home開啟了全熒幕並顯示導覽邊欄 | 從全域進入點開啟的首頁儀表板 |
| `thread-entrypoint-menu.png` | 已展開外掛程式的交談功能表 | 對話功能表 — 選擇主對話串進入點 |
| `thread-entrypoint-open.png` | 交談旁的首頁儀表板 | ChatGPT交談旁邊開啟的主儀表板 |
| `file-entrypoint.png` | 使用.csv的未儲存檔案進入點 | 檔案進入點 — 提供CSV檔案的自訂檢視器 |
| `entrypoint-icon.png` | 未儲存的選用圖示URL由動作的選取進入點共用 | 進入點圖示 — 設定選用的共用動作圖示 |
| `display-settings.png` | 模型啟動喜好設定、支援的資源模式，以及主機預設值的初始顯示 | 顯示模式 — 預設下的資源和模型啟動偏好設定 |
| `composer-mentions.png` | 搜尋提及且提及切換為開啟的純文字動作 | 搜尋提及 — 啟用撰寫器提及 |
| `composer-mentions-query.png` | 使用選用的字串查詢輸入搜尋提及動作細節 | 搜尋提及 — 選用的字串輸入命名查詢 |
| `composer-mentions-conflict.png` | 停用提及切換和警告命名現有提供者 | 撰寫器提及無法使用，因為搜尋提及已經是提供者 |

使用已核准、不敏感的型錄夾具。 確認表單未自動儲存
輸入範例值之前，請先捨棄未儲存的變更。 不要儲存，
核准產生的動作，或僅為了完成此檢查清單而進行部署。
協力廠商結果需要對相容使用者端的驗證存取權。 檔案
進入點需要案頭；工作瀏覽器不是典型的ChatGPT。

### Entrypoint檔案更新，2026-11-05

- Source：目前的本機`llm-apps-ui`元件和輸入驗證，而非
生產UI逐步解說。
- 已移除快速動作控制項。 全域、執行緒和檔案使用
核取方塊，支援針對一個Widget進行多個選取。 圖示URL為
由進入點共用的選擇性動作層級設定。
- 選取[檔案]會在不存在時新增名為`file`的選用物件輸入。
將「檔案」與「全域」或「執行緒」結合時，需要輸入保持選用狀態。
- 已封鎖瀏覽器檢查，因為兩個自動化瀏覽器設定檔都已
已有人使用。 未擷取到任何新的熒幕擷取畫面。

### 文字草稿來源，2026-11-03

- Source環境：本機UI/API/runtime/SDK實作，而不是完成的
生產動作編輯器逐步解說。
- 已發佈的套件驗證已確認執行階段1.3.0中的擴充功能API，而且
SDK 0.2.1。 這些是記錄的基準版本，而不是聲稱每
這些確切的發行版本中引進了功能。
- 已檢查OpenAI的公開擴充功能規格是否有平台限制。
- 生產環境登入在個別代理程式控制的瀏覽器中成功。 無應用程式
已儲存設定、未選取任何夾具，且未啟動任何部署。
- 使用者延後熒幕擷取畫面。 未擷取或取代任何新影像。
精確的生產控制標籤和端對端主機行為仍需要
發佈前的即時逐步解說。

## 部署和測試

文章： `/help/guides/deploy-your-app.md`

| 輸出目錄 | 檔案名稱 | 狀態與有用的內容 |
|---|---|---|
| `help/assets/guide-onboarding-agent/` | `deploy-stage.png` | 開始前的目標環境階段和部署控制項 |
| `help/assets/guide-onboarding-agent/` | `app-mcp-url.png` | 目前測試應用程式卡片、環境標籤和複製URL；遮罩每個執行階段URL |
| `help/assets/guide-deploy/` | `deployment-history.png` | 環境、狀態和部署日期欄，不含應用程式識別碼 |

允許時，對測試卡和歷史記錄使用唯讀現有部署。
除非使用者核准，否則請勿開始部署以取得這些狀態。
僅當狀態為兩者時，擷取執行中或成功的部署對話方塊
相關和授權。

針對`/help/guides/test-in-chatgpt.md`，保留
`help/assets/guide-onboarding-agent/chatgpt-generated-app.png`說明一個產品
回應。 它不會驗證目前的設定UI。 新增ChatGPT或Claude設定
熒幕擷取畫面需要目前經過驗證的存取；否則請使用正式廠商
指示並忽略未驗證的影像。

## Analytics設定

文章： `/help/guides/analytics-setup.md`

輸出目錄： `help/assets/guide-analytics/`

| 檔案名稱 | 狀態與有用的內容 | 建議的替代文字 |
|---|---|---|
| `analytics-settings.png` | 應用程式設定， Analytics標籤，啟用Analytics on，空的資料流ID，擴展的需求，停用儲存變更 | 應用程式設定， Analytics標籤 — 啟用Analytics已開啟、空白的資料流ID欄位和展開的需求清單 |
| `datastream-analytics-service.png` | 資料收集、新增服務、Adobe Analytics已啟用虛擬報表套裝ID | 新增服務 — 以範例報表套裝ID啟用Adobe Analytics |
| `datastream-platform-service.png` | 資料彙集， Adobe Experience Platform服務，僅選取事件資料集 | Adobe Experience Platform服務 — 已選取事件資料集並清除其他選項 |
| `datastream-services.png` | 已啟用服務、虛擬資料流ID和對應結構描述的資料流詳細資料 | 資料流詳細資料 — 兩種服務皆已啟用，範例為資料流ID和對應結構描述 |

永遠不要擷取真正的資料串流ID。 不要選取&#x200B;**儲存變更**：儲存後，
只有管理員可以變更資料流。 顯示Experience Platform、Adobe Analytics、
和Customer Journey Analytics熒幕，僅顯示下列專案的特定設定：
LLM應用程式；其他專案皆連結至Experience League。 取代組織
名稱、報表套裝ID、資料流ID、資料集ID、電子郵件和個人名稱
具有下列虛擬值： `example-org`、`demo.<suffix>`報表套裝，
`00000000-0000-0000-0000-000000000000`、`0000…0001`、`user@example.com`和
`Example User`.

## 準備提交

文章： `/help/guides/prepare-submission.md`

輸出目錄： `help/assets/guide-submission/`

| 檔案名稱 | 狀態與有用的內容 | 建議的替代文字 |
|---|---|---|
| `submission-card.png` | 生產部署後的應用程式頁面，透過設定準備提交卡片 | 準備提交 — 生產準備狀態和設定 |
| `submission-setup.png` | 準備提交對話方塊、設定和準備、範例發佈者詳細資料以及兩個圖示皆已儲存 | 設定並準備 — 發佈者和法律詳細資訊、核准圖示和儲存變更 |
| `submission-prepare.png` | 相同的對話方塊捲動以準備您的套件，整備顯示準備就緒，核取方塊清除 | 準備套件 — 準備就緒、確認核取方塊和準備套件 |
| `submission-progress.png` | 在執行時顯示相同的對話方塊 | 準備進行中 — 進度列和開始時間 |
| `submission-results.png` | 使用預留位置法律連結完成執行後的結果：準備工作需要注意，下載套件已停用 | 結果 — 準備摘要、下載報告、下載套件和下載錄製 |
| `submission-test-cases.png` | 結果，測試案例，第一個案例概述 | 測試案例 — 包含其請求、預期行為和記錄狀態的檢閱案例 |
| `submission-before-submit.png` | 結果，在您提交清單和PASS/WARN/FAIL檢查之前；在裁切下方執行ID | 提交之前 — 剩餘的詳細資訊、更新詳細資訊和封裝檢查 |

在中繼2026-11-13上從部署到生產環境的應用程式擷取。 僅使用
範例發行者值(`Frescopa Coffee (example)`， `https://www.example.com/...`)
和預留位置圖示。 每次準備執行都會呼叫已部署的應用程式，並處理
20分鐘；僅在「結果」畫面需要重新整理時開始一次。 遮罩MCP URL，
在結果擷取中執行ID和應用程式名稱。

## 現有的擷取來源

2026-10-19擷取回合使用使用者要求的本機UI，並具有相符的UI/API
來源檢查。 它不會建立生產同位檢查。

- 原始擷取： `docs-captures/2026-10-19/`及其`full-review/`子目錄。
- 舊版或已取代的公用影像仍保留在本機`retired/`子目錄中。
- 檔案固定已到產生動作稽核階段。 無動作
核准、提取請求合併、部署或儲存的驗證/變數
變更是該擷取執行的一部分。
- 先前執行的變數列、更新和刪除影像保留於
對照目前的實作檢查其控制項。
- 未重新擷取協力廠商設定畫面。 過時的設定熒幕擷取畫面為
已移除，而非顯示為最新。

每次新的重新整理都會更新日期、來源環境、驗證狀態
清理以及任何剩餘的驗證限制。 不要複製舊的來源
作為新擷取的證據轉寄。

### 動作編輯器切換移除，2026-10-19

已從執行中重新整理`guide-creating-actions/generated-action-review.png`
移除編輯器的已啟用開關後的本機UI。 應用程式頁面的可見度
切換器會維持開啟/關閉動作控制；模型和Widget曝光度設定
保留在編輯器中。 存放庫連結已遮罩，帳戶導覽已遮罩
已排除。 在此重新整理期間，未儲存、核准、切換或部署任何動作。
原始擷取： `docs-captures/2026-10-19/remove-enabled/`。

### Analytics設定，2026-10-21

已從使用者提供的重新整理`guide-analytics/analytics-settings.png`
`Screenshot 2026-10-21 at 11.48.36.png`，不是代理程式驅動的執行。 來源
環境：未記錄且未顯示在影像上；生產同位檢查未顯示
已驗證。 視覺審查確認啟用Analytics開啟，資料流ID欄位
空白，會展開「需求」，且會停用「儲存變更」。 影像是
複製時不會進行編輯；不會顯示任何識別碼或帳戶詳細資料。

### 資料流熒幕擷取畫面，2026-10-19

已新增`guide-analytics/datastream-analytics-service.png`，
`datastream-platform-service.png`，以及現有中的`datastream-services.png`
內部資料收集擷取（2026-07-29和2026-10-19）。 報表套裝ID
且資料串流ID已取代為虛擬文字重疊(`demo.aem.llm`和
`00000000-0000-0000-0000-000000000000`)；覆蓋字型是舊金山，所以它
與產品字型稍微不同。 不需要平台服務影像
變更。 Analytics服務影像仍會顯示來自
原始擷取。 OCR透過最終影像時，找不到原始識別碼。
以下列動作取代這些動作，新擷取會使用在UI中輸入的虛值：
文章將重新整理。
