---
title: 應用程式如何連線
description: 更仔細地瞭解您擁有的片段（動作中繼資料、處理常式程式碼和Widget）如何在建置階段和執行階段整合到一個執行中的LLM應用程式中。
source-git-commit: 2f3480b3667a6ab7c4ed65b999eed4638c383edb
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 0%

---


# 應用程式如何連線 {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps]目前在Beta中。
>
>此處顯示的功能、工作流程和UI不一定代表產品的最終狀態。 若要加入Beta，請傳送電子郵件至llm-apps-beta@adobe.com。

## 在一個句子中

**LLM應用程式**&#x200B;是您發佈至單一端點的一組&#x200B;**動作** （每個動作都是透過&#x200B;**模型內容通訊協定**&#x200B;或&#x200B;**MCP**&#x200B;公開的工具）。 像[!DNL ChatGPT]這樣的聊天主機會探索這些工具、在交談中呼叫這些工具，並轉譯含有結果的&#x200B;**互動式Widget** — 直接在聊天中進行。

## 整個佈線，建置→執行

**圖表1 — 建置時間。** 您擁有三個不同的表面；平台會將它們融合成一個可部署的應用程式。

```
┌─────────────────────┐   ┌─────────────────────┐   ┌─────────────────────┐
│ 1  LLM Apps UI      │   │ 2  Action Handler   │   │ 3  Widget repo      │
│                     │   │    repo             │   │                     │
│ Create, edit, and   │   │                     │   │ Each widget is an   │
│ manage your action  │   │ Business logic —    │   │ EDS block,          │
│ definitions here    │   │ built from our      │   │ published to a      │
│ (metadata)          │   │ boilerplate         │   │ public URL on       │
│                     │   │                     │   │ *.aem.page          │
│                     │   │ Returns content     │   │                     │
│                     │   │ (for the LLM) +     │   │                     │
│                     │   │ structuredContent   │   │                     │
│                     │   │ (for the widget)    │   │                     │
└─────────────────────┘   └─────────────────────┘   └─────────────────────┘
           │                         │                         │
           └─────────────────────────┼─────────────────────────┘
                                     ▼
                       ┌─────────────────────────────┐
                       │ LLM Apps deploy pipeline    │
                       │ Combines the 3 surfaces     │
                       │ into one running app        │
                       └─────────────────────────────┘
                                     │
                                     ▼
                 ┌─────────────────────────────────────────┐
                 │ ONE MCP server on Adobe I/O Runtime     │
                 │ https://<ns>.adobeioruntime.net/.../mcp │
                 └─────────────────────────────────────────┘
```

- **LLM應用程式UI** — 您可在此建立、編輯及管理每個動作的定義：其&#x200B;**程式碼識別碼** （您在此設定一次的固定概要，例如`my_action`，此固定概要將整個UI、處理常式和Widget中的這個相同動作繫結在一起）、說明、輸入結構描述、Widget選擇和CSP/visibility旗標。 沒有程式碼。
- **動作處理常式存放庫** — 您編寫商業邏輯的伺服器端存放庫（以我們的範本為基礎）。 每個處理常式函式會傳回兩個專案： `content` （LLM *讀取的純文字）和`structuredContent` （* Widget *讀取的資料物件）。*
- **Widget存放庫** — 每個Widget以區塊形式存在並發佈至公用`*.aem.page` URL的EDS存放庫。 每個區塊都使用[`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk)，這是Widget與主機/伺服器之間的橋接器。 它實作簡單API後面的&#x200B;**MCP應用程式規格** （基礎通訊協定），而且它將LLM主機本身抽象化，因此相同的Widget可在[!DNL ChatGPT]、[!DNL Claude]、Gemini或任何其他MCP主機中不受修改地運作。

**圖表2 — 執行階段。** 一旦該伺服器上線，使用者傳送的每則訊息會發生什麼情況。 以[!DNL ChatGPT]顯示為範例主機 — 任何MCP主機（例如[!DNL Claude]）都會播放相同的順序。

```
┌── ChatGPT  (the MCP host) ──────────────────────────────────────────────┐
│  1  tools/list  >  sees `my_action` + its description + input schema    │
│  2  user asks   >  "I need help with …"                                 │
│  3  model picks >  the description matches -> calls this tool           │
│  4  tools/call  >  { name: "my_action", arguments: {situation, ...} }   │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                 routes by CODE IDENTIFIER  ->  my_action
                                     ▼
┌── Adobe I/O Runtime ────────────────────────────────────────────────────┐
│  actions/my_action/index.js  --  your handler runs                      │
│  returns  { content -> text for the model , structuredContent -> data } │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌── Rendered inside the conversation ─────────────────────────────────────┐
│  5  render      >  ChatGPT renders the Widget repo's EDS block          │
│  The EDS block reads the structuredContent the Action Handler repo      │
│  returned, and draws the interactive card — live, inside the chat.      │
└─────────────────────────────────────────────────────────────────────────┘
```
