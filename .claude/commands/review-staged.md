---
description: 對 Git 暫存區 (staged) 的變更進行程式碼審查，根據專案規範檢查程式碼品質。
---

## User Input

```text
$ARGUMENTS
```

You **MAY** consider additional review focus from the user input (if not empty).

## Goal

對 `git diff --staged` 的變更進行全面程式碼審查，根據專案的 Laravel 開發規範檢查：
- 程式碼品質與風格
- 安全性問題
- 架構合規性
- 命名規範
- 潛在 Bug

**使用時機：**
- 提交 commit 前
- 發起 PR 前
- 自我審查變更

## Operating Constraints

**STRICTLY READ-ONLY**: 不修改任何檔案，只輸出審查報告。

**審查範圍**: 僅限於 staged 變更，不審查未暫存的檔案。

## Execution Steps

### 1. 獲取暫存變更

執行以下命令獲取變更內容：

```bash
# 查看暫存的檔案清單
git diff --staged --name-only

# 查看暫存的完整差異
git diff --staged
```

如果沒有暫存的變更，提示使用者先執行 `git add` 暫存檔案。

### 2. 分類變更檔案

根據檔案類型分類：

| 類型 | 路徑模式 | 審查重點 |
|-----|---------|---------|
| **Controller** | `app/Http/Controllers/` | HTTP 處理、不含商業邏輯 |
| **Service** | `app/Services/` | 商業邏輯、事務處理 |
| **Repository** | `app/Repositories/` | 資料存取、Eloquent 使用 |
| **Model** | `app/Models/` | $fillable、關聯、屬性 |
| **FormRequest** | `app/Http/Requests/` | 驗證規則 |
| **Migration** | `database/migrations/` | 資料庫結構 |
| **Blade** | `resources/views/` | 組件使用、XSS 防護 |
| **Test** | `tests/` | 測試覆蓋、斷言 |
| **Route** | `routes/` | 路由定義、中介層 |
| **Config** | `config/` | 設定安全性 |

### 3. 執行審查檢查

#### 3.1 命名規範檢查

```
✅ 類別/介面 → PascalCase
✅ 方法/函式 → camelCase
✅ 變數/屬性 → snake_case
✅ 常數 → SCREAMING_SNAKE_CASE
✅ 路由路徑 → kebab-case
✅ 資料表/欄位 → snake_case
✅ Blade 檔案 → kebab-case.blade.php
```

#### 3.2 安全性檢查

- [ ] 是否使用原生 SQL？（應使用 Eloquent）
- [ ] 是否使用 `{!! !!}`？（XSS 風險）
- [ ] Model 是否定義 `$fillable` 或 `$guarded`？
- [ ] FormRequest 是否包含適當驗證？
- [ ] 是否有硬編碼的密碼/金鑰？
- [ ] 是否有 SQL 注入風險？

#### 3.3 架構合規性檢查

- [ ] Controller 是否只處理 HTTP 請求/回應？
- [ ] 商業邏輯是否放在 Service 層？
- [ ] 資料庫操作是否放在 Repository 層？
- [ ] Controller 是否直接操作 Model？（禁止）

#### 3.4 程式碼風格檢查

- [ ] 是否使用 4 個空格縮排？（禁止 Tab）
- [ ] 是否有繁體中文註解？
- [ ] 是否有適當的 try-catch 錯誤處理？
- [ ] 是否使用事務確保資料一致性？

#### 3.5 前端檢查（Blade 檔案）

- [ ] 是否優先使用共用組件？
- [ ] 是否手動寫 `<input>`, `<select>` 等？（應使用 x-forms.*）
- [ ] 是否使用 `{{ $var }}` 輸出？（避免 XSS）

#### 3.6 測試檢查

- [ ] 新功能是否有對應測試？
- [ ] 測試是否覆蓋主要路徑？
- [ ] 是否有適當的斷言？

### 4. 輸出審查報告

```markdown
# 程式碼審查報告

**審查時間**: [日期時間]
**變更檔案數**: [數量]
**總變更行數**: +[新增] / -[刪除]

---

## 📊 審查摘要

| 項目 | 狀態 | 說明 |
|-----|------|------|
| 命名規範 | ✅/⚠️/❌ | - |
| 安全性 | ✅/⚠️/❌ | - |
| 架構合規 | ✅/⚠️/❌ | - |
| 程式碼風格 | ✅/⚠️/❌ | - |
| 前端規範 | ✅/⚠️/❌ | - |
| 測試覆蓋 | ✅/⚠️/❌ | - |

**整體評價**: [APPROVED / NEEDS_CHANGES / REJECTED]

---

## 📁 變更檔案

| 檔案 | 類型 | 變更 | 狀態 |
|-----|------|------|------|
| path/to/file.php | Service | +50/-10 | ✅/⚠️/❌ |

---

## ❌ 必須修正 (Blockers)

嚴重問題，必須修正後才能提交：

### 1. [問題標題]

**檔案**: `path/to/file.php:行號`
**問題**: [問題描述]
**原因**: [為什麼這是問題]
**建議修正**:
```php
// 修正前
[問題程式碼]

// 修正後
[建議程式碼]
```

---

## ⚠️ 建議改進 (Warnings)

非關鍵問題，建議改進：

### 1. [問題標題]

**檔案**: `path/to/file.php:行號`
**建議**: [改進建議]

---

## ✅ 優點 (Good Practices)

值得肯定的程式碼：

- [優點 1]
- [優點 2]

---

## 📋 審查檢查清單

### 安全性
- [ ] 無原生 SQL 使用
- [ ] 無 XSS 風險
- [ ] Model 有定義 $fillable/$guarded
- [ ] 無硬編碼敏感資訊

### 架構
- [ ] Controller 職責正確
- [ ] 商業邏輯在 Service 層
- [ ] 資料存取在 Repository 層

### 程式碼品質
- [ ] 命名規範正確
- [ ] 有繁體中文註解
- [ ] 錯誤處理適當
- [ ] 縮排使用空格

### 前端
- [ ] 使用共用組件
- [ ] 無手動表單元素

---

## 🔄 下一步

根據審查結果：

**如果 APPROVED:**
```bash
git commit -m "feat: [描述]"
```

**如果 NEEDS_CHANGES:**
1. 修正上述必須修正項目
2. 重新暫存變更 `git add .`
3. 再次執行 `/review-staged`

**如果 REJECTED:**
- 需要重大修改，建議重新設計
```

---

## Operating Principles

### 審查準則

- **嚴格遵循專案規範**: 根據 CLAUDE.md 和 .claude/standards/ 的規定審查
- **安全性優先**: 任何安全問題都是 Blocker
- **建設性回饋**: 提供具體的修正建議，不只是指出問題
- **保持客觀**: 基於規範審查，不帶個人偏好

### 嚴重程度定義

| 等級 | 符號 | 說明 | 處理 |
|-----|------|------|------|
| **Blocker** | ❌ | 安全漏洞、架構違規、嚴重 Bug | 必須修正 |
| **Warning** | ⚠️ | 命名不規範、缺少註解、風格問題 | 建議修正 |
| **Info** | ℹ️ | 可改進之處、最佳實踐建議 | 可選修正 |
| **Pass** | ✅ | 符合規範 | 無需處理 |

### 限制

- **NEVER** 修改任何檔案
- **NEVER** 自動提交變更
- **ALWAYS** 基於實際程式碼審查，不做假設
- **ALWAYS** 提供具體的程式碼位置（檔案:行號）

## Context

User additional focus: $ARGUMENTS
