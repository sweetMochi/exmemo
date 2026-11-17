---
title: "HTTP Verbs: 談 POST, PUT 和 PATCH 的應用"
source: https://ihower.tw/blog/6483-http-verbs-post-put-patch
author: ihower
published: 2012-03-20
created: 2026-11-16
tags:
  - http
  - rest
  - api
  - rails
  - idempotent
---
# HTTP Verbs: 談 POST, PUT 和 PATCH 的應用

> [!info] 來源
> ihower 的 Blog · 2012-03-20（頁面最後更新 2025-07-04）· 分類：HTTP、REST
> [原文連結](https://ihower.tw/blog/6483-http-verbs-post-put-patch)

## 前言：HTTP 方法 ≠ CRUD

- 常見的說法是：POST＝新增、GET＝讀取、PUT＝更新、DELETE＝刪除
- 作者設計純 API 時，才發現搞不清楚該用 PUT 還是 POST，原因是受了 **Rails scaffold 範例**的影響
- 其實 **PUT 也能用來新增**；另外還有較新的 **PATCH**（GitHub API、Rails 4 都已採用）

## 各方法的語意

| 方法 | 語意 | 重點 |
|---|---|---|
| POST | 新增 | 由伺服器分配 ID |
| PUT | **Replace**（Create or Update） | 存在就取代、不存在就建立；必須送出**資源的全部屬性** |
| PATCH | **Partial Update**（RFC 5789） | 只送要改的欄位 |
| DELETE | 刪除 | 執行後資源一定不存在 |

- 只改一個屬性也要用 PUT 送出完整資源很浪費頻寬，所以才有 PATCH

## 冪等性（Idempotent）與安全性（Safe）

- **Idempotent**：同一個操作做一次或做多次，結果都一樣 → 決定**能不能安全重試（retry）**
- **Safe**：不改變資源狀態，只有 GET、HEAD → 決定**能不能快取**

| 方法 | Safe | Idempotent |
|---|:-:|:-:|
| GET | ✅ | ✅ |
| POST | ❌ | ❌ |
| PATCH | ❌ | ❌ |
| PUT | ❌ | ✅ |
| DELETE | ❌ | ✅ |

- POST 重送會產生多筆資料
- PATCH 不保證冪等：它描述的是「一組修改」而不是新值，例如 `count++`、投票 vote up（留言區補充的例子）
- GitHub API 讓 POST 可以替代 PATCH，因為 PATCH 較新、支援度低，兩者又都非冪等
## 什麼時候適合用 PUT？

### 1. 訂閱功能

- 只有「已訂閱／未訂閱」兩種狀態，重複送出結果相同 → 天生冪等
- 訂閱：`PUT /subscriptions/{something_id}`（作者認為比 `POST /subscriptions` 好）
- 取消：`DELETE /subscriptions/{something_id}`

### 2. 離線的行動裝置

- 離線產生的資料由**客戶端用 UUID 當 ID**，多裝置同步時不需要中央伺服器分配 ID
- 用 POST 的問題：網路不穩時 POST 其實成功了，但客戶端沒收到回應而重試 → 伺服器出現重複資料
- 用 `PUT /items/{uuid}` 重送也沒關係，因為它是冪等的
- 可以搭配 **Conditional PUT**（`ETag`、`Last-Modified`）避免覆蓋衝突
- 沒有離線需求的第三方應用，直接 `POST /items` 讓伺服器產生 UUID 就好

## 結論

- 選 HTTP 方法要看**語意與冪等性**，不要死套 CRUD
- **POST**：伺服器分配 ID 的新增（非冪等）
- **PUT**：完整取代或用已知 ID 建立（冪等，可放心重試）
- **PATCH**：部分更新（不保證冪等）
- 判斷原則：**重送不影響結果 → 適合 PUT / DELETE**

> [!note] 留言區補充
> 作者在留言回覆中寫「PATCH 跟 POST 都是 idempotent 的操作」，和正文「兩者都非冪等」互相矛盾，應該是筆誤，以正文和表格為準。

## 延伸閱讀

- POST-PUT-PATCH illustrated（amundsen.com）
- PUT or POST: The REST of the Story（jcalcote.wordpress.com）
