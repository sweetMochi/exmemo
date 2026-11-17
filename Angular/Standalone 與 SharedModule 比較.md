---
title: "Standalone 元件 vs SharedModule"
source: https://ithelp.ithome.com.tw/articles/10378398
created: 2026-11-16
tags:
  - angular
  - standalone
  - ngmodule
related:
  - "[[淺談 Monorepo]]"
---

# Standalone 元件 vs SharedModule

> [!info] 來源
> 摘自 iT 邦幫忙「Angular 進階實務 30天」Day 28 · [原文連結](https://ithelp.ithome.com.tw/articles/10378398)
> 相關筆記：[[淺談 Monorepo]]

| 特點         | Standalone 元件      | SharedModule      |
| ---------- | ------------------ | ----------------- |
| Angular 版本 | 14+ 推薦             | 傳統方式，所有版本支援       |
| 引入方式       | 直接 import 元件       | import 整個 Module  |
| 靈活性        | 高，按需引入             | 中等，需要整個模組         |
| 檔案大小       | 更小，Tree-shaking 友善 | 較大，可能包含未使用元件      |
| 學習曲線       | 簡單，直觀              | 需了解 NgModule 概念   |
| 依賴管理       | 元件自行管理             | 模組統一管理            |
| 測試         | 單元測試較簡單            | 需設定 TestingModule |
| 程式碼結構      | 分散式，各元件獨立          | 集中式，統一管理          |
| 遷移成本       | 新專案建議使用            | 現有專案可繼續使用         |
| 效能         | 較佳，按需載入            | 一般，整包載入           |

> [!tip] 怎麼選
> - **新專案**：用 Standalone 元件，按需引入，打包檔較小。
> - **既有專案**：可以繼續用 SharedModule，再逐步遷移。
