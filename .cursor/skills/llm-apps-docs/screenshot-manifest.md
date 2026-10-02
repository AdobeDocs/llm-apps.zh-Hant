---
source-git-commit: 03c918b1643d9c4e8ebee40fd67694acb6751a14
workflow-type: tm+mt
source-wordcount: '1080'
ht-degree: 0%
---
# 熒幕擷圖資訊清單

擷取收件匣： `docs-captures/<YYYY-MM-DD>/`

僅擷取對使用者做出決定或驗證狀態有重大幫助的查核點。

Source檔案名稱不需要符合最終檔案名稱。 技能會根據可見的UI狀態繪製熒幕擷取畫面、保留原始檔案，並使用以下名稱建立經過清理的副本。

下面的每個指南都宣告自己的輸出目錄。 使用擷取所屬的區段。

&#x200B;# 入門指南

輸出目錄： `help/assets/guide-onboarding-agent/`

## 必要的擷取

### `app-details-onboarding.png`

- 狀態：已選取應用程式名稱、分析區域和&#x200B;**自動建置我的應用程式**。
- 包括：應用程式詳細資料、分析地區和建立我的應用程式的開始。
- 替代文字： `Create LLM App — app details and Build My App enabled`

### `install-aem-code-sync.png`

- 狀態：使用AEM範本初始化的空白EDS存放庫；需要AEM程式碼同步。
- 包括：EDS存放庫驗證訊息和安裝連結。
- 替代文字： `Create LLM App — empty EDS repository initialized and AEM Code Sync required`

### `eds-admin-required.png`

- 狀態：已安裝AEM Code Sync，但目前的使用者不是EDS網站管理員。
- 包含：完整的驗證訊息和&#x200B;**開啟AEM即時管理員**。
- 替代文字： `Create LLM App — EDS administrator access required`

### `actions-generating.png`

- 狀態：上線時的動作頁面為作用中。
- 包括：進度訊息和產生步驟。
- 替代文字： `Actions — generating recommendations`

### `actions-ready-for-review.png`

- 狀態：上線完成之後和核准之前產生的動作清單。
- 包含：動作名稱、產生/稽核狀態以及稽核控制。
- 僅使用夾具內容。
- 替代文字： `Actions — generated actions ready for review`

### `generated-action-review.png`

- 狀態：有一名代表產生動作。
- 包含：動作和Widget中繼資料導覽、處理常式產生結果，以及&#x200B;**標示為已檢閱**。
- 遮罩：必要的儲存區域擁有者。
- 替代文字： `Generated action — ready to mark as reviewed`

### `actions-reviewed.png`

- 狀態：已檢閱每個產生的動作。
- 包含： **所有動作都已檢閱**、動作徽章，以及&#x200B;**移至應用程式頁面**。
- 替代文字： `Actions — all generated actions reviewed`

### `deploy-stage.png`

- 狀態：啟動前的部署對話方塊。
- 包含：中繼目標環境和&#x200B;**部署**。
- 替代文字： `Deploy — select the Stage environment`

### `deploy-running.png`

- 狀態：部署管道正在執行。
- 包括：準備、開始、建置和發佈步驟。
- 替代文字： `Deploy — deployment pipeline running`

### `deploy-successful.png`

- 狀態：成功的中繼部署。
- 包括：環境和成功狀態。
- 遮罩：執行階段名稱空間、完整MCP URL、ID、時間戳記（若有識別）。
- 替代文字： `Deploy — successful staging deployment`

### `app-mcp-url.png`

- 狀態：在部署後測試應用程式區段。
- 包括：中繼環境、**複製URL**&#x200B;和成功的部署歷史記錄。
- 遮罩： MCP伺服器URL。
- 替代文字： `App Detail — copy the staging MCP server URL`

### `chatgpt-plugins-page.png`

- 狀態： ChatGPT外掛程式頁面。
- 包含：外掛程式標籤、搜尋和建立按鈕。
- 替代文字： `ChatGPT — Plugins page`

### `chatgpt-new-plugin.png`

- 狀態：新增外掛程式對話方塊。
- 包括：名稱、說明、伺服器URL、驗證、確認及建立。
- 遮罩： MCP伺服器URL。
- 替代文字： `ChatGPT — create a plugin with the MCP server URL`

### `chatgpt-plugin-connect.png`

- 狀態：建立外掛程式後確認。
- 包含： **新增 <plugin> 至ChatGPT &#x200B;** 和**&#x200B;連線&#x200B;**。
- 遮罩：瀏覽器URL和聯結器識別碼。
- 替代文字： `ChatGPT — connect the new plugin`

### `chatgpt-generated-app.png`

- 狀態：在ChatGPT中叫用的夾具外掛程式。
- 包括：附加的應用程式、產生的Widget和文字回應。
- 排除：交談記錄、帳戶名稱和不相關的應用程式。
- 替代文字： `ChatGPT — generated LLM App plugin response`

## 選擇性擷取

只有在文章無法清楚解釋決策時新增擷取：

- GitHub應用程式存放庫存取權選取專案。
- 疑難排解的上線狀態失敗。
- 外掛程式圖示上傳。

請勿為已用散文清除的靜態欄位清單新增熒幕擷取畫面。

&#x200B;# 驗證指南

輸出目錄： `help/assets/guide-authentication/`

由[authentication.md](../../../help/guides/authentication.md)參考。

**[!UICONTROL 複製資源識別碼]**&#x200B;步驟會重複使用入門指南的
`app-mcp-url.png`. 不要再擷取它。

此段落中的每一個擷取都會顯示安全性組態。 儲存前遮罩：

