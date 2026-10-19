---
title: 基礎架構設定
description: 使用Adobe受管理基礎架構建立Adobe LLM應用程式，或提供兩個空白的GitHub存放庫供平台初始化。
source-git-commit: 14d94a68dabddd2f2ef2cef65c51d31645d4a958
workflow-type: tm+mt
source-wordcount: '1337'
ht-degree: 0%
---

# 基礎架構設定 {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

基礎架構是您應用程式的技術基礎：儲存其程式碼的存放庫，以及執行其動作處理常式並將其Widget傳送給使用者的服務。

如[總覽](/help/overview/overview.md#key-concepts)中所述，**動作處理常式**&#x200B;會執行工作，例如尋找產品，而&#x200B;**Widget**&#x200B;會將結果呈現為交談中的互動式體驗。

[!DNL LLM Apps]會將處理常式和Widget原始程式碼儲存在兩個不同的[!DNL GitHub]存放庫中：

- **動作存放庫** — 包含伺服器端處理常式及其測試。
- **Widget存放庫** — 包含互動式Widget的程式碼和樣式。

例如，產品搜尋處理常式會擷取相符的產品，而其Widget會將它們顯示為產品卡。 存放庫會儲存該程式碼；Adobe I/O Runtime會執行已部署的處理常式，而Edge Delivery Services則會提供Widget。

安裝精靈會準備這些存放庫，並連線應用程式所需的服務。 我們建議您從&#x200B;**Adobe受管理基礎結構**&#x200B;開始，這是最快速的開始方式。 Adobe會為您的動作處理常式和Widget建立並託管存放庫。 您不需要自行設定存放庫。

或者，在您自己的帳戶或組織中提供兩個空白的[!DNL GitHub]存放庫。 平台會使用必要的專案檔案將它們初始化，您不需要預先準備動作或Widget程式碼。

**在本指南中：**&#x200B;瞭解如何選擇基礎結構選項，並使用安裝精靈建立您的應用程式。 安裝之後，繼續[建立動作](/help/guides/creating-actions.md)。

建立應用程式&#x200B;**不會**&#x200B;產生啟動動作。 從應用程式頁面個別開始產生動作。

## 開啟建立應用程式精靈

開啟[Adobe LLM應用程式](https://experience.adobe.com/llm-apps)。 從歡迎頁面，選取&#x200B;**[!UICONTROL 開始使用]**。 如果您已經有開啟的應用程式，請使用應用程式選擇器，然後選取&#x200B;**[!UICONTROL 建立新的應用程式]**。

**[!UICONTROL 建立您的LLM應用程式]**&#x200B;精靈從&#x200B;**[!UICONTROL 開始]**。 選擇如何設定技術環境。

![建立您的LLM應用程式 — 在Adobe受管理基礎結構和連線您自己的存放庫之間選擇](/help/assets/guide-infrastructure/setup-choice.png)

| 基礎架構選項 | 在精靈中選取 | 其含義 |
|---|---|---|
| **Adobe受管理基礎結構** | **[!UICONTROL 讓我們處理它]** | Adobe會為您的應用程式建立和管理存放庫和連線服務。 程式碼存取權可應要求提供。 |
| **您自己的存放庫** | **[!UICONTROL 我將自行設定]** | 您提供兩個空白的GitHub存放庫，並透過精靈授與存取權。 平台會分別初始化一個動作處理常式和一個介面工具集。 |

這兩個選項都支援稍後自動產生動作。 這兩個選項都不需要您手動寫入動作。

## 使用Adobe管理基礎結構 {#adobe-managed-infrastructure}

使用此選項可讓Adobe為您的應用程式設定和管理存放庫和連線服務。 您不需要自行建立存放庫或安裝[!DNL GitHub]個App。

1. 選取&#x200B;**[!UICONTROL 讓我們處理它]**。
2. 選取&#x200B;**[!UICONTROL 下一步]**。
3. 完成[應用程式詳細資料](#app-details)。
4. 選取&#x200B;**[!UICONTROL 建立應用程式]**。

在Adobe準備存放庫並連線Widget網站時，應用程式頁面會顯示&#x200B;**[!UICONTROL 設定您的應用程式]**。 UI指出設定通常需要1-2分鐘；在設定就緒之前，動作、測試和部署仍無法使用。

![應用程式設定 — Adobe正在準備應用程式基礎結構](/help/assets/guide-infrastructure/provisioning.png)

如果安裝失敗，請讀取錯誤並選取&#x200B;**[!UICONTROL 重試]**。 如果頁面無法檢查進度，安裝程式會在背景中繼續；請選取&#x200B;**[!UICONTROL 再次檢查]**&#x200B;以重新連線。

安裝程式完成時，請繼續[建立動作](/help/guides/creating-actions.md)。

## 連線您自己的存放庫 {#your-own-repositories}

選取&#x200B;**[!UICONTROL 我將自行設定]**，然後選取&#x200B;**[!UICONTROL 下一步]**。 此路徑包含四個精靈步驟：

**開始執→動作基礎結構設定→Widget基礎結構設定→應用程式詳細資料。**

您需要存取選取的存放庫和許可權，才能安裝或設定必要的[!DNL GitHub]應用程式。 對於組織擁有的存放庫，管理員可能需要核准存取權。

建立兩個空白的存放庫：一個用於動作處理常式，另一個用於Widget。 請勿使用README、授權或`.gitignore`初始化。 當您驗證精靈中的每個存放庫時，平台會新增所需的專案結構。

如果您已有相容的動作和EDS專案，您可以改為連線這些專案，但不需要現有的程式碼。

>[!NOTE]
>
>對於空白的存放庫，驗證會寫入初始專案檔案。 在選取&#x200B;**[!UICONTROL 驗證存放庫]**&#x200B;之前，請選擇此應用程式的存放庫。

### 設定動作基礎結構

動作存放庫包含當LLM平台叫用動作時所執行的伺服器端處理常式與測試。

1. 在&#x200B;**[!UICONTROL 動作來源]**&#x200B;底下，選取&#x200B;**[!UICONTROL GitHub]**。 其他程式碼來源目前無法使用。
2. 回答&#x200B;**[!UICONTROL 您是否已建立GitHub存放庫？]**：
   - 選取&#x200B;**[!UICONTROL 是，我會使用已建立的存放庫]**&#x200B;來連線相容的現有專案或空的存放庫。
   - 選取&#x200B;**[!UICONTROL 我還沒有存放庫]**，然後選取&#x200B;**[!UICONTROL 在GitHub上建立存放庫]**，以建立空的動作存放庫。 之後請返回精靈。
3. 將存放庫連線至Adobe LLM Apps [!DNL GitHub]應用程式：
   - 如果未連線任何組織，請選取[在GitHub上安裝]。**&#x200B;**
   - 如果組織已經連線，請選取&#x200B;**[!UICONTROL 連線存放庫]**&#x200B;並將存放庫新增到該安裝。
4. 在[!DNL GitHub]上，選擇&#x200B;**[!UICONTROL 僅選取存放庫]**，並僅授與此應用程式使用的存放庫存取權。
5. 返回精靈。 在&#x200B;**[!UICONTROL 選取存放庫]**&#x200B;下，選擇&#x200B;**[!UICONTROL 組織]**&#x200B;和&#x200B;**[!UICONTROL 存放庫]**。
6. 選取&#x200B;**[!UICONTROL 驗證存放庫]**。 驗證會使用Adobe LLM應用程式範本初始化空白的存放庫。 現有的存放庫必須包含相容的動作專案。
7. 驗證成功後，請選取&#x200B;**[!UICONTROL 下一步]**。

![動作基礎結構設定 — 選擇GitHub並連線動作存放庫](/help/assets/guide-infrastructure/action-repository.png)

如果組織或存放庫遺失，請使用&#x200B;**[!UICONTROL 連線其他組織]**&#x200B;或在其選擇器中連線其他存放庫&#x200B;**。**&#x200B;在[!DNL GitHub]上變更存取權後，選取&#x200B;**[!UICONTROL 重新整理組織和存放庫]**。

### 設定Widget基礎結構

Widget是附加至動作（例如產品卡片或預訂表單）的可選視覺回應。 精靈會連線至[!DNL Edge Delivery Services] (EDS)存放庫來主控它們。

1. 在&#x200B;**[!UICONTROL Widget來源]**&#x200B;下，選取&#x200B;**[!UICONTROL 手動選取Widget存放庫]**。
2. 在&#x200B;**[!UICONTROL 程式碼來源]**&#x200B;底下，選取&#x200B;**[!UICONTROL GitHub]**。
3. 選擇您是否已有存放庫。 如果沒有，請選取&#x200B;**[!UICONTROL 在GitHub上建立存放庫]**，建立空白的Widget存放庫，然後返回精靈。
4. 安裝或設定此存放庫的Adobe LLM Apps [!DNL GitHub]應用程式，就像您對動作存放庫所做的一樣。
5. 選取其&#x200B;**[!UICONTROL 組織]**&#x200B;和&#x200B;**[!UICONTROL 存放庫]**。 請勿選取動作存放庫。
6. 選取&#x200B;**[!UICONTROL 驗證存放庫]**。 驗證會使用AEM範本初始化空的存放庫。 現有的存放庫必須是相容的EDS專案。
7. 選取&#x200B;**[!UICONTROL 安裝AEM程式碼同步]**&#x200B;並授予AEM程式碼同步處理存取Widget存放庫的許可權。
8. 在安裝確認頁面上，使用安裝精靈，將自己新增為EDS網站的管理員。
9. 返回[!DNL LLM Apps]並選取&#x200B;**[!UICONTROL 驗證AEM程式碼同步安裝]**。
10. 當已安裝&#x200B;**[!UICONTROL AEM程式碼同步]**&#x200B;出現，請選取&#x200B;**[!UICONTROL 下一步]**。

>[!NOTE]
>
>精靈將&#x200B;**[!UICONTROL 使用Cloud Manager Edge Delivery Services]**&#x200B;標籤為&#x200B;**[!UICONTROL 即將推出]**，不允許選取。 使用手動Widget存放庫選項。

如果驗證未偵測到AEM Code Sync，請確認AEM Code Sync有權存取所選的Widget存放庫，以及您已將自己新增為網站管理員。 然後重新驗證。

## 輸入應用程式詳細資料 {#app-details}

兩個基礎結構選項都已完成，並顯示&#x200B;**[!UICONTROL 應用程式詳細資料]**。

![應用程式詳細資料 — 輸入應用程式名稱、選取AEM程式並選擇分析資料區域](/help/assets/guide-infrastructure/app-details.png)

1. 輸入&#x200B;**[!UICONTROL LLM應用程式名稱]**。 使用可識別品牌或體驗的名稱，例如&#x200B;*Frescopa咖啡*。
2. 選取應用程式所屬的&#x200B;**[!UICONTROL AEM程式]**。 如果您只能存取一個程式，精靈會自動選取該程式。
3. 在&#x200B;**[!UICONTROL Analytics資料區域]**&#x200B;底下，選擇&#x200B;**[!UICONTROL 美國(US)]**&#x200B;或&#x200B;**[!UICONTROL 歐洲(EU)]**。
4. 選取&#x200B;**[!UICONTROL 建立應用程式]**。

>[!IMPORTANT]
>
>應用程式建立後，就無法變更AEM程式和分析資料區域。 分析區域會決定應用程式的分析資料儲存位置。

如果沒有可用的AEM程式，請向您的管理員要求存取權。 如果載入程式失敗，請選取&#x200B;**[!UICONTROL 重試載入程式]**。

精靈會在建立後開啟應用程式頁面。 使用Adobe受管基礎架構，請等待基礎架構設定完成後再繼續。

## 下一步：建立動作

您的應用程式現在具有其基礎架構，但尚未產生任何動作。 繼續進行[建立動作](/help/guides/creating-actions.md)以分析網站或選擇手動動作建立。

如需動作中繼資料、處理常式、Widget和已部署的MCP伺服器之間的關係，請參閱[應用程式如何連線在一起](/help/guides/app-architecture.md)。
