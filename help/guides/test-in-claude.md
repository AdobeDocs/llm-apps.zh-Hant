---
title: 以克勞德聯結器測試您的LLM應用程式
description: 從您的Adobe LLM應用程式MCP伺服器URL建立Claude聯結器，並在交談中測試。
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '676'
ht-degree: 0%
---

# 將您的LLM應用程式測試為[!DNL Claude]聯結器 {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

部署後，您的LLM應用程式會公開MCP伺服器URL。 將此URL新增至[!DNL Claude]作為自訂聯結器，然後測試產生的動作和Widget。

**在本指南中：**&#x200B;瞭解如何將您部署的應用程式新增為[!DNL Claude]聯結器、選擇其登入行為，以及在交談中測試其動作和Widget。

對於具有公開和受保護動作的應用程式，[!DNL Claude]支援延遲登入，直到呼叫受保護的動作為止。 其動作全部需要驗證的應用程式需要先登入才能使用。 請參閱[使用您自己的身分提供者來驗證一般使用者](/help/guides/authentication.md)。

## 計畫需求

使用遠端MCP的自訂聯結器可供免費、Pro、Max、Team和Enterprise方案使用。 免費帳戶支援一個自訂聯結器。 在「專案團隊」和「企業」中，授權擁有者在成員連線組織之前為其新增聯結器。 企業自訂角色也可以授予該許可權。

請參閱[Claude目前的自訂聯結器指示](https://claude.com/docs/connectors/custom/add-unlisted)，以取得計畫與組織控制項。 遠端MCP端點必須可從Claude的基礎結構連線，而不只是從本機瀏覽器連線。

## 複製MCP伺服器URL

在[!DNL LLM Apps]：

1. 開啟「應用程式詳細資料」頁面。
2. 尋找&#x200B;**[!UICONTROL 測試應用程式]**。
3. 在&#x200B;**[!UICONTROL 中繼環境]**&#x200B;下，選取&#x200B;**[!UICONTROL 複製URL]**。

## 新增自訂聯結器

1. 開啟[自訂>聯結器](https://claude.ai/customize/connectors)。 對於全組織聯結器，授權擁有者會改為開啟[組織設定>聯結器](https://claude.ai/admin-settings/connectors)。
2. 選取&#x200B;**[!UICONTROL 新增自訂聯結器]**，或針對組織流程選取&#x200B;**[!UICONTROL 新增]** > **[!UICONTROL 自訂]** > **[!UICONTROL 網頁]**。
3. 如有要求，請輸入聯結器名稱，然後貼上完整的HTTPS MCP伺服器URL。
4. 如果對話方塊有多個步驟，請選取&#x200B;**[!UICONTROL 繼續]**&#x200B;並檢閱偵測到的驗證設定：
   - 已停用驗證的公用應用程式&#x200B;**[!UICONTROL 無法登入]**。
   - **[!UICONTROL 需要時登入]**&#x200B;使用公開及受保護動作的應用程式。
   - **[!UICONTROL 立即登入]**，要求先登入再使用聯結器。
5. 對於OAuth，請選擇您的身分提供者支援的使用者端身分方法。 如果您使用預先註冊的使用者端，則只能在聯結器的OAuth設定中輸入其詳細資訊，而不是在URL或動作中繼資料中。
6. 選取「**[!UICONTROL 新增]**」。 對於組織聯結器，每個成員會從自己的聯結器清單中選取&#x200B;**[!UICONTROL 連線]**。

有些帳戶顯示具有&#x200B;**[!UICONTROL 進階設定]**&#x200B;的單熒幕對話方塊，而有些則顯示多步驟流程。 如果您的配置不同，請使用[目前的平台指示](https://claude.com/docs/connectors/custom/add-unlisted#choose-authentication-settings)。

>[!NOTE]
>
>僅使用來自您信任之開發人員的聯結器。 人類無法控制開發人員提供哪些工具，或保證這些工具能如預期運作或維持不變。

## 允許產生的工具

[!DNL Claude]列出聯結器頁面上&#x200B;**[!UICONTROL 工具許可權]**&#x200B;下每個產生的動作。 新工具預設為&#x200B;**[!UICONTROL 需要核准]**，提示您在測試期間核准每個呼叫。

針對變更資料或執行敏感操作的動作，保持啟用核准提示。 在適合您的測試時，**[!UICONTROL 請一律允許]**&#x200B;僅用於特定受信任的低風險動作。

## 測試聯結器

1. 開始新的聊天。
2. 在訊息方塊中選取&#x200B;**+** （或輸入`/`），將&#x200B;**[!UICONTROL 聯結器暫留]**，然後開啟您為此交談新增的聯結器。
3. 提出符合其中一個產生之動作的問題。 例如： *給我看點咖啡。*

確認：

- [!DNL Claude]會叫用預期的動作。
- Widget會顯示預期的範例資料。
- 文字回應符合Widget。
- Widget控制項如預期般運作。

對於混合式驗證，請先呼叫公開動作而不登入。 然後呼叫受保護的動作，完成登入提示，並確認相同的請求之後會成功。 測試遺失的許可權和無效的輸入。

變更應用程式後，重新部署並重複受影響的測試。 如果您需要變更聯結器的已儲存驗證設定，[!DNL Claude]可能會要求您移除並再次新增聯結器。

## 後續步驟

- [自訂產生的Widget](/help/guides/widgets.md)。
- [從頭開始建立動作](/help/guides/create-action.md)。
