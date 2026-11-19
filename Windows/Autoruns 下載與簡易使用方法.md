---
title: "Autoruns 下載與簡易使用方法"
created: 2026-11-19
tags:
  - windows
  - sysinternals
  - autoruns
  - startup
related:
  - "[[工作管理員啟動項目 Program 排查紀錄]]"
---
# Autoruns 下載與簡易使用方法

Autoruns 是 Microsoft Sysinternals 推出的免費工具，可以列出 Windows 所有會自動啟動的項目，範圍比工作管理員的「啟動應用程式」完整得多

> 實際排查案例：[[工作管理員啟動項目 Program 排查紀錄]]

## 下載與啟動

- 下載頁面：<https://learn.microsoft.com/sysinternals/downloads/autoruns>
- 解壓縮後免安裝即可使用，64 位元系統執行 `Autoruns64.exe`
- 建議**以系統管理員身分執行**，否則部分項目無法顯示或修改

## 畫面說明

**常用分頁**

| 分頁 | 內容 |
|---|---|
| Everything | 全部項目 |
| Logon | 登錄的 Run 機碼、Startup 資料夾 |
| Scheduled Tasks | 工作排程器 |
| Services | Windows 服務 |
| Drivers | 驅動程式 |
| Explorer | 檔案總管的右鍵選單擴充等 |

**欄位**：Autoruns Entry（名稱）、Description（描述）、Publisher（發行者，`(Verified)` 表示簽章有效）、Image Path（執行檔路徑）

**顏色**

- **黃色**：檔案不存在，通常是解除安裝後留下的殘留項目
- **粉紅色**：沒有簽章、找不到發行者，或 VirusTotal 有偵測結果，需要特別留意

## 常用操作

- **取消勾選**：暫時停用，隨時可以再勾回來，比刪除安全
- **右鍵 → Delete**：直接刪除項目
- **右鍵 → Jump to Entry**：開啟登錄編輯器或資料夾，跳到該項目的實際位置
- **右鍵 → Jump to Image**：在檔案總管中顯示執行檔
- **上方篩選框**：輸入關鍵字篩選，比對範圍包含名稱、描述和路徑

## 建議設定（Options 選單）

- **Hide Microsoft Entries**：隱藏 Microsoft 項目，減少干擾
  - 注意：勾選後 Microsoft 簽章的項目（例如 Office 元件）也會被隱藏，排查時找不到目標可以先取消勾選
- **Hide Empty Locations**：隱藏沒有任何項目的位置
- **Scan Options → Verify code signatures、Check VirusTotal.com**：驗證簽章並查詢 VirusTotal，方便判斷可疑程式

## 使用建議

1. 修改前先用「File → Save」把目前的清單存起來備份
2. 不確定的項目先取消勾選，觀察幾天沒有問題再刪除
3. 已驗證簽章的 Microsoft 項目不要隨意更動，尤其是 Services 和 Drivers 分頁
