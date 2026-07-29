---
title: 以克勞德聯結器測試您的LLM應用程式
description: 從您的Adobe LLM應用程式MCP伺服器URL建立Claude聯結器，並在交談中測試。
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '399'
ht-degree: 1%

---


# 將您的LLM應用程式測試為[!DNL Claude]聯結器 {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

部署後，您的LLM應用程式會公開MCP伺服器URL。 將此URL新增至[!DNL Claude]作為自訂聯結器，然後測試產生的動作和Widget。

這是建置、自訂或擴充應用程式後的最終驗證步驟。

## 計畫需求

使用遠端MCP的自訂聯結器可在[!DNL Claude]、[!DNL Claude] Desktop和Cowork上免費取得、Pro、Max、Team和Enterprise計畫。 免費計畫帳戶僅限於一個自訂聯結器。 對於「專案團隊」和「企業」組織，「擁有者」或「主要擁有者」必須先啟用聯結器，其他成員才能使用它們。

## 複製MCP伺服器URL

在[!DNL LLM Apps]：

1. 開啟「應用程式詳細資料」頁面。
2. 尋找&#x200B;**[!UICONTROL 測試應用程式]**。
3. 在&#x200B;**[!UICONTROL 中繼環境]**&#x200B;下，選取&#x200B;**[!UICONTROL 複製URL]**。

## 新增自訂聯結器

1. 開啟[claude.ai/new?modal=add-custom-connector](https://claude.ai/new?modal=add-custom-connector#settings/customize-connectors)。 這會直接開啟&#x200B;**[!UICONTROL 新增自訂聯結器]**&#x200B;對話方塊。
2. 輸入：
   - **[!UICONTROL 名稱]** — 聯結器名稱。
   - **[!UICONTROL 遠端MCP伺服器URL]** — 您複製的MCP伺服器URL。
3. 選取「**[!UICONTROL 新增]**」。

   ![Claude — 新增自訂聯結器對話方塊](/help/assets/guide-test-claude/claude-add-custom-connector.png)

>[!NOTE]
>
>僅使用來自您信任之開發人員的聯結器。 人無法控制開發人員提供哪些工具，也無法確認這些工具是否如預期運作，或不會變更。

## 允許產生的工具

每個產生的動作會列在聯結器頁面的&#x200B;**[!UICONTROL 工具許可權]**&#x200B;下。 依預設，新工具設定為&#x200B;**[!UICONTROL 需要核准]**，這會提示您核准測試期間的每個呼叫。

將每個工具（或整個&#x200B;**[!UICONTROL 互動工具]**&#x200B;群組）設定為&#x200B;**[!UICONTROL 永遠允許]**，這樣測試就不會因核准提示而中斷。

![Claude — 將工具許可權設定為[一律允許]](/help/assets/guide-test-claude/claude-tool-permissions.png)

## 測試聯結器

1. 開始新的聊天。
2. 在訊息方塊中選取&#x200B;**+** （或輸入`/`），將&#x200B;**[!UICONTROL 聯結器暫留]**，然後開啟您為此交談新增的聯結器。

   ![Claude — 啟用交談的聯結器](/help/assets/guide-test-claude/claude-enable-connector-chat.png)

3. 提出符合其中一個產生之動作的問題。 例如： *給我看點咖啡。*

確認：

- [!DNL Claude]會叫用預期的動作。
- Widget會顯示預期的範例資料。
- 文字回應符合Widget。
- Widget控制項如預期般運作。

## 後續步驟

- [自訂產生的Widget](/help/guides/widgets.md)。
- [從頭開始建立動作](/help/guides/create-action.md)。
