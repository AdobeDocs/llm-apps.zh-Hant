---
title: 準備應用程式以提交ChatGPT
description: 使用Adobe LLM應用程式為您的應用程式準備提交套件、準備報表和測試錄製，然後在OpenAI入口網站中提交應用程式。
source-git-commit: faa25ef012ea10a592fcc79460aa5ca7ad537cf8
workflow-type: tm+mt
source-wordcount: '1453'
ht-degree: 0%
---

# 準備應用程式以提交ChatGPT {#prepare-submission}

>[!IMPORTANT]
>
>所有AEM Sites Cloud Service客戶都可使用LLM應用程式來開發及管理自己的應用程式。 一旦計數開始，每個應用程式呼叫都會計入客戶的AEM Sites內容請求。 Adobe會在計量生效前宣告計量速率和開始日期。

若要在[!DNL ChatGPT]外掛程式目錄中列出您的應用程式，請將提交套件上傳至OpenAI。 [!DNL LLM Apps]協助您準備它。 它會從您的應用程式建立套件ZIP，傳送測試請求至您部署的應用程式，並記錄結果以便您檢查。 然後，您會上傳套件，並在OpenAI入口網站中完成提交。

**在本指南中：**&#x200B;瞭解如何在[!DNL LLM Apps]為您的應用程式準備提交封裝，以及下一步在OpenAI入口網站中該做什麼。

