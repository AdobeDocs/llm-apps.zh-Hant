---
title: 自動建立您的第一個LLM應用程式
description: 從您的網站建立Adobe LLM應用程式、檢閱產生的動作、將其部署，並在支援的LLM平台（例如ChatGPT）中進行測試。
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '1217'
ht-degree: 0%

---


# 自動建立您的第一個應用程式 {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

平台可將您的網站轉換為運作中的應用程式支架。 它會建議動作、寫入處理常式程式碼和測試、建立EDS Widget，並將產生的檔案傳送到您擁有的兩個[!DNL GitHub]存放庫。

產生約需15分鐘。 在本教學課程結束時，您將會有已部署的應用程式，您可以在支援的LLM平台（例如[!DNL ChatGPT]）中進行測試。

**歷程：**&#x200B;確認建立兩個存放庫→建立應用程式→檢閱產生的動作→部署→中繼環境→測試外掛程式→連線生產系統的需求。

## 開始之前

請先完成所有[LLM應用程式需求](/help/overview/overview.md#requirements)，再開始本教學課程。

本教學課程會建立[Frescopa Coffee](https://frescopa.coffee/)的LLM應用程式。

## 建立兩個空白的存放庫

平台需要兩個空白的存放庫。 在相同的[!DNL GitHub]帳戶或組織下建立兩者：

- **處理常式存放庫** — 儲存動作處理常式與測試。 例如，`my-brand-llm-app`。
- **EDS存放庫** — 儲存產生的Widget區塊和樣式。 例如，`my-brand-llm-app-eds`。

前往每個存放庫的[github.com/new](https://github.com/new)。

請勿使用README、`.gitignore`或授權初始化存放庫。 平台會準備所需的專案結構。

>[!TIP]
>
>使用可識別應用程式和每個存放庫用途的存放庫名稱。 這可讓應用程式建立對話方塊更容易辨識。

## 啟動應用程式

1. 開啟[Adobe LLM應用程式](https://experience.adobe.com/#/@llmapps/llm-apps/)並選取&#x200B;**[!UICONTROL 建立應用程式]**。
2. 輸入&#x200B;**[!UICONTROL LLM應用程式名稱]**&#x200B;和選用的說明。
3. 選取&#x200B;**[!UICONTROL Analytics區域]**。

   >[!IMPORTANT]
   >
   >應用程式建立後，就無法變更Analytics區域。

4. 在&#x200B;**[!UICONTROL 建立我的應用程式]**&#x200B;中，選取&#x200B;**[!UICONTROL 自動建立我的應用程式]**。
5. 在&#x200B;**[!UICONTROL 您的網站]**&#x200B;中，輸入包含`https://`通訊協定的網站URL。 平台會分析此網站，以判斷有用的動作和代表性範例結果。

![建立LLM應用程式 — 應用程式詳細資料及建置我的應用程式已啟用](/help/assets/guide-onboarding-agent/app-details-onboarding.png)

## 授予[!DNL LLM Apps]存放庫的存取權

Adobe LLM Apps [!DNL GitHub]應用程式會提供[!DNL LLM Apps]存取您選取的存放庫許可權。

>[!NOTE]
>
>連線[!DNL GitHub]組織是一次性設定。 如果組織已出現在對話方塊中，請使用&#x200B;**[!UICONTROL 在GitHub]**&#x200B;上管理存放庫，而非再次連線。

### 已連線的組織

如果您在建立存放庫之前已安裝Adobe LLM Apps [!DNL GitHub]應用程式：

1. 選取已連線的組織。
2. 選取&#x200B;**[!UICONTROL 在GitHub上管理存放庫]**。
3. 將兩個存放庫新增至現有的[!DNL GitHub]應用程式安裝。
4. 返回[!DNL LLM Apps]並重新整理存放庫清單。

### 僅首次連線

如果組織未出現在對話方塊中：

1. 選取&#x200B;**[!UICONTROL 連線GitHub組織]**。
2. 安裝Adobe LLM Apps [!DNL GitHub]應用程式。
3. 選擇&#x200B;**[!UICONTROL 僅選取存放庫]**&#x200B;並選取兩個存放庫。
4. 返回「建立LLM應用程式」對話方塊。

如果您無法安裝或更新[!DNL GitHub]應用程式，請洽詢組織管理員。

## 選取存放庫

1. 在&#x200B;**[!UICONTROL 樣板存放庫]**&#x200B;下，選取組織和空的處理常式存放庫。
2. 在&#x200B;**[!UICONTROL EDS存放庫]**&#x200B;下，選取組織和空的EDS存放庫。

   ![建置我的應用程式 — 選取GitHub組織、樣版存放庫和EDS存放庫](/help/assets/guide-onboarding-agent/repos-selected.png)

3. 根據&#x200B;**[!UICONTROL 條款與條件]**，請核取&#x200B;**[!UICONTROL 我接受Adobe Developer條款]**。
4. 選取&#x200B;**[!UICONTROL 建立應用程式]**。

## 完成EDS設定

當選取的EDS存放庫為空白時，[!DNL LLM Apps]會使用AEM樣版將其初始化。 接著，對話方塊會要求您先安裝AEM程式碼同步，再嘗試再次建立應用程式。

1. 在EDS存放庫下方的訊息中，選取&#x200B;**[!UICONTROL 安裝AEM程式碼同步]**。
2. 在[!DNL GitHub]上安裝AEM Code Sync，並授與它對EDS存放庫的存取權。
3. 返回「建立LLM應用程式」對話方塊。

![建立LLM應用程式 — 已初始化空的EDS存放庫且需要AEM程式碼同步](/help/assets/guide-onboarding-agent/install-aem-code-sync.png)

您必須是EDS網站的管理員。 如果對話方塊報告您不是管理員：

![建立LLM應用程式 — 需要EDS系統管理員存取權](/help/assets/guide-onboarding-agent/eds-admin-required.png)

1. 選取&#x200B;**[!UICONTROL 開啟AEM即時管理員]**。
2. 按一下&#x200B;**[!UICONTROL +新增使用者]**&#x200B;按鈕，將您新增為EDS網站的管理員。

   ![建立LLM應用程式 — 將您新增為EDS管理員](/help/assets/guide-onboarding-agent/add-eds-admin.png)

3. 返回[!DNL LLM Apps]，重新整理EDS存放庫，然後再次選取&#x200B;**[!UICONTROL 建立應用程式]**。

存放庫和管理員檢查通過後，[!DNL LLM Apps]會建立應用程式並開始產生動作。

## 等待動作產生

從左側移至&#x200B;**[!UICONTROL 動作]**&#x200B;頁面。 代理程式分析網站並產生應用程式時，「動作」頁面會顯示&#x200B;**探索您對話式體驗的動作**。 產生通常需要大約15分鐘。 您可以離開此頁面，稍後再返回。

![動作 — 產生建議](/help/assets/guide-onboarding-agent/actions-generating.png)

產生期間，[!DNL LLM Apps]：

1. 分析網站並識別有用的客戶意圖。
2. 建立動作中繼資料，包括說明和輸入引數。
3. 為處理常式存放庫中的每個動作產生處理常式並進行測試。
4. 為EDS存放庫中的每個動作產生EDS Widget。
5. 準備供您檢閱的動作。

產生的處理常式最初會使用衍生自網站的範例資料。 他們展示完整的體驗，但未連線至您的生產系統。

## 檢閱產生的動作

產生完成後，「動作」頁面會顯示產生的動作和Widget預覽。 每個動作都有&#x200B;**[!UICONTROL AI產生的動作，需要檢閱]**&#x200B;徽章。

![動作 — 產生的動作已準備好檢閱](/help/assets/guide-onboarding-agent/actions-ready-for-review.png)

針對每個動作：

1. 選取&#x200B;**[!UICONTROL 檢閱]**。
2. 檢閱名稱、說明、引數、註解、產生的處理常式和Widget。
3. 選取&#x200B;**[!UICONTROL 標籤為已檢閱]**。 這會合併產生的提取請求。
4. 返回「動作」頁面，然後對其餘動作重複此動作。

![已產生的動作 — 準備標籤為已檢閱](/help/assets/guide-onboarding-agent/generated-action-review.png)

檢閱所有動作時，請選取&#x200B;**[!UICONTROL 移至應用程式頁面]**。

![動作 — 已檢閱所有產生的動作](/help/assets/guide-onboarding-agent/actions-reviewed.png)

>[!NOTE]
>
>產生的程式碼是您擁有的起點。 您可以在檢閱後變更動作中繼資料、處理常式、測試、Widget JavaScript和Widget樣式。

## 部署應用程式

1. 返回「應用程式詳細資料」頁面。
2. 選取&#x200B;**[!UICONTROL 部署]**。
3. 選取&#x200B;**[!UICONTROL 階段]**&#x200B;作為目標環境。
4. 選取&#x200B;**[!UICONTROL 部署]**。

![部署 — 選取中繼環境](/help/assets/guide-onboarding-agent/deploy-stage.png)

[!DNL LLM Apps]正在準備、建置和發佈應用程式，請稍候。

![部署 — 部署管道正在執行](/help/assets/guide-onboarding-agent/deploy-running.png)

![部署 — 成功的中繼部署](/help/assets/guide-onboarding-agent/deploy-successful.png)

部署後，**[!UICONTROL 測試應用程式]**&#x200B;區段會顯示暫存MCP伺服器URL。 選取&#x200B;**[!UICONTROL 複製URL]**。

![應用程式詳細資料 — 複製暫存MCP伺服器URL](/help/assets/guide-onboarding-agent/app-mcp-url.png)

## 在[!DNL ChatGPT]中測試

請依照ChatGPT](/help/guides/test-in-chatgpt.md)中的[測試，使用暫存MCP伺服器URL建立外掛程式。

提出符合其中一個產生之動作的問題。 確認：

- [!DNL ChatGPT]選取預期的動作。
- Widget會呈現並包含預期的範例資料。
- Widget控制項會產生預期的後續追蹤行為。
- 文字回應會準確摘要結果。

![ChatGPT — 產生的LLM應用程式外掛程式回應](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

您現在擁有正常運作的端對端架構。

## 讓應用程式生產就緒

產生的應用程式使用範例資料。 在和客戶一起使用之前：

1. **連線您的系統** — [自訂每個產生的處理常式](/help/guides/customize-handler.md)，以呼叫API或資料來源來取代範例資料。
2. **保護認證** — 將API URL和認證儲存在Managed執行階段設定中，絕不儲存在原始碼或Widget JavaScript中。
3. **驗證資料** — 驗證動作引數和API回應、新增要求逾時以及傳回安全錯誤訊息。
4. **更新Widget** — 讓每個Widget與其處理常式的`structuredContent`保持一致，然後套用您的品牌和協助工具要求。 請參閱[自訂產生的Widget](/help/guides/widgets.md)。
5. **測試處理常式** — 涵蓋有效輸入、無效輸入、空白結果、API失敗以及Widget預期的資料形狀。
6. **在Stage**&#x200B;中驗證 — 透過[!DNL ChatGPT]外掛程式重新部署並測試每個動作。
7. **部署至生產環境** — 中繼測試成功後，請部署至生產環境，並使用生產MCP伺服器URL建立或更新外掛程式。

若要新增平台未建立的功能，請參閱[從頭開始建立動作](/help/guides/create-action.md)。

