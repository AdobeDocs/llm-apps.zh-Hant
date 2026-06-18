---
title: Adobe LLM應用程式疑難排解
description: 建置、部署和測試Adobe LLM應用程式時常見問題的解決方案。
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '451'
ht-degree: 0%

---


# 疑難排解 {#troubleshooting}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

這會提供使用[!DNL Adobe LLM Apps]時的疑難排解資訊。

## 常見問題

| 症狀 | 可能的原因 | 嘗試什麼 |
|---------|----------------|-------------|
| 應用程式未出現在LLM平台中 | 您的LLM平台訂閱不支援自訂MCP應用程式，或未啟用開發人員模式 | 確認您的計畫支援自訂MCP應用程式。 在&#x200B;**設定→應用程式→進階設定**&#x200B;中啟用開發人員模式 |
| LLM平台中的「無法連線」錯誤 | MCP伺服器URL不正確或部署失敗 | 從「應用程式詳細資料」頁面仔細檢查URL。 檢查部署歷史記錄是否有失敗 |
| 未叫用動作 | LLM平台無法比對使用者問題和您的動作 | 使用`@YourApp`明確叫用它。 改善動作說明以協助模型比對方式 |
| Widget未呈現 | EDS Widget URL或CSP網域設定錯誤 | 驗證「建立動作」對話方塊中的指令碼URL和Widget內嵌URL。 檢查CSP資源和連線網域是否包含您的EDS來源 |
| 空白或錯誤回應 | 處理常式有錯誤或遺失 | 請先使用`npm start`在本機測試。 檢視[本機開發](/help/reference/development.md#local-development) |
| Widget已載入但未顯示任何資料 | `structuredContent`形狀不符合區塊所預期的形狀 | 在區塊的`decorate`函式中記錄`bridge.toolResult`，並與處理常式輸出進行比較 |
| 在「複製並建置」時部署失敗 | 您的存放庫中有`npm install`或webpack建置錯誤 | 在本機執行`npm install && npm run build`以重現錯誤 |
| 部署在「收集認證」失敗 | 存放庫未連結或Developer Console專案設定錯誤 | 確認存放庫已連結至「應用程式詳細資料」設定頁面 |
| 載入Widget時發生CORS錯誤 | EDS網站缺少`access-control-allow-origin`標頭 | 透過`admin.hlx.page`設定CORS標頭 |
| 儲存CORS標題時，HTTP標題編輯器傳回`404 Error updating config: config not found` | 網站設定遺失`headers`區段 | 請參閱下方的[初始化EDS網站設定標頭區段](#initialize-the-eds-site-config-headers-section) |
| Widget會在預覽中呈現，但不會在LLM平台中呈現 | 區塊在預覽模式中回覆為範例資料，但因即時資料而失敗 | 使用MCP檢查器或CURL以實際`structuredContent`進行測試 |

## 初始化EDS網站設定標題區段

如果HTTP標題編輯器傳回`404 Error updating config: config not found`，則網站設定遺失`headers`區段。 手動修正：

1. 移至[tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html)，輸入您的組織和網站，然後按一下&#x200B;**[!UICONTROL 擷取]**。
2. 開啟瀏覽器DevTools （[網路]索引標籤），並從擷取要求中複製`x-auth-token`標頭的值。
3. 擷取目前的網站設定：

   ```bash
   curl -H "x-auth-token: $TOKEN" \
     https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json > config.json
   ```

4. 開啟`config.json`並將`"headers": {}`新增至JSON物件。
5. 將更新的設定發佈回：

   ```bash
   curl -X POST \
     -H "x-auth-token: $TOKEN" \
     -H "Content-Type: application/json" \
     -d @config.json \
     "https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json"
   ```

6. 重新載入標題編輯器並正常儲存`Access-Control-Allow-Origin`標題。

