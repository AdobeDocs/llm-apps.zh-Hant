---
title: 應用程式如何連線
description: 更仔細地瞭解您擁有的片段（動作中繼資料、處理常式程式碼和Widget）如何在建置階段和執行階段整合到一個執行中的LLM應用程式中。
source-git-commit: e066f66b37914e2f747176e865e26dcc074bff20
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

**LLM應用程式**&#x200B;是一組&#x200B;**動作** （每個動作都是在&#x200B;**模型上公開的工具）
您發佈至單一端點的內容通訊協定&#x200B;**&#x200B;或&#x200B;**&#x200B;MCP**)。 聊天主機
例如[!DNL ChatGPT]會探索這些工具、在對話中呼叫這些工具，並轉譯
含有結果的&#x200B;**互動式Widget** — 直接在聊天室中。

## 整個佈線，建置→執行

**圖表1 — 建置時間。** 您擁有三個獨立的曲面；平台熔絲
將它們整合到一個可部署的應用程式中。

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

- **LLM應用程式UI** — 您可在此建立、編輯及管理每個動作的定義：
其&#x200B;**代碼識別碼** (您在此處設定過一次的固定概要，例如`my_action`，
跨UI、處理常式和Widget將此相同動作繫結在一起)，
說明、輸入結構、Widget選擇和CSP/可見度標幟。 沒有程式碼。
- **動作處理常式存放庫** — 伺服器端存放庫（從我們的範本中建立）
何處撰寫商業邏輯。 每個處理常式函式會傳回兩個專案：
  `content` （純文字，*LLM*&#x200B;讀取）和`structuredContent` (資料物件
  *Widget*&#x200B;讀取)。
- **Widget存放庫** — 每個Widget以區塊形式存在並取得的EDS存放庫
已發佈至公用`*.aem.page` URL。 每個區塊使用
  [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk)，
Widget與主機/伺服器之間的Bridge。 它會實作**MCP應用程式
規格** — 基礎通訊協定 — 在簡單API之後，而且
會抽象化LLM主機本身，因此相同的Widget可在未經修改的情況下運作。
  [!DNL ChatGPT]、[!DNL Claude]、Gemini或任何其他MCP主機。

**圖表2 — 執行階段。** 使用者傳送的每則訊息發生什麼情況，一次
一台伺服器已上線。 以[!DNL ChatGPT]作為範例主機顯示 — 
任何MCP主機（例如[!DNL Claude]）都會播放相同的序列。

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
