---
title: "Day 28：淺談 Monorepo"
source: https://ithelp.ithome.com.tw/articles/10378398
series: "Angular 進階實務 30天"
published: 2025-09-11
created: 2026-11-16
tags:
  - angular
  - monorepo
  - frontend
  - deployment
  - ithelp
related:
  - "[[Standalone 與 SharedModule 比較]]"
---
# Day 28：淺談 Monorepo

> [!info] 來源
> iT 邦幫忙鐵人賽 ·「Angular 進階實務 30天」第 28 篇 · 2025-09-11
> [原文連結](https://ithelp.ithome.com.tw/articles/10378398)
> 上一篇：Day 27 如何寫出可維護的元件？ · 下一篇：Day 29 Angular Library – 建立與共用元件

## 前言：Standalone 元件 vs SharedModule

比較表已移到獨立筆記：[[Standalone 與 SharedModule 比較]]

## 什麼是 Monorepo？

- **Mono**（希臘語 μόνος）＝單一；**Repository**＝倉庫
- 用**一個版本控制倉庫管理多個專案**（Git、SVN 皆可）
- 重點不只是「放在一起」，而是**協作與共用**的價值

## 為什麼需要 Monorepo？

前台、後台、API、App 常有共通點：

- 設計規格相似（UI 元件、色彩風格）
- 環境設定相似（API domain、認證機制）
- 資料模型相似（Model、Enum、Interface）

集中管理的好處：

- **清楚追蹤**：一次 commit 記錄跨專案改動
- **共用程式碼**：model、enum、interface、UI component
- **一致性**：Lint、測試、CI/CD pipeline 統一設定

## 實作：Angular Workspace

```bash
ng new company-workspace --create-application false
cd company-workspace
ng generate application frontend
ng generate application backend
ng generate library shared
```

- `frontend`：前台
- `backend`：後台
- `shared`：共用程式碼（model、enum、UI 元件）

### 範例：共用按鈕元件

```bash
ng generate component shared/button
```

```typescript
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-shared-button',
  template: `<button [ngClass]="style">{{ label }}</button>`,
  styles: [`
    button { padding: 8px 16px; border-radius: 8px; }
    .primary { background: #1976d2; color: white; }
    .secondary { background: #e0e0e0; color: #333; }
  `]
})
export class SharedButtonComponent {
  @Input() label = '按鈕';
  @Input() style: 'primary' | 'secondary' = 'primary';
}
```

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { SharedButtonComponent } from './button/button.component';

@NgModule({
  declarations: [SharedButtonComponent],
  imports: [CommonModule],
  exports: [SharedButtonComponent]
})
export class SharedModule { }
```

前後台只要 import `SharedModule` 即可使用。

## 部署原則

> [!warning] 最難的是部署
> Monorepo 寫法不難，坑多在部署與安裝。

**只交付靜態檔**：build 各應用即可，shared 會在編譯時自動包進去。

```bash
npm run build admin -- --base-href="/admin/"
npm run build user -- --base-href="/user/"
```

**自動化部署 Checklist**

- [ ] 每個應用設定自己的 `baseHref`
- [ ] 使用相對資源路徑（避免 `/assets/...` 讀不到）
- [ ] 規劃獨立 `outputPath`，如 `"outputPath": "dist/admin"`
- [ ] 開啟 `outputHashing=all`
- [ ] 使用單一套件管理（workspace），確保版本一致
- [ ] 設定 SPA fallback，路由導回 `index.html`
- [ ] 確認 `.js`、`.mjs`、`.css` 的 MIME 類型正確
- [ ] 使用 `fileReplacements` 替換 production 環境變數
- [ ] API 端點對應正確環境

```javascript
// GitHub Pages 404.html 範例
if (location.pathname.startsWith('/admin/')) {
  location.replace('/admin/');
}
```

> [!tip]
> 90% 的部署問題都出在**路徑設定**，先確保 `baseHref` 與資源路徑正確。

## 結語

**優點**

- 程式碼共用：元件、樣式、介面定義
- 一致性：前後台視覺與功能一致
- 維護性：統一管理，易於版本控制與修 bug
- 開發效率：避免重複開發
- 部署便利：統一建置與部署流程

**缺點**

- 倉庫體積快速變大
- CI/CD 可能變慢，需工具優化，有學習門檻
- 團隊習慣需改變（所有人在同一倉庫協作）
