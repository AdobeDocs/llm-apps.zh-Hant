---
title: 基礎架構設定
description: 使用LLM應用程式受管理的基礎結構或您自己的存放庫新增到Cloud Manager，建立Adobe LLM應用程式。
source-git-commit: 9b2ec369baba5d533088d2dd4adefcb80f5c9727
workflow-type: tm+mt
source-wordcount: '1947'
ht-degree: 1%
---

# 基礎架構設定 {#create-first-app}

>[!IMPORTANT]
>
>所有AEM Sites Cloud Service客戶都可使用LLM應用程式來開發及管理自己的應用程式。 一旦計數開始，每個應用程式呼叫都會計入客戶的AEM Sites內容請求。 Adobe會在計量生效前宣告計量速率和開始日期。

基礎架構是您應用程式的技術基礎：儲存其程式碼的存放庫，以及執行其動作處理常式並將其Widget傳送給使用者的服務。

如[總覽](/help/overview/overview.md#key-concepts)中所述，**動作處理常式**&#x200B;會執行工作，例如尋找產品，而&#x200B;**Widget**&#x200B;會將結果呈現為交談中的互動式體驗。

[!DNL LLM Apps]會將處理常式和Widget原始程式碼儲存在兩個不同的存放庫中：

- **動作存放庫** — 包含伺服器端處理常式及其測試。
- **Widget存放庫** — 包含互動式Widget的程式碼和樣式。

例如，產品搜尋處理常式會擷取相符的產品，而其Widget會將它們顯示為產品卡。 存放庫會儲存該程式碼；Adobe I/O Runtime會執行已部署的處理常式，而Edge Delivery Services則會提供Widget。

安裝精靈會準備這些存放庫，並連線應用程式所需的服務。 從兩個選項中選擇一個：

- **[!DNL LLM Apps]受管理基礎結構**&#x200B;是最快的開始方式。 Adobe會為您的動作處理常式和Widget建立並託管存放庫，因此您不需要自行設定存放庫。
- **您自己的存放庫**&#x200B;會將您應用程式的程式碼保留在您擁有的存放庫中，新增到[!DNL Cloud Manager]。 它們可以託管於[!DNL GitHub]、[!DNL GitHub Enterprise]、[!DNL GitLab]、[!DNL Bitbucket]或[!DNL Azure DevOps]。 平台會使用必要的專案檔案來初始化空白的存放庫，因此您不需要預先準備動作或Widget程式碼。

**在本指南中：**&#x200B;瞭解如何選擇基礎結構選項，並使用安裝精靈建立您的應用程式。 安裝之後，繼續[建立動作](/help/guides/creating-actions.md)。

建立應用程式&#x200B;**不會**&#x200B;產生啟動動作。 從應用程式頁面個別開始產生動作。

## 開啟建立應用程式精靈

開啟[Adobe LLM應用程式](https://experience.adobe.com/llm-apps)。 從歡迎頁面，選取&#x200B;**[!UICONTROL 開始使用]**。 如果您已經有開啟的應用程式，請使用應用程式選擇器，然後選取&#x200B;**[!UICONTROL 建立新的應用程式]**。

**[!UICONTROL 建立您的LLM應用程式]**&#x200B;精靈從&#x200B;**[!UICONTROL 開始]**。 選擇如何設定技術環境。

![建立您的LLM應用程式 — 開始使用讓我們處理它並使用我的Cloud Manager設定；紅色的大綱標籤自己的存放庫選項](/help/assets/guide-infrastructure/setup-choice.png)

| 基礎架構選項 | 在精靈中選取 | 其含義 |
|---|---|---|
| **[!DNL LLM Apps]受管理基礎結構** | **[!UICONTROL 讓我們處理它]** | Adobe會為您的應用程式建立和管理存放庫和連線服務。 程式碼存取權可應要求提供。 |
| **您自己的存放庫** | **[!UICONTROL 使用我的Cloud Manager設定]** | 您選取兩個新增至[!DNL Cloud Manager]的存放庫：一個用於動作處理常式，另一個用於Widget。 如果它們是空的，則平台會將其初始化。 |

這兩個選項都支援稍後自動產生動作。 這兩個選項都不需要您手動寫入動作。

## 使用[!DNL LLM Apps]個受管理基礎結構 {#adobe-managed-infrastructure}

使用此選項可讓Adobe為您的應用程式設定和管理存放庫和連線服務。 您不需要自行建立存放庫或安裝[!DNL GitHub]個App。

1. 選取&#x200B;**[!UICONTROL 讓我們處理它]**。
2. 選取&#x200B;**[!UICONTROL 下一步]**。
3. 完成[應用程式詳細資料](#app-details)。
4. 選取&#x200B;**[!UICONTROL 建立應用程式]**。

在Adobe準備存放庫並連線Widget網站時，應用程式頁面會顯示&#x200B;**[!UICONTROL 設定您的應用程式]**。 UI指出設定通常需要1-2分鐘；在設定就緒之前，動作、測試和部署仍無法使用。

![應用程式設定 — Adobe正在準備應用程式基礎結構](/help/assets/guide-infrastructure/provisioning.png)

如果安裝失敗，請讀取錯誤並選取&#x200B;**[!UICONTROL 重試]**。 如果頁面無法檢查進度，安裝程式會在背景中繼續；請選取&#x200B;**[!UICONTROL 再次檢查]**&#x200B;以重新連線。

安裝程式完成時，請繼續[建立動作](/help/guides/creating-actions.md)。

## 使用您自己的存放庫 {#your-own-repositories}

選取&#x200B;**[!UICONTROL 使用我的Cloud Manager設定]**，然後選取&#x200B;**[!UICONTROL 下一步]**。 此路徑包含四個精靈步驟：

**開始執→動作基礎結構設定→Widget基礎結構設定→應用程式詳細資料。**

[!DNL LLM Apps]使用您的Adobe帳戶透過[!DNL Cloud Manager]到達您的存放庫。 您未安裝[!DNL GitHub]應用程式。

### 開始之前 {#own-repositories-prerequisites}

- 存取應用程式所屬的[!DNL Cloud Manager]程式。
- 兩個存放庫，一個用於動作處理常式，另一個用於Widget。 空白的存放庫沒有問題，包括僅包含其Git主機建立的README的存放庫。
- 兩個存放庫[都已新增至Cloud Manager](https://experienceleague.adobe.com/zh-hant/docs/experience-manager-cloud-manager/content/managing-code/external-repositories)作為私人存放庫，其擁有權已驗證，且狀態為&#x200B;**就緒**。 [!DNL Cloud Manager]必須能夠寫入它們；唯讀存取權杖是不夠的。

只會列出您自行託管的存放庫。 不支援[!DNL Cloud Manager]中由Adobe託管的存放庫。

#### 您的Widget存放庫是Edge Delivery Services網站 {#widget-repository-is-an-eds-site}

Widget是從[!DNL Edge Delivery Services] (EDS)網站提供，您的Widget存放庫儲存該網站的程式碼。 確定Widget存放庫屬於EDS網站，或將會屬於。 有兩種方式可設定一個：

- **任何Git提供者，請自備Git。** 在[!DNL Cloud Manager]中建立EDS網站，並使用&#x200B;**自備Git**&#x200B;將其連結到您的存放庫。 [!DNL Cloud Manager]然後將存放庫的程式碼同步至網站。 請在啟動精靈之前執行此動作。 參閱[自備Git](https://www.aem.live/developer/byo-git)。
- **具有AEM程式碼同步的[!DNL GitHub]存放庫。** 自行設定EDS網站，如同[Edge Delivery Services教學課程](https://www.aem.live/developer/tutorial)。 精靈會引導您完成安裝AEM Code Sync，以及在驗證存放庫後檢查存取權。

Widget步驟會偵測適用的情況。 請參閱[設定Widget基礎結構](#set-up-the-widget-infrastructure)。

>[!TIP]
>
>即使貴公司已擁有EDS網站，請為您的[!DNL LLM Apps] Widget建立個別的EDS網站。

>[!NOTE]
>
>對於空白的存放庫，驗證會寫入初始專案檔案。 在選取&#x200B;**[!UICONTROL 驗證存放庫]**&#x200B;之前，請選擇此應用程式的存放庫。

### 設定動作基礎結構

動作存放庫包含當LLM平台叫用動作時所執行的伺服器端處理常式與測試。

1. 在&#x200B;**[!UICONTROL Cloud Manager程式]**&#x200B;下，選取包含動作存放庫的程式。
2. 在&#x200B;**[!UICONTROL Cloud Manager存放庫]**&#x200B;下，選取動作存放庫。 如果遺失該清單，請在[!DNL Cloud Manager]中新增該清單，並附上&#x200B;**[!UICONTROL 前往Cloud Manager]**，然後重新整理清單。
3. 選取&#x200B;**[!UICONTROL 驗證存放庫]**。 驗證會使用Adobe LLM應用程式範本初始化空白的存放庫。 現有的存放庫必須包含相容的動作專案。
4. 當顯示&#x200B;**[!UICONTROL 已驗證的存放庫]**&#x200B;時，請選取&#x200B;**[!UICONTROL 下一步]**。

![動作基礎結構設定 — 已選取Cloud Manager程式和存放庫，已驗證存放庫](/help/assets/guide-infrastructure/action-repository.png)

平台會將產生的動作程式碼推送至此存放庫的分支。 選取&#x200B;**[!UICONTROL 標籤為已檢閱]**&#x200B;會合併分支。 請參閱[建立動作](/help/guides/creating-actions.md)。

### 設定Widget基礎結構 {#set-up-the-widget-infrastructure}

Widget是附加至動作（例如產品卡片或預訂表單）的可選視覺回應。 Edge Delivery Services (EDS)會從使用您Widget存放庫的網站提供這些服務。

1. 在&#x200B;**[!UICONTROL Widget來源]**&#x200B;下，選取&#x200B;**[!UICONTROL 手動選取Widget存放庫]**。

   ![Widget基礎架構設定 — 手動選取Widget存放庫的Widget來源](/help/assets/guide-infrastructure/widget-source.png)

2. 在&#x200B;**[!UICONTROL Cloud Manager程式]**&#x200B;底下，選取包含介面工具儲存庫的程式。 它可能與動作存放庫的程式不同。
3. 在&#x200B;**[!UICONTROL Edge Delivery Services存放庫]**&#x200B;下，選取Widget存放庫。 請勿選取動作存放庫。
4. 選取&#x200B;**[!UICONTROL 驗證存放庫]**。 驗證會使用AEM範本初始化空的存放庫。 現有的存放庫必須是相容的EDS專案。

後續動作取決於託管Widget存放庫的位置。

#### 其他提供者上的Widget存放庫（自備Git） {#widget-repository-byog}

對於[!DNL GitLab]、[!DNL Bitbucket]、[!DNL Azure DevOps]或[!DNL GitHub Enterprise]上的存放庫，驗證會找到[!DNL Cloud Manager]連結到存放庫的EDS網站。 精靈會在&#x200B;**[!UICONTROL Edge Delivery Services網站]**&#x200B;下顯示網站。

![Widget基礎架構設定 — Widget存放庫已驗證，且Cloud Manager將其同步至](/help/assets/guide-infrastructure/widget-repository.png)的Edge Delivery Services網站

[!DNL Cloud Manager]會將存放庫的程式碼同步至該網站，因此您不需要AEM程式碼同步。 選取&#x200B;**[!UICONTROL 下一步]**。

如果驗證報告未連結任何網站，請使用&#x200B;**自備Git**&#x200B;將存放庫連結到[!DNL Cloud Manager]中的EDS網站，然後再次驗證。 參閱[自備Git](https://www.aem.live/developer/byo-git)。

#### GitHub上的Widget存放庫 {#widget-repository-github}

[!DNL GitHub]存放庫使用AEM程式碼同步來發佈其程式碼。 驗證後，精靈會顯示&#x200B;**[!UICONTROL 安裝AEM程式碼同步]**。 使用相同的Adobe帳戶來存取[!DNL LLM Apps]、AEM網站存取權和DA寫入存取權。

##### 設定AEM網站存取權 {#aem-site-access}

**新安裝：**&#x200B;選取&#x200B;**[!UICONTROL 安裝AEM程式碼同步]**，並授與它存取Widget存放庫的許可權。 完成安裝確認頁面上的安裝精靈，將自己新增為網站管理員。

**現有的EDS網站：**&#x200B;請保持您的AEM程式碼同步安裝，並確認其涵蓋選取的Widget存放庫。 請要求現有網站管理員在`https://tools.aem.live/tools/user-admin/index.html/?org=<org>&site=<site>`開啟AEM使用者管理員，並以`admin`角色新增您的Adobe帳戶。 您不需要重新安裝Code Sync。

將`<org>`和`<site>`取代為您的AEM組織和網站名稱。 您的網站管理員可以確認這些值。

##### 授予DA寫入許可權 {#da-write-access}

針對新網站和現有網站，請要求DA組織管理員：

1. 在`https://da.live/config#/<org>/`開啟DA設定，將`<org>`取代為儲存網站內容的DA組織。
2. 開啟&#x200B;**許可權**&#x200B;工作表。 如果工作表不存在，請選取&#x200B;**[!UICONTROL 新增工作表]**，將其命名為`permissions`，並新增欄標題&#x200B;**路徑**、**群組**&#x200B;和&#x200B;**動作**。 您也可以新增&#x200B;**註解**&#x200B;欄來說明每個規則。
3. 新增授與您Adobe帳戶寫入網站中所有內容的存取權的資料列：在&#x200B;**路徑**&#x200B;中使用網站的內容路徑，**群組**&#x200B;中使用您的Adobe帳戶電子郵件，並在&#x200B;**動作**&#x200B;中使用`write`。 儲存設定。

   ![DA許可權表，顯示群組資料欄中的範例帳戶，以及動作資料欄中的寫入許可權](/help/assets/guide-infrastructure/da-write-permissions.png)

   熒幕擷圖使用虛構的帳戶。 將`widget-author@example.com`取代為您的Adobe帳戶電子郵件，並使用您網站的內容路徑，而非整個組織的`/ + **`範例。 `CONFIG`列是組態管理員的專用資料列；您不需要變更它即可授與內容存取權。

AEM網站角色不授予DA寫入許可權。 您不需要整個組織的寫入許可權。

如需設定DA許可權的詳細資訊，請參閱[許可權和安全性](https://www.aem.live/docs/ew/administering/permissions)。

##### 驗證AEM設定 {#validate-aem-setup}

1. 返回[!DNL LLM Apps]並選取&#x200B;**[!UICONTROL 驗證AEM設定]**。
2. 當顯示&#x200B;**[!UICONTROL AEM安裝程式已驗證]**&#x200B;時，請選取&#x200B;**[!UICONTROL 下一步]**。

如果驗證失敗，請依照訊息檢查[AEM網站存取權](#aem-site-access)或[DA寫入存取權](#da-write-access)，然後再次驗證。

>[!NOTE]
>
>精靈將&#x200B;**[!UICONTROL 使用Cloud Manager Edge Delivery Services]**&#x200B;標籤為&#x200B;**[!UICONTROL 即將推出]**，不允許選取。 使用手動Widget存放庫選項。

## 輸入應用程式詳細資料 {#app-details}

兩個基礎結構選項都已完成，並顯示&#x200B;**[!UICONTROL 應用程式詳細資料]**。

![應用程式詳細資料 — 輸入的LLM應用程式名稱、從Cloud Manager存放庫設定的AEM程式，以及Application Insights資料區域](/help/assets/guide-infrastructure/app-details.png)

1. 輸入&#x200B;**[!UICONTROL LLM應用程式名稱]**。 使用可識別品牌或體驗的名稱，例如&#x200B;*Frescopa咖啡*。
2. 選取應用程式所屬的&#x200B;**[!UICONTROL AEM程式]**。 如果您只能存取一個程式，精靈會自動選取該程式。 使用您自己的存放庫時，程式是動作存放庫的程式，不能在此變更。
3. 在&#x200B;**[!UICONTROL 應用程式見解資料區域]**&#x200B;下，選擇&#x200B;**[!UICONTROL 美國(US)]**&#x200B;或&#x200B;**[!UICONTROL 歐洲(EU)]**。
4. 選取&#x200B;**[!UICONTROL 建立應用程式]**。

>[!IMPORTANT]
>
>應用程式建立後，無法變更AEM程式和應用程式見解資料區域。 區域會決定應用程式的Application Insights資料儲存位置。

如果沒有可用的AEM程式，請向您的管理員要求存取權。 如果載入程式失敗，請選取&#x200B;**[!UICONTROL 重試載入程式]**。

精靈會在建立後開啟應用程式頁面。 使用[!DNL LLM Apps]個受管理的基礎結構，請等待基礎結構設定完成後再繼續。

若要稍後檢查存放庫，請開啟應用程式的&#x200B;**[!UICONTROL 設定]**。 **[!UICONTROL 存放庫]**&#x200B;區段會顯示每個存放庫的程式和名稱，以及針對使用自攜Git的Widget存放庫，其EDS網站和來源。 應用程式建立後，便無法變更存放庫。

![應用程式設定 — 含範本和Edge Delivery Services存放庫的存放庫區段，包括連結的網站和來源](/help/assets/guide-infrastructure/settings-repositories.png)

先前使用Adobe LLM Apps [!DNL GitHub]應用程式建立的應用程式可繼續使用現有的存放庫。

## 下一步：建立動作

您的應用程式現在具有其基礎架構，但尚未產生任何動作。 繼續進行[建立動作](/help/guides/creating-actions.md)以分析網站或選擇手動動作建立。

如需動作中繼資料、處理常式、Widget和已部署的MCP伺服器之間的關係，請參閱[應用程式如何連線在一起](/help/guides/app-architecture.md)。
