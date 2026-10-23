---
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '1418'
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
| `setup-choice.png` | 開始使用兩個可見的基礎結構選項 | 建立您的LLM應用程式 — 選擇Adobe管理基礎架構或您自己的存放庫 |
| `app-details.png` | 應用程式名稱、AEM程式、分析地區和不可變設定警告 | 應用程式詳細資料 — 輸入名稱、選取AEM程式並選擇分析區域 |
| `action-repository.png` | 已選取GitHub的擁有存放庫路徑以及存放庫連線指示 | 動作基礎結構設定 — 連線空的或相容的動作存放庫 |
| `provisioning.png` | 核准的Adobe管理設定進行中 | 應用程式設定 — Adobe正在準備基礎結構 |

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
| `generated-action-review.png` | LLM Widget詳細資料、動作詳細資料、Analytics、產生的提取請求通知，以及標籤為已檢閱 | 產生的動作 — 核准前先檢查Widget和處理常式 |

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
| `action-basic-info.png` | 搜尋產品、其衍生的識別碼、有用的說明和唯讀註解 | 建立新動作 — 基本資訊和註解 |
| `action-analytics-user-intent.png` | 收集使用者意圖和預設的匿名摘要指示 | 建立新動作 — 檢閱使用者意圖集合 |
| `widget-metadata.png` | Edge Delivery Services區塊型別和安全的範例沙箱來源 | 建立新動作 — Widget型別和沙箱網域 |
| `widget-template-urls.png` | 範例指令碼和Widget URL | 建立新動作 — 指令碼和Widget URL |
| `widget-permissions.png` | 選用的瀏覽器許可權，未使用的許可權關閉 | 建立新動作 — 瀏覽器許可權 |
| `widget-csp.png` | 資源、連線、框架、重新導向和基礎URI網域控制項 | 建立新動作 — 內容安全性原則網域 |

建立對話方塊有&#x200B;**動作**&#x200B;和&#x200B;**Widget中繼資料**標籤。 已儲存的編輯器
有**LLM Widget詳細資料**、**動作詳細資料**&#x200B;和&#x200B;**Analytics**標籤。 擷取
配置程式實際使用的版面。 填寫範例欄位而不儲存，然後
捨棄編輯。

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