- **[!UICONTROL 簽發者]** URL，以及可識別身分提供者或其廠商的任何主機名稱。
- MCP伺服器URL完整顯示（無論它出現在何處）。
- 租使用者、使用者端和組織識別碼。
- 帳戶名稱、頭像和電子郵件。

在欄位必須保持清晰的位置使用中性預留位置值 — 例如
`https://auth.example.com`. 範圍名稱應該讀為一般範例，例如`orders:read`。

## 必要的擷取

### `auth-core-settings.png`

- 狀態： **[!UICONTROL 設定]** > **[!UICONTROL 驗證]** （具有&#x200B;**[!UICONTROL 啟用驗證]**）並填入&#x200B;**[!UICONTROL 核心設定]**。
- 包含： **[!UICONTROL Workspace]**&#x200B;選擇器，顯示&#x200B;**[!UICONTROL 階段]**、**[!UICONTROL 在其開啟狀態中啟用驗證]**、**[!UICONTROL 簽發者]**&#x200B;以及支援的&#x200B;**[!UICONTROL 領域]**，其中至少包含兩個領域。
- 包含摺疊的&#x200B;**[!UICONTROL 進階設定]**&#x200B;控制項，讓讀者可以看到&#x200B;**[!UICONTROL JWKS URI]**&#x200B;是選用的，而且位於其所在位置。
- 遮罩：簽發者主機名稱。
- 替代文字： `Authentication — enable authentication and complete the core settings`

擷取2026-08-25。 裁剪以放置空白畫布；不需要遮罩，因為
產品中的&#x200B;**[!UICONTROL 簽發者]**&#x200B;設定為`https://auth.example.com`，早於
capture. 偏好使用於之後編輯影像。 **[!UICONTROL 支援的領域]**&#x200B;保留
一個範圍(`read:all`)；兩個可以更好地說明欄位，但這不值得
自行重新擷取。

### `auth-per-action.png`

- 狀態：啟用驗證後&#x200B;**[!UICONTROL 每個動作組態]**，且有意混合模式。
- 包含：至少三個動作，每個模式一個 — **[!UICONTROL 無]**、**[!UICONTROL 必要]**&#x200B;和&#x200B;**[!UICONTROL 選用]** — 以及閘道上的&#x200B;**[!UICONTROL 領域]**&#x200B;資料行。
- 包含： **[!UICONTROL 所有動作都需要驗證]**，最好是處於不確定狀態，這是混合組態產生的結果。
- 僅使用夾具動作名稱。
- 替代文字： `Authentication — set an auth mode and scopes for each action`

擷取2026-08-25。 僅限裁切，沒有遮色片。 顯示所有三種模式，填入
**[!UICONTROL 領域]**&#x200B;儲存格，以及&#x200B;**[!UICONTROL 需要驗證其中的所有動作]**
不確定的狀態，以`Test Action 1/2/3`作為夾具名稱。

裁切&#x200B;**內部**&#x200B;設定面板自己的容器邊框 — 每個都有一個全高1px規則
在擷取的一側，將其中一個保留在框架中時，會讀為邊緣下方的雜散線。
影像。

產品本身關於每個聯結器套用驗證[!DNL Claude]的警告為
在兩個擷取回合&#x200B;**中在此索引標籤上未觀察到**，因此此處不需要它。 此
指南會改用散文說明該行為。 如果警告確實存在於之後的組建中，
將其擷取為`auth-claude-warning.png`並新增專案。

### `chatgpt-authentication-mode.png`

- 狀態： **[!UICONTROL 新外掛程式]**&#x200B;對話方塊與&#x200B;**[!UICONTROL 驗證]**&#x200B;下拉式清單已開啟。
- 包含：所有三個值 — **[!UICONTROL 無Auth]**、**[!UICONTROL Mixed]**&#x200B;和&#x200B;**[!UICONTROL OAuth]** — 因此指南中的對應表格可對照實際控制項檢查。
- 遮罩：MCP伺服器URL，以及瀏覽器URL中的任何聯結器識別碼。
- 替代文字： `ChatGPT — select the authentication mode for the plugin`

以與上線指南的`chatgpt-new-plugin.png`相同的方式將其框架化：對話方塊卡片
頁面周圍仍可看到邊界，左右大約40px。 請勿裁切排清至
卡片。

擷取2026-08-25 （光模式），以符合檔案中的所有其他擷取。 此
下拉式清單會包含&#x200B;**[!UICONTROL 伺服器URL]**&#x200B;欄位，所以MCP URL無法辨識 — 但是
它的半透明材質讓該欄位內容的模糊影像流過
選項。 三個未反白的列會以面板填色及其標籤重新上色
重新呈現，這會將其移除。 透過取樣驗證，而不是透過眼睛驗證：出血的模糊程度足以讓
遺漏，而且是MCP伺服器URL。

請注意，即時控制項提供&#x200B;**4**&#x200B;個值 — **[!UICONTROL OAuth]**， **存取權
權杖/ API金鑰&rbrack;**、**&#x200B;[!UICONTROL 沒有驗證]&#x200B;**&#x200B;和&#x200B;**&#x200B;[!UICONTROL 混合]**。 指南的對應
表格僅涵蓋應用程式的驗證模式可對應的三種（正確但未涵蓋）
將下拉式清單描述為有三個選項。

## 選擇性擷取

只有在文章證明不足時新增：

- `auth-scope-blocked.png` — **[!UICONTROL 儲存]**&#x200B;已封鎖，因為動作需要&#x200B;**[!UICONTROL 支援的領域]**&#x200B;中缺少領域。 對疑難排解專案很有用。
- 對話中間登入提示會引發&#x200B;**[!UICONTROL 選擇性]**&#x200B;動作。 平台擁有的UI經常變更，且已有散文說明。

不要擷取識別提供者自己的登入頁面。 它可識別本檔案未列出的廠商。
