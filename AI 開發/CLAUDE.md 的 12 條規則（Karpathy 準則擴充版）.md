---
title: "CLAUDE.md這樣寫才對！12條規則一次整理，讓Claude Code錯誤率從41%降至3%"
source: https://www.bnext.com.tw/article/90965/claude.md-claude-code
type: article
language: zh-TW
author: 蘇柔瑋
publisher: 數位時代
published: 2026-05-18
created: 2026-11-17
tags:
  - ai
  - claude-code
  - claude-md
  - agentic-engineering
  - coding-agent
  - prompt-engineering
  - rules
  - code-quality
  - verification
related:
  - "[[Karpathy 的三層方法（Spec、Verifier、Environment）]]"
  - "[[Matt Pocock 的 AI Skills 工作流（grill-me）]]"
  - "[[Coding Agent 與技術債（別拿 codebase 去抵押）]]"
---

# CLAUDE.md 的 12 條規則（Karpathy 準則擴充版）

> [!info] 來源
> 數位時代文章（繁體中文）· 蘇柔瑋 · 2026-05-18 · [文章連結](https://www.bnext.com.tw/article/90965/claude.md-claude-code)
> 原始素材：GitHub 專案 multica-ai/andrej-karpathy-skills（4 條規則版，65 行）與 Mnimiy 的 [X 貼文](https://x.com/Mnilax/status/2053116311132155938)（12 條規則版）

> [!warning] 數據來源
> 41%、11%、3% 的錯誤率與 76% 的遵循率，都是 **Mnimiy 個人實測的自述數字**（30 個程式碼庫、50 個代表性任務、約 6 週），沒有公開的評測方法可以複驗，當作方向參考即可
> 文章內的作者拼寫不一致：正文寫「Mnimiy」，X 帳號是「Mnilax」

## 核心主張

CLAUDE.md 是放在專案根目錄、每次對話都會讀進去的**行為規範**，重點不是教 AI 寫程式，而是**限制它在不確定時的預設反應**

> 每一條規則都要對應一個你真的遇過的錯誤；量身打造的 6 條，勝過照抄卻用不到的 12 條

## 脈絡：規則是怎麼長出來的

| 階段    | 作者                       | 做了什麼                                      | 自述錯誤率 |
| ----- | ------------------------ | ----------------------------------------- | ----- |
| 起點    | Andrej Karpathy（2026-01） | 指出 AI 寫程式反覆出現的 3 種壞習慣                     | 41%   |
| 4 條版  | Forrest Chang            | 把抱怨整理成 4 條規則的 CLAUDE.md，GitHub 收藏數超過 12 萬 | 11%   |
| 12 條版 | Mnimiy                   | 實測後補上 8 條，處理 agent 多步驟任務與大型專案             | 3%    |

Karpathy 指出的 3 種壞習慣：

1. **自行假設**：遇到模糊的地方不問，直接猜一個往下做
2. **過度複雜**：加上沒人要求的抽象層與彈性
3. **順手亂改**：動到跟任務無關的程式碼

## 12 條規則

### 原始 4 條（對應 Karpathy 的抱怨）

| # | 規則 | 重點 |
|---|---|---|
| 1 | **Think Before Coding** 先想再寫 | 把假設列出來，不確定就發問 |
| 2 | **Simplicity First** 簡單至上 | 只寫解決問題所需的最少程式碼 |
| 3 | **Surgical Changes** 手術式修改 | 只改必要範圍，不順手重構 |
| 4 | **Goal-Driven Execution** 目標導向 | 先定義可驗證的成功標準，再迭代到通過 |

### 新增 8 條（補 agent 與大型專案的洞）

| # | 規則 | 重點 |
|---|---|---|
| 5 | **Use the model only for judgment calls** | 分類、摘要、草擬才用模型；路由、重試、狀態碼這類確定性邏輯交給一般程式碼 |
| 6 | **Token budgets are not advisory** | 預算是硬上限（文中範例：單一任務 4,000、單次對話 30,000），快到就先總結再重開 |
| 7 | **Surface conflicts, don't average them** | 既有寫法互相矛盾時選一種並說明理由，另一種標記待清理，不要混成第三種 |
| 8 | **Read before you write** | 動手前先讀輸出、呼叫端與共用工具 |
| 9 | **Tests verify intent, not just behavior** | 業務邏輯改了測試就該失敗，否則這個測試沒有用 |
| 10 | **Checkpoint after every significant step** | 每一步回報已完成、已驗證、待辦；講不出目前狀態就停下來重整 |
| 11 | **Match the codebase's conventions** | 就算不認同也要照專案慣例，有意見用提出討論的方式，不要私自分岔 |
| 12 | **Fail loud** | 不確定或沒驗證就明講，不回報模糊的「已完成」「測試通過」 |

> [!note] 觀察
> 新增的 8 條大致分成三類：**誠實回報**（10、12）、**尊重既有程式碼**（7、8、11）、**資源與分工**（5、6），再加上一條測試品質（9）
> 其中 9、10、12 都在處理同一件事：別讓 AI 用「看起來完成了」取代「真的驗證過」

## 規則怎麼寫才有效

| 做法 | 原因 |
|---|---|
| ✅ 用抽象規則，少放範例 | 範例很吃上下文（3 個範例約等於 10 條規則），而且容易讓模型過度擬合 |
| ✅ 寫成具體行為 | 「像資深工程師一樣思考」這類角色扮演與情緒喊話，遵循度會掉到三成左右 |
| ✅ 用通用說法 | 寫死特定工具的指令，在沒裝該工具的專案會默默失效；改寫成「配合專案既定風格」 |

## 限制

- **遵循率約 76%**：大約四次有一次 AI 不會主動套用規則（4 條版是 78%，增加到 12 條並沒有明顯稀釋）
- **適用情境有差**：結構明確的重構任務效果最好；探索性、創意性的開發效果有限
- 「簡單至上」在需要快速試錯的**原型階段**反而可能綁手綁腳

## 與其他筆記的關聯

- 76% 的遵循率正好呼應 [[Karpathy 的三層方法（Spec、Verifier、Environment）]] 的「CLAUDE.md 是請求而不是規則」：真正不能越過的紅線要用 hook 在工具層強制，CLAUDE.md 只負責調整預設行為
- 規則 1「不確定就發問」與 [[Matt Pocock 的 AI Skills 工作流（grill-me）]] 的 grill-me 方向相同，都是讓 AI 把人腦中的情境挖出來
- 規則 3、7、11 是 [[Coding Agent 與技術債（別拿 codebase 去抵押）]] 的防線：不讓 agent 悄悄把風格與結構帶偏

## 可以帶走的做法

1. 不要整份照抄，先從原始 4 條開始
2. 每次 AI 犯了**重複出現**的錯，才補一條對應的規則
3. 規則只描述行為，不放長範例、不寫情緒字眼、不綁特定工具
4. 絕對不能發生的事另外用 hook 強制，不要只寫在 CLAUDE.md

> [!warning] 未收錄
> 文末的贊助內容（寵物 AI 競賽）與延伸閱讀連結未收錄；12 條規則的完整英文範本在原文文末可以下載
