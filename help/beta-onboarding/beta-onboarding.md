---
title: Adobe LLM應用程式的Beta入門
description: 以Beta計畫參與者的身分開始使用Adobe LLM應用程式。
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '1551'
ht-degree: 0%

---


# Beta入門 {#beta-onboarding}

>[!IMPORTANT]
>
>**免責宣告：**&#x200B;這是[!DNL LLM Apps]的測試版本。 這裡顯示的功能、工作流程和UI不一定代表應用程式或產品的最終狀態。

>[!NOTE]
>
>開始之前，請確定已符合所有[必要條件](/help/beta-onboarding/prerequisites.md)。

作為Beta計畫參與者，您將收到一封電子郵件，其中包含兩個zip封存和一個應用程式設定參考。 請依照下列步驟，讓您的應用程式上線。

## 開始之前

在深入研究這些步驟之前，請先熟悉本指南中使用的主要概念。 這可節省您的時間，並協助一切按一下就位。

**LLM應用程式** — 使用者在[!DNL ChatGPT]或其他LLM平台中互動的品牌化助理。

**動作** — 您的應用程式所提供的功能。 例如，「尋找經銷商」或「瀏覽產品」。 使用者提出相關問題時，LLM會叫用每個動作。

**動作處理常式** — 叫用動作時執行的程式碼。 它可以呼叫您的API、擷取即時資料或傳回靜態資料。 Adobe提供的範例處理常式會傳回硬式編碼資料，讓您在連線實際後端之前，可以端對端驗證設定。

**Widget** — 顯示給使用者的視覺回應 — 卡片、輪播、表格或任何與LLM文字回覆一起呈現的自訂UI。

**應用程式設定參考** — Adobe提供的檔案，可讓您在設定應用程式時，為每個動作準確輸入內容。


## 步驟1：將提供的封存推送到[!DNL GitHub]

Adobe透過電子郵件提供兩個zip封存：

- **應用程式程式碼** (`<project-name>.zip`) — 在[!DNL Adobe I/O Runtime]上執行並支援應用程式邏輯的動作處理常式。 您將依原樣部署這些應用程式，以讓應用程式端對端運作，然後稍後再更新，以連線您真正的後端。
- **EDS專案** (`<project-name>-eds.zip`) — 您Widget的前端程式碼。 Adobe已為您預先建立這些程式；這是您的程式碼基底，可供您擁有、自訂和風格，以符合您的品牌。

在[!DNL GitHub]上建立&#x200B;**兩個新的空白存放庫** （每個封存一個），然後解壓縮每個封存並將其推送。 我們建議將每個存放庫命名在對應zip檔案之後 — `<project-name>`代表應用程式程式碼，`<project-name>-eds`代表EDS專案。

`<your-github-org>`是指您的個人[!DNL GitHub]使用者名稱或[!DNL GitHub]組織 — 將擁有存放庫的帳戶。

**應用程式程式碼存放庫** — 解壓縮封存、初始化本機Git存放庫，並將其推送到[!DNL GitHub]：

```bash
# Unzip and enter the folder
unzip <project-name>.zip
cd <project-name>

# Initialize and push
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-repo>.git
git push -u origin main
```

**EDS存放庫** — 對EDS封存重複相同的步驟，指向第二個存放庫：

```bash
unzip <project-name>-eds.zip
cd <project-name>-eds

git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-eds-repo>.git
git push -u origin main
```

## 步驟2：建立LLM應用程式

