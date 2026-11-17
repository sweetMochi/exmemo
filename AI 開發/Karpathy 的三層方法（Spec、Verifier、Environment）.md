---
title: "Stop Prompting Claude. Use Karpathy's Method Instead."
source: https://www.youtube.com/watch?v=7zZy1QTvokM
type: video
language: en
duration: "13:18"
created: 2026-11-17
tags:
  - ai
  - claude-code
  - agentic-engineering
  - spec
  - verification
  - claude-md
  - hooks
  - knowledge-base
related:
  - "[[Matt Pocock 的 AI Skills 工作流（grill-me）]]"
  - "[[Coding Agent 與技術債（別拿 codebase 去抵押）]]"
  - "[[為什麼手寫程式碼反而是優勢]]"
---

# Karpathy 的三層方法（Spec、Verifier、Environment）

> [!info] 來源
> YouTube 影片（英文）· 約 13 分鐘 · 頻道 Austin Marchese · [影片連結](https://www.youtube.com/watch?v=7zZy1QTvokM)
> 文字稿：[[Stop Prompting Claude. Use Karpathy's Method Instead..txt]]
> 字幕把 Andrej Karpathy 拼成「Cararpathy／Carpathy」，「AISN 2026」應是 Sequoia **AI Ascent 2026** 的誤聽。

> [!warning] 框架歸屬
> 「Spec → Verifier → Environment」三層是 **YouTuber 自己整理的框架**，以 Karpathy 的演講與訪談為素材，不是 Karpathy 本人提出的方法。直接引用 Karpathy 的只有：洗車問題、不喜歡 plan mode、animals vs. ghosts、LLM knowledge base，以及結尾那句話。

## 核心主張

AI 擅長**可衡量**的事，但缺乏**情境**。人要用三層結構把自己的理解交給 AI：

```
Environment（工作坊）
 ├─ Spec（釘在牆上的藍圖）
 └─ Verifier（QA 品質管理）
```

## 為什麼需要：洗車問題

> 「我想去洗車場洗車，距離 50 公尺，該開車還是走路？」

最先進的模型都會回答「走路，因為很近」。講者實測 Claude、Gemini、Grok、ChatGPT，答案都一樣。AI 沒有「洗車需要車子在場」這種**情境訊號**可以依據。

## Layer 1：Spec（規格）

Spec 是**把你的理解，用 AI 能用的格式交給它**。

- Karpathy：「我其實不喜歡 plan mode……它很有用，但更通用的做法是**跟 agent 一起設計非常詳細的 spec**。」不是說 plan mode 不好，而是要**再深入一層**

| 步驟           | 說明                                                | 給 Claude 的指示        |
| ------------ | ------------------------------------------------- | ------------------- |
| **1. 找出目標**  | 「做月報」是任務；目標是報告要導出的**結論、要驅動的決策**。目標導向的 AI 永遠無法替你決定 | 「詢問我細節，找出這個專案的目標」   |
| **2. 敏捷式切分** | 別用瀑布式一次丟整包給 agent。範圍小、檢查點明確，review → 調整 → 重複      | 「偏向更小、更分割的 spec」    |
| **3. 精確＋動腦** | 越精確 AI 越少臆測，每個臆測都是偏離的機會。AI 寫的 spec 你要批判性地讀        | 「讓我明確確認關鍵決策，確保沒有遺漏」 |

> [!note] 和 grill-me 的相似處
> 「讓 Claude 訪問我」就是 [[Matt Pocock 的 AI Skills 工作流（grill-me）]] 的 grill-me 概念：讓 AI 挖出人腦中的情境，拍板定案由人負責。

## Layer 2：Verifier（驗證）

### 心智模型：動物 vs. 幽靈

- Karpathy：我們不是在打造**動物**，而是在**召喚幽靈**。它們不是動物智能，對它吼叫不會讓它表現更好或更差，它們只是**統計模擬電路**。要保持懷疑，並隨時間摸清它
- 講者的比喻：**機器人圖書館員**
	- 只能根據館藏回答，沒有那本書就幫不上忙
	- 而且**不知道自己缺哪本書**，所以會自信地瞎掰
	- 這就是 AI 數學很強、情境卻常出錯的原因
- 結論：把 AI 當人對待（施壓、哀求、「弄好一點」）沒用。**唯一的槓桿是驗證**

### 三個著力點

| 步驟 | 說明 | 例子 |
|---|---|---|
| **1. 事先定義評估標準** | 動手前就精確定義「好」長什麼樣子 | ❌「讓報告看起來不錯」→ ✅「報告必須有三個段落，每段以建議作結」 |
| **2. 用第二個模型當評審** | 另一間圖書館的館員，館藏不同，能看出第一位哪裡對錯 | Claude Code 裝 Codex plugin：「若是複雜建置，最後讓 Codex 檢查，確保兩邊結論一致」 |
| **3. 引入外部訊號** | 用外部資料佐證產出 | 技術：把 session 接上部署平台，**實際確認**部署成功；非技術：帶入歷史報告當格式參考 |

給 Claude 的指示：「列出你會用來確保最終成品品質的評估標準，要精確。」

> [!tip] Boris Cherny（Claude Code 作者）
> 如果 Claude 有**回饋迴路**，最終成果品質會提升 **2–3 倍**。

## Layer 3：Environment（環境）

前兩層需要一個地方存在。多數人每次都從零開始搭工作坊（光有一串長對話紀錄不算數），要建立一個**會隨時間改進的工作空間**。

### 1. CLAUDE.md

- 每次 prompt 都會自動注入，是 Claude 最先讀的東西
- 例：加入「建置任何多步驟功能前，先附上驗證計畫」，驗證就成了**預設行為**，不用每次記得說
- 講者的 CLAUDE.md 結構：
	1. 這個 repo（工作空間）怎麼運作
	2. 自訂 skill 與路由方式
	3. 知識架構：資料放在哪、AI 該去哪找
	4. 無論如何都要遵守的工作規則
- 「這是**你的**世界，AI 住在裡面，不是反過來。」

### 2. LLM 知識庫

- Karpathy 在 Twitter 上爆紅的概念：在本機建立資料夾系統，讓 Claude 容易知道資訊在哪
- **你的資料就是你的護城河**，也是累積自己智慧資產的開始

### 3. Skills

- 經驗法則：**會重複做的事就做成 skill**（完成特定任務的手冊）
- 「找水管漏洞最好的方法，就是讓水流過去。」越常用，越能發現哪裡要修，系統會**複利成長**

### 4. 護欄：prompt 層 vs. 工具層

- CLAUDE.md 寫「不要捏造資訊」只是**指引**，AI 仍可能忽略
- 例：CLAUDE.md 寫「不要動 `/important-dont-edit`」大概有 80% 效果，但那是**請求而不是規則**
- 改用 **PreToolUse hook**：在 Write／Edit 前檢查目標檔案，Claude 就**真的改不了**，因為限制是在工具層強制執行

依「出錯的代價」分成三類：

| 類別 | 意義 |
|---|---|
| **Always do** | AI 自動執行 |
| **Ask first** | 需要你再確認 |
| **Never do** | 絕不能越過的紅線（用 hook 強制） |

## 結論：AI 時代最該投資的是理解

> **"You can outsource your thinking, but you can't outsource your understanding."**
> 你可以外包思考，但無法外包理解。—— Karpathy

三層都以**你對全局的理解**為核心：你得清楚目標，以及要怎麼指揮 AI。這與 [[為什麼手寫程式碼反而是優勢]] 的「補上知識缺口才能判斷 agent 對不對」，以及 [[Coding Agent 與技術債（別拿 codebase 去抵押）]] 的「人在迴路中、小步交付」都相呼應。

| 層 | 解決的 AI 問題 | 關鍵做法 |
|---|---|---|
| Spec | 缺乏情境，會自己臆測 | 讓 AI 訪問你、小範圍 spec、親自確認關鍵決策 |
| Verifier | 不知道自己錯，施壓無效 | 事先定義標準、第二個模型評審、外部訊號 |
| Environment | 每次從零開始 | CLAUDE.md、知識庫、skills、hook 護欄 |

> [!warning] 業配
> 8:23–8:48 為訂閱呼籲與 Claude Max 抽獎，摘要未收錄。影片中提到的三段完整 prompt 只出現在畫面上，文字稿沒有收錄。
