---
title: 先決條件
description: 在Adobe LLM應用程式Beta上線工作階段之前需要設定哪些專案。
source-git-commit: 1ff383dff82068f68746d665d079216375ba523a
workflow-type: tm+mt
source-wordcount: '530'
ht-degree: 2%

---


在使用Adobe上線工作階段之前，請確認您已具備下列條件。 如果可能，請執行以下驗證步驟 — 結果會告訴您哪些人需要待在會議室中，而不是您是否可以繼續。

## Adobe Developer Console

您需要存取具有您Adobe IMS組織中&#x200B;**開發人員**&#x200B;角色（或&#x200B;**系統管理員**&#x200B;角色）的[Adobe Developer Console](https://developer.adobe.com/console)。 確保您的組織可以存取[[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/)。

若要驗證，請移至[developer.adobe.com/console](https://developer.adobe.com/console)。 如果您看見「快速入門」畫面，表示您的許可權設定正確。

![Adobe Developer Console — 確認開發人員存取的快速入門畫面](/help/assets/overview/dev-console-access-granted.png)

如果您看到&#x200B;**限制存取**&#x200B;訊息，則表示您沒有開發人員角色。 邀請您的IMS組織管理員加入入門工作階段。

![Adobe Developer Console — 限制存取訊息](/help/assets/overview/dev-console-access-denied.png)

## [!DNL GitHub]

您需要在組織中擁有下列許可權的[!DNL GitHub]帳戶：

- **建立存放庫** — 您需要在組織中建立兩個存放庫：一個用於應用程式程式碼，另一個用於EDS專案。 若要確認，請移至[github.com/new](https://github.com/new) — 如果您可從&#x200B;**擁有者**&#x200B;下拉式清單中選取您的組織，則表示您擁有許可權。

  ![GitHub新存放庫擁有者下拉式清單，顯示組織選擇](/help/assets/overview/github-repo-owner-dropdown.png)

- **安裝[!DNL GitHub]應用程式** — 您需要適當的許可權，才能在您的組織上安裝[!DNL GitHub]應用程式。 請參閱[安裝GitHub應用程式的需求](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app)。

**在您上線工作階段之前驗證您的許可權**

在與Adobe會面之前執行此快速檢查。 結果會告訴您哪些人需要待在會議室，而非您是否可以繼續。

1. 移至[github.com/new](https://github.com/new)，選取您的組織作為擁有者，並建立名為`llm-apps-test`的存放庫。
2. 移至[Adobe LLM Apps許可權檢查程式](https://github.com/apps/adobe-llm-apps-permission-checker/installations/new)安裝頁面，並僅針對`llm-apps-test`存放庫安裝應用程式。

| 結果 | 其含義 | 動作 |
|---|---|---|
| 兩個步驟都成功 | 您擁有必要的許可權 | 您已準備好上線工作階段 |
| 步驟2顯示&#x200B;**要求**，而非&#x200B;**安裝** | 您沒有安裝[!DNL GitHub]應用程式的許可權 | 邀請您的[!DNL GitHub]組織管理員加入入門會議 |

完成後，請刪除`llm-apps-test`存放庫並從您的組織設定中解除安裝許可權檢查程式應用程式。

## 具有[!DNL Edge Delivery Services]的AEM Sites

動作Widget託管於&#x200B;**Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**。 您的組織需要包含[!DNL Edge Delivery Services]的AEM Sites授權。 您的EDS組織中必須有&#x200B;**管理員**&#x200B;角色。

若要驗證，請移至[EDS使用者管理工具](https://tools.aem.live/tools/user-admin/index.html)，輸入您的組織名稱，將&#x200B;**網站**&#x200B;留白，然後按一下&#x200B;**擷取使用者**。 在清單中尋找您的帳戶，並確認它顯示&#x200B;**管理員**&#x200B;徽章。

![EDS使用者管理員工具顯示具有管理員角色的使用者](/help/assets/overview/eds-user-admin.png)

如果您還沒有EDS組織，則不需要採取任何動作 — 系統會在上線流程中為您建立一個組織。

## LLM Platform （用於測試）

若要測試您部署的應用程式，您需要支援的訂閱層級，以允許自訂MCP應用程式和啟用&#x200B;**開發人員模式**。 例如，[!DNL ChatGPT]需要&#x200B;**Pro**、**Business**&#x200B;或&#x200B;**Enterprise / Edu**&#x200B;訂閱。