導覽至[experience.adobe.com/llm-apps/](https://experience.adobe.com/llm-apps/)，然後按一下&#x200B;**[!UICONTROL 建立LLM應用程式]**。

![應用程式頁面 — 尚未建立任何應用程式](/help/assets/guide-create-app/first-load.png)

使用應用程式設定參考中&#x200B;**[!UICONTROL 應用程式詳細資料]**&#x200B;區段的值，填入&#x200B;**[!UICONTROL 應用程式詳細資料]**：

- **[!UICONTROL LLM應用程式名稱]**
- **[!UICONTROL LLM應用程式描述]**
- **[!UICONTROL 您的網站]**

![建立應用程式對話方塊](/help/assets/guide-create-app/app-details-1.png)

在&#x200B;**[!UICONTROL Analytics資料區域]**&#x200B;下，選取將儲存分析資料的區域。 應用程式建立後，無法變更此&#x200B;**&#x200B;**。

>[!IMPORTANT]
>
>應用程式建立後，就無法變更分析資料區域。

![分析資料區域下拉式清單](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

在&#x200B;**存放庫**&#x200B;底下，選取您先前推送的[!DNL GitHub]組織和應用程式程式碼存放庫&#x200B;**。**

>[!NOTE]
>
>如果您是第一次設定應用程式，您的[!DNL GitHub]組織將不會出現在清單中。 按一下&#x200B;**[!UICONTROL 連線其他GitHub組織]**&#x200B;以連結您的組織並授與存放庫的存取權。

![建立應用程式對話方塊 — 已連結的存放庫](/help/assets/guide-create-app/app-details-repo-linked.png)

保留&#x200B;**[!UICONTROL 根據您的網站自動建議動作]**&#x200B;未勾選 — 您將手動設定動作。

接受&#x200B;**[!UICONTROL Adobe Developer條款]**，然後按一下&#x200B;**[!UICONTROL 建立應用程式]**。

![正在建立應用程式 — 正在載入畫面](/help/assets/guide-create-app/app-loading.png)

![應用程式詳細資料頁面](/help/assets/guide-create-app/app-detail-top.png)


## 步驟3：讓您的Widget上線

在此步驟中，您設定了Adobe提供的EDS專案，並透過[!DNL DA.live] — Adobe的撰寫和CDN層發佈專案。 每個發佈的檔案都會成為叫用動作時向使用者顯示的Widget。

### 步驟3.1：將EDS存放庫連線至[!DNL DA.live]

1. 移至[github.com/apps/aem-code-sync](https://github.com/apps/aem-code-sync)。 如果尚未安裝應用程式，請按一下[安裝]。**&#x200B;** 如果已安裝，請按一下&#x200B;**[!UICONTROL 設定]**，然後將`<your-eds-repo>`新增至其可存取的存放庫清單。
2. 安裝後，您登陸&#x200B;**[!DNL AEM Code Sync]已註冊的**&#x200B;確認頁面。 在「**下一步→建立您的內容**」下，按一下「[!DNL DA.live]」連結。
3. 在&#x200B;**示範內容**&#x200B;畫面上，選取&#x200B;**無**，然後按一下&#x200B;**製作精彩**。
4. 您被帶往網站的[!DNL DA.live]作者檢視。

### 步驟3.2：為每個動作建立[!DNL DA.live]檔案

在[!DNL DA.live]中，您必須為每個動作&#x200B;**建立一個檔案**。 發佈後，每個檔案都會變成叫用該動作時向使用者顯示的Widget。

針對每個動作：

1. 在[!DNL DA.live]中，在您的網站根目錄建立新檔案，並按照應用程式設定參考中指定的方式加以命名（請參閱&#x200B;**[!DNL DA.live]檔案**&#x200B;區段）。
2. 在檔案中，使用左側邊欄並按一下&#x200B;**[!UICONTROL 區塊]**&#x200B;以插入新區塊。
3. 將區塊標頭設為應用程式設定參考中指定的區塊名稱（請參閱&#x200B;**[!DNL DA.live]檔案**&#x200B;區段）。
4. 使用&#x200B;**[!UICONTROL 發佈]**&#x200B;按鈕（頂端工具列中的紙張平面圖示）發佈檔案。

發佈後，可在`https://main--<your-eds-repo>--<your-github-org>.aem.live/<document-name>`存取每個檔案。 在步驟4中設定每個動作時，您會在&#x200B;**[!UICONTROL Widget URL]**&#x200B;欄位中輸入此URL。


### 步驟3.3：設定EDS網站的CORS標題

若要允許LLM平台載入您的Widget跨來源，您必須將`Access-Control-Allow-Origin`標題新增至EDS網站。

移至[tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html)的&#x200B;**HTTP標頭編輯器**。

1. 輸入您的&#x200B;**組織** (`<your-github-org>`)和&#x200B;**網站** （您的EDS存放庫名稱），然後按一下&#x200B;**[!UICONTROL 擷取]**。 系統會提示您驗證並授權存取您的網站。
2. 在路徑`/**`下，按一下&#x200B;**[!UICONTROL 新增標題]**。
3. 將標頭名稱設為`Access-Control-Allow-Origin`，並將值設為`*`。
4. 按一下&#x200B;**[!UICONTROL 儲存]**。

如需[!DNL AEM Edge Delivery Services]中自訂HTTP標頭的完整檔案，請參閱[aem.live/docs/custom-headers](https://www.aem.live/docs/custom-headers)。

儲存標頭後，觸發程式碼同步以將變更傳播到所有檔案：

```bash
curl -X POST "https://admin.hlx.page/code/<your-github-org>/<your-eds-repo>/main/*"
```


## 步驟4：新增動作

在[LLM Apps UI](https://experience.adobe.com/llm-apps/)中，開啟您的應用程式並在左側邊欄中導覽至&#x200B;**[!UICONTROL 動作]**。 按一下&#x200B;**+**&#x200B;以建立新動作。 對應用程式設定參考中說明的每個動作重複此動作（請參閱&#x200B;**動作1**、**動作2**、**動作3**&#x200B;區段）。

![動作頁面 — 尚未有動作](/help/assets/guide-create-action/actions-empty.png)

### 動作標籤

- **動作名稱**&#x200B;和&#x200B;**描述** — 由LLM平台用來決定何時叫用動作。 使用應用程式設定參考中&#x200B;**動作標籤**&#x200B;區段的確切值。
- **輸入引數** — 每個引數的名稱、型別和描述。 使用應用程式設定參考中&#x200B;**動作標籤**&#x200B;區段的值。

![建立動作 — 基本資訊](/help/assets/guide-create-action/action-basic-info.png)

### Widget中繼資料標籤

- **型別** — 選取&#x200B;**[!UICONTROL EDS]**。

展開&#x200B;**[!UICONTROL CSP組態]**&#x200B;並填入：

- **[!UICONTROL CSP — 連線網域]** — 使用您應用程式設定參考中&#x200B;**Widget中繼資料標籤**&#x200B;區段的值。
- **[!UICONTROL CSP — 資源網域]** — 使用您應用程式設定參考中&#x200B;**Widget中繼資料標籤**&#x200B;區段的值。

![建立動作 — Widget中繼資料](/help/assets/guide-create-action/widget-metadata.png)

![建立動作 — 許可權和CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

### Widget Builder索引標籤

在&#x200B;**[!UICONTROL Widget來源]**&#x200B;下，選取&#x200B;**[!UICONTROL 使用現有的Widget]**，然後填入：

- **[!UICONTROL 指令碼URL]** — 使用您應用程式設定參考中&#x200B;**Widget中繼資料標籤**&#x200B;區段的值。
- **[!UICONTROL Widget URL]** — 使用您應用程式設定參考中&#x200B;**Widget中繼資料標籤**&#x200B;區段的值。

按一下&#x200B;**[!UICONTROL 建立動作]**。 此動作會在[動作]頁面上以卡片形式顯示，並具有&#x200B;**[!UICONTROL EDS]**&#x200B;徽章和引數計數。

![動作頁面 — 已建立動作](/help/assets/guide-create-action/actions-with-action.png)


## 步驟5：部署

設定所有動作後，請前往「應用程式詳細資料」頁面，然後按一下右上角的&#x200B;**[!UICONTROL 部署]**。

![應用程式詳細資料 — 準備部署](/help/assets/guide-deploy/app-detail-deploy-ready.png)

選取目標環境並按一下&#x200B;**[!UICONTROL 部署]**。 管道會執行四個步驟：準備認證、開始部署、從存放庫建立您的應用程式，以及發佈至[!DNL Adobe I/O Runtime]。

![部署管道正在執行](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

完成時，請捲動至[應用程式詳細資料]頁面上的&#x200B;**[!UICONTROL 測試應用程式]**&#x200B;區段，並複製&#x200B;**[!UICONTROL MCP伺服器URL]** — 您需要它才能在[!DNL ChatGPT]中註冊您的應用程式。

![部署成功](/help/assets/guide-deploy/app-detail-deploy-finish.png)

![測試應用程式 — 已部署的URL](/help/assets/guide-deploy/test-app-deployed.png)


## 步驟6：將應用程式新增至[!DNL ChatGPT]

將自訂應用程式新增至[!DNL ChatGPT]需要&#x200B;**Pro**、**Business**&#x200B;或&#x200B;**Enterprise**&#x200B;訂閱。 免費和加號方案不支援自訂MCP應用程式。

1. 在[!DNL ChatGPT]中，按一下您的設定檔頭像，然後移至&#x200B;**[!UICONTROL 設定]**。

   ![ChatGPT — 設定功能表](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

2. 在側邊欄中選取&#x200B;**[!UICONTROL 應用程式]**，按一下&#x200B;**[!UICONTROL 進階設定]**，然後啟用&#x200B;**[!UICONTROL 開發人員模式]**。

   ![ChatGPT — 開發人員模式已啟用](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

3. 移至[!UICONTROL 應用程式&#x200B;]&#x200B;**→的**&#x200B;[!UICONTROL &#x200B;設定]並按一下&#x200B;**[!UICONTROL 建立應用程式]**。

   ![ChatGPT — 建立應用程式對話方塊](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

4. 貼上從[!DNL LLM Apps]複製的&#x200B;**[!UICONTROL MCP伺服器URL]**，將&#x200B;**[!UICONTROL 驗證]**&#x200B;設定為&#x200B;*無驗證*，核取確認核取方塊，然後按一下&#x200B;**建立**。

您的應用程式會顯示在&#x200B;**[!UICONTROL 已啟用的應用程式]**&#x200B;之下，並具有&#x200B;**[!UICONTROL DEV]**&#x200B;徽章。

![ChatGPT — 已啟用應用程式](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

開始新的交談，使用&#x200B;**+**&#x200B;按鈕或輸入&#x200B;**@**&#x200B;後接您的應用程式名稱，附加您的應用程式，並詢問符合其中一個設定動作的問題。

![ChatGPT — 從功能表選取應用程式](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

![ChatGPT — 動作結果](/help/assets/guide-test-chatgpt/chatgpt-response.png)

## 後續步驟

您部署的範例應用程式使用硬式編碼資料。 若要將其轉換為生產就緒體驗：

- **連線您的API** — 更新應用程式程式碼存放庫中的動作處理常式，以呼叫您真正的API、資料庫或服務。 每個處理常式都位於`actions/<action-name>/index.js`。
- **檢閱並調整您的Widget** — 開啟您的EDS專案，調整區塊樣式和版面配置以符合您的品牌，並驗證Widget是否正確呈現即時資料。
- **重新部署** — 更新您的處理常式和Widget後，請將變更推送到[!DNL GitHub]，然後按一下[!DNL LLM Apps] UI中的&#x200B;**[!UICONTROL 部署]**&#x200B;以發佈新版本。
- **提交以進行發佈** — 當您對體驗感到滿意時，請透過[!DNL ChatGPT]外掛程式或聯結器發佈程式提交您的應用程式以供檢閱。 Adobe不會控制此程式 — 請參閱LLM平台檔案以瞭解提交需求和時間表。
