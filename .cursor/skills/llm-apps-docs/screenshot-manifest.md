---
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '398'
ht-degree: 0%

---
# 入門熒幕擷圖資訊清單

擷取收件匣： `docs-captures/<YYYY-MM-DD>/`

輸出目錄： `help/assets/guide-onboarding-agent/`

僅擷取對使用者做出決定或驗證狀態有重大幫助的查核點。

Source檔案名稱不需要符合最終檔案名稱。 技能會根據可見的UI狀態繪製熒幕擷取畫面、保留原始檔案，並使用以下名稱建立經過清理的副本。

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
- 包含： **新增 <plugin> 至ChatGPT **和**&#x200B;連線&#x200B;**。
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
