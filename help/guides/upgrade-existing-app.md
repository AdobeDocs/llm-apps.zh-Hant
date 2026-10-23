---
title: 升級現有應用程式
description: 在啟用Adobe Analytics或Customer Journey Analytics之前，更新舊版Adobe LLM應用程式的執行階段和EDS SDK。
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '793'
ht-degree: 0%
---

# 升級現有應用程式 {#upgrade-existing-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

只有當您的應用程式使用1.2.0之前的`@adobe/llm-apps-runtime`或其EDS Widget使用0.1.13之前的LLM Apps SDK時，才使用此指南。 已符合兩項需求的應用程式可以直接進入[設定Adobe Analytics和Customer Journey Analytics](/help/guides/analytics-setup.md)。

**在本指南中：**&#x200B;瞭解如何更新及驗證舊版應用程式的執行階段及EDS SDK。 然後返回Analytics設定指南，以啟用Analytics並驗證集合。

## 開始之前 {#before-you-begin}

您需要存取處理常式存放庫，以及針對具有EDS Widget的應用程式，存取連結至您應用程式的EDS存放庫。 如果您的應用程式使用Adobe受管基礎架構，請在更新之前要求程式碼存取權。 請參閱[使用Adobe受管理基礎結構](/help/guides/create-app.md#adobe-managed-infrastructure)。

不需要建立其他應用程式。 僅更新支援版本以下的元件。 您可以重複使用符合[Analytics設定需求](/help/guides/analytics-setup.md#setup-checklist)的現有Adobe設定。

對於具有EDS Widget的應用程式，請先發佈並驗證SDK更新，然後合併執行階段更新並重新部署應用程式。 這兩個存放庫更新可以獨立準備，但都必須在測試啟用Analytics的部署之前可用。

## 檢查並更新LLM應用程式動作存放庫 {#upgrade-runtime}

1. 開啟連結至應用程式的處理常式存放庫並安裝其相依性：

   ```bash
   npm install
   ```

1. 檢查已安裝的執行階段：

   ```bash
   npm ls @adobe/llm-apps-runtime
   ```

1. 如果執行階段早於1.2.0，請安裝目前版本：

   ```bash
   npm install @adobe/llm-apps-runtime@latest
   npm ls @adobe/llm-apps-runtime
   ```

   確認安裝的版本是1.2.0或更新版本。 如果您的應用程式已使用最新支援的版本，請保留該版本。

1. 確認執行階段相依性及其解析的版本符合最低需求。 應用程式自己的版本與執行階段相依性不同。
1. 執行存放庫的檢查。 LLM應用程式範本：

   ```bash
   npm test -- --runInBand
   npm run build
   npm run lint
   ```

   在部署之前先調查失敗。 如果原始版本的檢查已經失敗，請將該基準與升級引入的任何失敗分開記錄。 成功的本機建置不會驗證分析集合。

1. 認可相依性更新及其鎖定檔，然後將更新推送並合併至處理常式存放庫的預設分支，通常是`main`。 部署該分支的複製。 僅存在於本機或未合併特徵分支上的更新不會包含在部署中。

   當鎖定檔存在時，部署會安裝具有`npm ci`的相依性，這會使用記錄的版本。 重新部署不會自動升級執行階段。 在重新部署之前，請確認GitHub中預設分支上的升級相依性和解析版本。

## 檢查並更新LLM應用程式Widget存放庫 {#upgrade-sdk}

若是具有EDS Widget的應用程式，請檢查EDS存放庫中的SDK以及伺服器執行階段。 更新一個存放庫不會更新另一個存放庫。

有些EDS存放庫包含複製的SDK指令碼，但不具有npm相依性。 瀏覽器會直接載入已提供的指令碼，因此缺少套件專案並不表示缺少SDK。 使用您專案的SDK更新程式，重新整理複製的SDK發佈。 將SDK維護為複製指令碼的專案不需要新增永久性npm相依性。

對於透過npm管理SDK的專案：

1. 從EDS存放庫根目錄中，安裝目前的SDK並確認其版本：

   ```bash
   npm install --save-dev @adobe/llmapps-sdk@latest
   npm ls @adobe/llmapps-sdk
   ```

   使用0.1.13或更新版本。 如果您的專案已使用最新支援的版本，請保留此版本。

1. 確認安裝已重新整理提供的SDK發佈。 安裝掛接會覆寫複製的SDK指令碼，因此在認可前請先檢閱任何本機自訂。 只更新相依性宣告不會更新EDS提供的指令碼。 請參閱[安裝LLM Apps SDK](/help/guides/bring-your-own-eds.md#install-the-llm-apps-sdk)。
1. 執行EDS儲存庫的檢查，例如`npm run lint`，並檢查現有的Widget是否仍會呈現互動並回應。
1. 認可重新整理的SDK發佈以及任何相依性和鎖定檔案更新。 EDS會提供複製的指令碼，而非直接安裝安裝的npm套件。

在任一更新方法之後，將更新推播並合併至動作的&#x200B;**[!UICONTROL 指令碼URL]**&#x200B;所使用的EDS分支。 確認URL提供更新的SDK且其支援程式庫已成功載入。 如果URL使用您的EDS即時網站，請完成該網站的發佈步驟。

## 下一步 {#next-step}

返回[設定Adobe Analytics和Customer Journey Analytics](/help/guides/analytics-setup.md)。 所有應用程式都必須啟用Analytics並部署以收集事件；僅升級執行階段或SDK不會啟用收集。

儲存Analytics設定後，先部署到Stage和[驗證新的action-call和Widget-interaction事件](/help/guides/analytics-setup.md#verify)，然後再部署到生產環境。 確認動作和Widget仍然有效，且單一連結選取範圍會產生一個Widget互動事件。

Analytics會在啟用Analytics的部署後開始收集新的互動。 升級不會重新建構啟用集合前發生的互動。