>[!NOTE]
>
>[!DNL LLM Apps]未將您的應用程式提交至OpenAI。 開發人員和網域驗證、連線測試、檢閱和發佈會在OpenAI入口網站中進行。 檢視[上傳並提交您的外掛程式](https://developers.openai.com/plugins/deploy/submission)。

## 開始之前

- [將您的應用程式](/help/guides/deploy-your-app.md)部署至&#x200B;**生產環境**。 [!DNL LLM Apps]將其測試要求傳送至您應用程式的生產版本。
- 請確定您要發佈的動作已檢閱並啟用。
- 準備好您的發佈者名稱，以及支援頁面的URL、隱私權原則和服務條款。
- 準備兩個圖示：標誌和撰寫器圖示。 每個畫素必須是正方形、非動畫PNG、每側64至4096畫素，且不大於1MiB。 僅使用您有權發佈的影像。
- 如果您的應用程式[驗證一般使用者](/help/guides/authentication.md)，請在您的身分提供者中為[!DNL ChatGPT]註冊OAuth使用者端，除非您的身分提供者支援動態使用者端註冊(DCR)或使用者端識別碼中繼資料檔案(CIMD)。

## 開啟「準備提交」

第一次將應用程式部署到生產環境後，應用程式頁面會在&#x200B;**[!UICONTROL 測試應用程式]**&#x200B;下方顯示&#x200B;**[!UICONTROL 準備提交]**&#x200B;卡片。

![準備提交 — 生產準備狀態並設定](/help/assets/guide-submission/submission-card.png)

選取&#x200B;**[!UICONTROL 設定]**。 稍後，卡片上的按鈕會變更為顯示下一個步驟，例如&#x200B;**[!UICONTROL 檢視進度]**&#x200B;或&#x200B;**[!UICONTROL 檢視結果]**。

**[!UICONTROL 準備提交]**&#x200B;對話方塊開啟。 它有三個區段： **[!UICONTROL 設定與準備]**、**[!UICONTROL 結果]**&#x200B;和&#x200B;**[!UICONTROL 執行歷程記錄]**。

## 新增您的提交詳細資料

在&#x200B;**[!UICONTROL 中設定並準備]**：

1. 在&#x200B;**[!UICONTROL Publisher &amp; Legal]**&#x200B;下，輸入&#x200B;**[!UICONTROL 發佈者名稱]**、**[!UICONTROL 支援URL]**、**[!UICONTROL 隱私權原則URL]**&#x200B;和&#x200B;**[!UICONTROL 服務條款URL]**。
   - 針對&#x200B;**[!UICONTROL 發行者名稱]**，請輸入您將在OpenAI入口網站中驗證為發行者的公用名稱。
   - 針對&#x200B;**[!UICONTROL 支援URL]**，請輸入支援或連絡頁面的URL。 不接受電子郵件地址。
   - 隱私權政策必須說明您的應用程式、驗證、Widget和分析收集及使用的資料。
2. 在&#x200B;**[!UICONTROL 已核准的圖示]**&#x200B;下，選取&#x200B;**[!UICONTROL 選擇標誌]**，然後&#x200B;**[!UICONTROL 選擇撰寫器圖示]**，並為每個圖示選擇一個PNG檔案。
3. 如果您的應用程式使用驗證，請開啟&#x200B;**[!UICONTROL OAuth使用者端註冊]**，然後選擇[!DNL ChatGPT]取得OAuth使用者端的方式。 僅選擇您的身分提供者支援的選項：
   - **[!UICONTROL 已提供]** — 您已為[!DNL ChatGPT]註冊使用者端。 輸入其&#x200B;**[!UICONTROL 已註冊的使用者端ID]**。 請勿輸入您的Adobe IMS使用者端ID。
   - **[!UICONTROL DCR]** - [!DNL ChatGPT]使用動態使用者端註冊來註冊使用者端。
   - **[!UICONTROL CIMD]** - [!DNL ChatGPT]會使用使用者端ID中繼資料檔案來識別自身。

   [!DNL LLM Apps]從未要求使用者端密碼。
4. 選擇性地開啟&#x200B;**[!UICONTROL 其他提交詳細資料]**，並輸入清單文字、封裝名稱和版本、購買詳細資料、裝載的示範URL、支援的國家/地區以及發行說明。 如果您將欄位留白，[!DNL LLM Apps]會儘可能草擬文字。 無法草稿的任何內容稍後會顯示在&#x200B;**[!UICONTROL 提交之前]**&#x200B;下。
5. 選取&#x200B;**[!UICONTROL 儲存變更]**。

![設定並準備 — 發行者與法律詳細資料、核准圖示及儲存變更](/help/assets/guide-submission/submission-setup.png)

準備作業僅使用您儲存的詳細資訊和圖示。

>[!TIP]
>
>OpenAI檢閱者需要應用程式的示範影片（最好錄製在[!DNL ChatGPT]中），而且必須透過可開啟的URL進行。 來自[!DNL LLM Apps]的測試錄製是供您自己的檢查使用，不會取代此示範。 錄製示範，將其上傳到檢閱者可存取的位置，並在準備封裝前於&#x200B;**[!UICONTROL 裝載的示範URL]**&#x200B;中輸入其URL。

## 準備封裝

在&#x200B;**[!UICONTROL 準備您的封裝]**&#x200B;下，檢查&#x200B;**[!UICONTROL 整備]**。 **[!UICONTROL 準備就緒]**&#x200B;表示您儲存的詳細資料符合需求。 否則，區段會列出缺少的內容，例如所需的詳細資訊或成功的生產部署。

1. 閱讀&#x200B;**[!UICONTROL 準備時會發生什麼情況]**。 [!DNL LLM Apps]會建立封裝，並將測試要求傳送至您部署的應用程式。 這可能需要幾分鐘的時間，而且測試請求可能會出現在您應用程式的分析中。
2. 選取&#x200B;**[!UICONTROL 我瞭解這將呼叫我部署的應用程式以準備檢閱證據。]** 核取方塊。
3. 選取&#x200B;**[!UICONTROL 準備封裝]**。

![準備您的封裝 — 準備就緒、確認核取方塊和準備封裝](/help/assets/guide-submission/submission-prepare.png)

準備執行時，對話方塊顯示&#x200B;**[!UICONTROL 準備進行中]**。 [!DNL LLM Apps]使用您選取&#x200B;**[!UICONTROL 準備封裝]**&#x200B;時的應用程式和詳細資料。

![準備進行中 — 進度列和開始時間](/help/assets/guide-submission/submission-progress.png)

您可以關閉對話方塊，稍後再回來。 準備作業會在背景中繼續，而且應用程式頁面上的卡片會顯示&#x200B;**[!UICONTROL 正在準備封裝]**，直到它完成為止。 如果準備失敗，則會保留您儲存的詳細資料。

## 檢閱結果

準備完成時，請開啟&#x200B;**[!UICONTROL 結果]**。 區段頂端會總結結果。 **[!UICONTROL 準備工作需要注意]**&#x200B;表示[!DNL LLM Apps]已建立草稿套件，但發現您必須先修正的問題，例如預留位置法律連結。

![結果 — 準備摘要、下載報告、下載封裝和下載錄製](/help/assets/guide-submission/submission-results.png)

- **[!UICONTROL 封裝與報告]** — 選取&#x200B;**[!UICONTROL 下載封裝]**&#x200B;以取得提交ZIP，並選取&#x200B;**[!UICONTROL 下載報告]**&#x200B;以取得準備報告。 **[!UICONTROL 下載封裝]**&#x200B;只有在準備產生完整封裝時才能使用。
- **[!UICONTROL 檢閱錄製]** — 選取「**[!UICONTROL 下載錄製]**」並觀看影片，確認您的應用程式是否如預期般運作。 一個影片可以包含數個測試案例。
- **[!UICONTROL 測試案例]** — 針對每個測試案例，檢查已傳送的要求及其狀態。 開啟&#x200B;**[!UICONTROL 預期行為]**，檢視應用程式應該做什麼。 有些測試案例會詢問您應用程式不支援的問題。 對於上述情況，應用程式應拒絕要求，且不會呼叫動作。

![測試案例 — 包含其請求、預期行為和記錄狀態的檢閱案例](/help/assets/guide-submission/submission-test-cases.png)

錄製來自[!DNL LLM Apps]測試環境。 這並不表示[!DNL ChatGPT]已驗證您的應用程式或OpenAI已核准它。

**[!UICONTROL 在您提交之前]**&#x200B;會列出您仍需確認或輸入的詳細資料，例如套件版本、應用程式是否涉及購買、裝載的示範URL、支援的國家/地區以及發行說明。 在清單下方，每個檢查都會顯示&#x200B;**通過**、**警告**&#x200B;或&#x200B;**失敗**。 展開檢查以檢視詳細資料。 若要修正詳細資料，請選取&#x200B;**[!UICONTROL 更新詳細資料]**，儲存您的變更，然後再次準備封裝。

![在您提交之前 — 剩餘的詳細資料、更新詳細資料，以及封裝檢查](/help/assets/guide-submission/submission-before-submit.png)

如果您在準備後變更應用程式或詳細資料，**[!UICONTROL 結果]**&#x200B;會顯示自此回合&#x200B;**以來變更的**&#x200B;應用程式或輸入。 選取&#x200B;**[!UICONTROL 再次準備]**&#x200B;以從目前應用程式建立新的套件和詳細資料。 **[!UICONTROL 執行歷程記錄]**&#x200B;列出先前的準備工作。 開啟不會變更您已儲存的詳細資料。

## 在OpenAI入口網站中提交

當封裝完成時，請下載它，然後依照OpenAI檔案中的[上傳並提交您的外掛程式](https://developers.openai.com/plugins/deploy/submission)至：

1. 上傳ZIP並修正自動檢查發現的任何問題。
2. 連線您的MCP伺服器並完成網域驗證。
3. 輸入稽核資訊，包括檢閱者的登入詳細資料（若您的應用程式需要登入），然後提交應用程式以供檢閱。
4. 發佈核准的外掛程式。

若要驗證您的網域：

1. 上傳套件後，複製OpenAI入口網站顯示的挑戰Token。
2. 在[!DNL LLM Apps]中，開啟應用程式的&#x200B;**[!UICONTROL 設定]** > **[!UICONTROL 提交]**。
3. 在&#x200B;**[!UICONTROL 網域驗證]**&#x200B;下，將權杖貼到&#x200B;**[!UICONTROL OpenAI挑戰權杖]**&#x200B;中，並選取&#x200B;**[!UICONTROL 儲存]**。
4. [再次將您的應用程式](/help/guides/deploy-your-app.md)部署至生產環境。 Token只會在下次部署後發佈。
5. 返回OpenAI入口網站並完成驗證。

[!DNL LLM Apps]會在您應用程式網域上的`/.well-known/openai-apps-challenge`提供權杖，因此您不需要自行託管。

### 順利檢閱的秘訣

- **檢查每個動作的驗證。** 當OpenAI入口網站掃描您的MCP伺服器時，會呼叫每個動作。 如果動作標示為需要驗證，但無法驗證呼叫，則掃描會失敗。 接著，您需要修正應用程式、準備新套件，然後再次上傳。
- **等待檢閱時間。** 提交之後，OpenAI會先檢閱您的應用程式，您才能發佈，並透過電子郵件傳送意見。 在您規劃發行日期時，請將這段時間列入考量。
- **清單詳細資訊變更時上傳新封裝。** 發佈後，[!DNL ChatGPT]會在您重新部署時自動擷取您對MCP伺服器的變更。 變更清單詳細資料或其他封裝中繼資料需要新封裝。 在[!DNL LLM Apps]中再次準備並在OpenAI入口網站中上傳。

## 相關

- [部署您的應用程式](/help/guides/deploy-your-app.md)
- [將您的LLM應用程式測試為ChatGPT外掛程式](/help/guides/test-in-chatgpt.md)
- [設定ChatGPT擴充功能](/help/guides/chatgpt-extensions.md)
