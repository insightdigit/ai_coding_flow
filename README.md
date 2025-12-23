# AI 開發流程新人手冊

> **版本:** 1.0 | **最後更新:** 2025-12-23
>
> 歡迎加入團隊！本手冊將幫助你快速了解如何使用 Claude Code 的 AI 輔助開發流程。

---

## 目錄

1. [快速開始](#快速開始)
2. [核心概念](#核心概念)
3. [AI 工具總覽](#ai-工具總覽)
4. [Skills（自動流程）](#skills自動流程)
5. [Commands（指令）](#commands指令)
6. [Agents（代理）](#agents代理)
7. [常見開發場景](#常見開發場景)
8. [最佳實踐](#最佳實踐)
9. [FAQ](#faq)

---

## 快速開始

### 第一步：了解基本架構

本專案使用 **Laravel 12 + Blade + Tailwind CSS**，採用三層架構：

```
Controller (處理 HTTP) → Service (商業邏輯) → Repository (資料存取)
```

### 第二步：記住三個核心原則

| 原則 | 說明 |
|-----|------|
| **測試先行** | 先寫測試，再寫程式碼（TDD） |
| **規格先行** | 先定義規格，再開始實作 |
| **組件優先** | 開發 UI 前，先檢查有無可用的共用組件 |

### 第三步：開始第一個任務

只需要用自然語言描述你要做的事，AI 會自動選擇合適的流程：

```
「我要開發一個產品管理功能」  → AI 自動執行新功能開發流程
「這個 bug 需要修復」        → AI 自動執行 bug 修復流程
「幫我做一個訂單列表頁面」   → AI 自動執行 UI 開發流程
```

---

## 核心概念

### 開發流程金字塔

```
                    ┌─────────┐
                    │ Review  │  ← 程式碼審查
                   ┌┴─────────┴┐
                   │   Code    │  ← 實作程式碼
                  ┌┴───────────┴┐
                  │    TDD      │  ← 測試驅動開發
                 ┌┴─────────────┴┐
                 │     Spec      │  ← 技術規格
                ┌┴───────────────┴┐
                │      BDD        │  ← 行為定義
               ┌┴─────────────────┴┐
               │ Sequential Thinking│  ← 結構化分析
               └───────────────────┘
```

### 檔案結構

```
.claude/
├── skills/           # 自動觸發的工作流程（6 個）
│   ├── new-feature/  # 新功能開發
│   ├── bug-fix/      # Bug 修復
│   ├── testing/      # 測試開發
│   ├── ui-dev/       # UI/前端開發
│   ├── architecture/ # 架構設計
│   └── refactor/     # 重構/優化
│
├── commands/         # 明確調用的指令（8 個）
│   ├── bdd.md            # 行為驅動開發
│   ├── tdd.md            # 測試驅動開發
│   ├── components.md     # 掃描共用組件
│   ├── impact-analysis.md # 影響範圍分析
│   ├── review-staged.md  # 審查暫存區
│   ├── backend-spec.md   # 後端規格
│   ├── frontend-spec.md  # 前端規格
│   └── bug-fix.md        # Bug 修復
│
├── agents/           # AI 代理
│   └── core/
│       ├── researcher.md # 研究分析
│       ├── coder.md      # 程式碼實作
│       ├── tester.md     # 測試撰寫
│       └── reviewer.md   # 程式碼審查
│
└── standards/        # 開發規範
    ├── workflow.md       # 工作流程
    ├── architecture.md   # 架構模式
    ├── naming.md         # 命名規範
    ├── coding-style.md   # 程式碼風格
    ├── security.md       # 安全規範
    ├── frontend.md       # 前端開發
    └── components.md     # 共用組件
```

---

## AI 工具總覽

### 三大類工具

| 類別 | 觸發方式 | 用途 |
|-----|---------|------|
| **Skills** | 自動（根據關鍵字） | 完整的開發流程 |
| **Commands** | 手動（輸入 `/指令`） | 特定任務 |
| **Agents** | 由 AI 調用 | 專業任務執行 |

### 工具關係圖

```
┌─────────────────────────────────────────────────────────────┐
│                        用戶輸入                              │
│  「我要開發產品管理功能」                                     │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                   Skills（自動識別）                          │
│  ✓ 偵測到「開發」→ 觸發 new-feature Skill                     │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                   Commands（按順序執行）                      │
│  1. /bdd → 2. /backend-spec → 3. /tdd → ...                 │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                   Agents（執行任務）                          │
│  researcher → tester → coder → reviewer                      │
└─────────────────────────────────────────────────────────────┘
```

---

## Skills（自動流程）

### 什麼是 Skills？

Skills 是**自動觸發的完整開發流程**。當你的訊息包含特定關鍵字時，AI 會自動識別並執行對應的流程。

### 六大 Skills 一覽

| Skill | 觸發關鍵字 | 執行流程 |
|-------|-----------|----------|
| **new-feature** | 新功能、新增、開發、實作、建立 | BDD → TDD → Code → Review |
| **bug-fix** | bug、錯誤、問題、修復、fix、壞掉 | 分析 → 影響評估 → TDD → 修復 → Review |
| **testing** | 測試、test、TDD、單元測試 | 分析 → TDD → 實作 → Review |
| **ui-dev** | 頁面、表單、畫面、UI、前端、介面 | 掃描組件 → BDD → TDD → 實作 → Review |
| **architecture** | 架構、設計、評估、比較、方案 | 深度分析 → 方案比較 → 架構審查 |
| **refactor** | 重構、優化、調整、改善、拆分、效能 | 影響分析 → 回歸測試 → 重構 → Review |

### 使用範例

```
❌ 不需要這樣說：
   「請執行 new-feature skill 來開發產品管理」

✅ 直接說：
   「我要開發一個產品管理功能」

   AI 會自動識別「開發」關鍵字，觸發 new-feature skill
```

---

## Commands（指令）

### 什麼是 Commands？

Commands 是**手動調用的指令**，用於執行特定任務。使用方式是輸入 `/指令名稱`。

### 八大 Commands 詳解

#### 1. `/bdd` - 行為驅動開發

**用途：** 用 Gherkin 語法定義功能行為

**使用時機：** 開發新功能前，先定義預期行為

**範例：**
```
/bdd 使用者登入功能
```

**輸出：**
```gherkin
# language: zh-TW
功能: 使用者登入
  作為一個訪客
  我想要登入系統
  以便存取個人資料

  場景: 使用有效憑證登入
    假設 我在登入頁面
    當 我輸入帳號 "test@example.com"
    而且 我輸入密碼 "password"
    而且 我點擊登入按鈕
    那麼 我應該看到 "歡迎回來"
```

---

#### 2. `/tdd` - 測試驅動開發

**用途：** 執行 TDD 流程（紅燈 → 綠燈 → 重構）

**使用時機：** 開發任何功能前

**範例：**
```
/tdd ProductService::createProduct
/tdd unit ProductRepository::getActiveProducts
/tdd feature 產品列表 API
```

**流程：**
1. **紅燈** - 先寫失敗的測試
2. **綠燈** - 寫最少程式碼讓測試通過
3. **重構** - 改善程式碼，保持測試通過

**⚠️ 重要限制：**
- TDD 只測試**後端邏輯**
- 不測試 Blade 視圖、HTML、CSS、JavaScript

---

#### 3. `/components` - 掃描共用組件

**用途：** 列出所有可用的共用 Blade 組件

**使用時機：** 開發任何 UI 前（**強制**）

**範例：**
```
/components
```

**輸出範例：**
```
📦 可用共用組件

表單組件 (forms/)
├── x-forms.text-input      文字輸入框
├── x-forms.textarea-field  多行文字框
├── x-forms.select-field    下拉選單
└── x-forms.image-upload    圖片上傳

UI 組件
├── x-page-header    頁面標題
├── x-breadcrumb     麵包屑
├── x-status-badge   狀態標籤
├── x-alert          提示訊息
├── x-button         按鈕
└── x-section-card   區塊容器

操作組件
├── x-action-buttons 操作按鈕組（查看/編輯/刪除）
└── x-back-button    返回按鈕
```

**⚠️ 禁止行為：**
- ❌ 不執行 `/components` 就開始寫 UI
- ❌ 手動寫 `<input>`, `<select>`, `<textarea>`
- ❌ 重複創建已存在的組件

---

#### 4. `/impact-analysis` - 影響範圍分析

**用途：** 分析程式碼變更的影響範圍

**使用時機：** 修改現有程式碼前、重構前、修復 Bug 前

**範例：**
```
/impact-analysis app/Services/ProductService.php
/impact-analysis ProductService::createProduct
/impact-analysis "修改產品價格計算邏輯"
```

**輸出內容：**
- 依賴關係圖（誰依賴這個、這個依賴誰）
- 受影響的檔案清單
- 需要執行的測試
- 風險等級評估

---

#### 5. `/review-staged` - 審查暫存區

**用途：** 對 Git 暫存區的變更進行程式碼審查

**使用時機：** 提交 commit 前、發起 PR 前

**範例：**
```
/review-staged
```

**審查項目：**
- ✓ 命名規範
- ✓ 安全性（SQL 注入、XSS）
- ✓ 架構合規（分層是否正確）
- ✓ 程式碼風格
- ✓ 前端規範（是否使用共用組件）
- ✓ 測試覆蓋

---

#### 6. `/backend-spec` - 後端規格

**用途：** 撰寫後端技術規格文檔

**使用時機：** 開發新功能前，定義後端架構

**範例：**
```
/backend-spec 產品管理
/backend-spec 訂單模組 - 建立訂單、取消訂單
```

**產出內容：**
- 資料表設計
- Model 規格（$fillable、關聯、Scope）
- Repository 方法
- Service 方法與流程
- Controller 設計
- 驗證規則

---

#### 7. `/frontend-spec` - 前端規格

**用途：** 撰寫前端技術規格文檔

**使用時機：** 開發 UI 前，定義頁面架構

**範例：**
```
/frontend-spec 產品管理頁面
/frontend-spec 訂單列表頁、訂單表單頁
```

**產出內容：**
- 頁面結構圖
- 組件使用清單
- 表單欄位定義
- 使用者操作流程
- 樣式規格

---

#### 8. `/bug-fix` - Bug 修復

**用途：** 執行標準化的 Bug 修復流程

**使用時機：** 修復 Bug 時

**範例：**
```
/bug-fix 產品列表載入很慢
/bug-fix app/Services/ProductService.php 價格計算錯誤
/bug-fix "Call to undefined method" ProductController
```

**流程：**
1. Sequential Thinking - 分析問題根因
2. Impact Analysis - 評估修改影響
3. TDD - 撰寫測試重現 Bug
4. 修復 - 讓測試通過
5. Review - 程式碼審查

---

## Agents（代理）

### 什麼是 Agents？

Agents 是**專業的 AI 代理**，各自負責特定類型的任務。通常由 AI 在執行 Skill 時自動調用。

### 核心 Agents

#### 🔍 Researcher（研究員）

**職責：** 深度分析程式碼、研究依賴關係、找出模式

**何時被調用：**
- 開始任何新任務前
- 需要了解現有程式碼結構時

**分析內容：**
- 程式碼結構和模式
- 依賴關係
- 現有實作方式
- 建議和改進點

---

#### 💻 Coder（工程師）

**職責：** 撰寫符合規範的 Laravel 程式碼

**何時被調用：**
- TDD 綠燈階段（實作程式碼）
- 重構階段

**遵循原則：**
- Controller → Service → Repository 分層
- SOLID 原則
- 繁體中文註解
- 4 個空格縮排

---

#### 🧪 Tester（測試員）

**職責：** 撰寫全面的測試案例

**何時被調用：**
- TDD 紅燈階段（撰寫測試）
- 需要補充測試時

**測試類型：**
- Unit Test（Service、Repository）
- Feature Test（Controller、API）
- 遵循 AAA 模式（Arrange-Act-Assert）

---

#### 👀 Reviewer（審查員）

**職責：** 程式碼審查、品質把關

**何時被調用：**
- 程式碼實作完成後
- 提交前

**審查項目：**
- 功能正確性
- 安全性（SQL 注入、XSS）
- 效能（N+1 問題）
- 架構合規（分層）
- 程式碼品質（命名、註解）

---

## 常見開發場景

### 場景 A：新功能開發

**你說：**
```
「幫我開發一個產品管理系統」
```

**AI 自動執行：**
```
1. Sequential Thinking → 分析需求
2. /bdd              → 定義行為（Given/When/Then）
3. /backend-spec     → 產生後端規格
4. /frontend-spec    → 產生前端規格
5. /tdd              → 先寫測試（紅燈）
6. coder agent       → 實作（綠燈→重構）
7. reviewer agent    → 審查
8. /review-staged    → 提交前審查
```

---

### 場景 B：Bug 修復

**你說：**
```
「產品價格計算錯誤，應該是 100 但顯示 90」
```

**AI 自動執行：**
```
1. Sequential Thinking → 分析問題根因
2. /impact-analysis    → 評估影響範圍
3. /tdd               → 先寫失敗測試重現 bug
4. coder agent        → 修復讓測試通過
5. reviewer agent     → 審查
6. /review-staged     → 提交前審查
```

---

### 場景 C：UI 開發

**你說：**
```
「幫我做一個產品列表頁面」
```

**AI 自動執行：**
```
1. Sequential Thinking → 分析 UI 需求
2. /components        → 列出可用共用組件 ⚠️ 必須
3. /bdd              → 定義 UI 行為
4. /frontend-spec    → 產生前端規格
5. /tdd              → 先寫 Feature Test
6. coder agent       → 實作 UI（使用共用組件）
7. reviewer agent    → 審查
```

---

### 場景 D：重構

**你說：**
```
「幫我重構 ProductService，把計算邏輯拆出來」
```

**AI 自動執行：**
```
1. Sequential Thinking → 分析重構策略
2. /impact-analysis   → 評估影響範圍 ⚠️ 必須
3. /tdd              → 先寫回歸測試
4. coder agent       → 重構（保持測試通過）
5. reviewer agent    → 審查
```

---

### 場景 E：快速修改

**你說：**
```
「直接幫我改這個方法名稱」
```

**AI 執行：**
```
直接編輯，不執行完整流程
```

---

## 最佳實踐

### ✅ 應該做的

1. **讓 AI 自動選擇流程**
   ```
   ✅ 「我要開發產品管理功能」
   ❌ 「請執行 new-feature skill」
   ```

2. **開發 UI 前一定要執行 `/components`**
   ```
   ✅ 先執行 /components 查看可用組件
   ❌ 直接寫 <input>, <select>
   ```

3. **修改前先執行 `/impact-analysis`**
   ```
   ✅ 修改 Service 前先分析影響
   ❌ 直接修改核心程式碼
   ```

4. **提交前執行 `/review-staged`**
   ```
   ✅ git add . → /review-staged → git commit
   ❌ 直接 git commit
   ```

---

### ❌ 不應該做的

1. **不寫測試就修復 Bug**
   ```
   ❌ 直接改程式碼解決 bug
   ✅ 先寫測試重現 bug，再修復
   ```

2. **在 Controller 寫商業邏輯**
   ```
   ❌ 在 Controller 直接操作 Model
   ✅ Controller → Service → Repository
   ```

3. **手動寫表單元素**
   ```
   ❌ <input type="text" name="name">
   ✅ <x-forms.text-input name="name" label="名稱" />
   ```

4. **使用原生 SQL**
   ```
   ❌ DB::select("SELECT * FROM users WHERE id = $id")
   ✅ User::find($id)
   ```

---

## FAQ

### Q1：什麼時候需要手動執行指令？

**A：** 大部分情況下，AI 會自動選擇合適的流程。但以下情況可以手動執行：

- 需要單獨掃描組件：`/components`
- 需要單獨分析影響：`/impact-analysis`
- 需要單獨審查：`/review-staged`

---

### Q2：TDD 流程太慢，可以跳過嗎？

**A：** 強烈建議**不要跳過**。TDD 的好處：

- 確保程式碼符合預期
- 防止未來的改動破壞功能
- 讓重構更有信心

如果真的需要快速修改，可以說「直接改」：
```
「直接幫我改這個方法名稱」
```

---

### Q3：Skills 和 Commands 有什麼差別？

**A：**

| 比較 | Skills | Commands |
|-----|--------|----------|
| 觸發方式 | 自動（根據關鍵字） | 手動（輸入 `/指令`） |
| 範圍 | 完整流程 | 單一任務 |
| 範例 | 說「開發產品功能」觸發 new-feature | 輸入 `/components` 掃描組件 |

---

### Q4：我的任務沒有被自動識別怎麼辦？

**A：** 嘗試使用對應的關鍵字：

| 任務類型 | 關鍵字 |
|---------|--------|
| 新功能 | 新功能、新增、開發、實作、建立 |
| Bug 修復 | bug、錯誤、問題、修復、fix |
| UI 開發 | 頁面、表單、畫面、UI、前端 |
| 重構 | 重構、優化、調整、改善、效能 |
| 架構 | 架構、設計、評估、比較 |
| 測試 | 測試、test、TDD、單元測試 |

---

### Q5：如何知道目前有哪些共用組件？

**A：** 執行 `/components` 指令，會列出所有可用的 Blade 組件，包括：

- 表單組件（text-input, select-field, textarea 等）
- UI 組件（page-header, breadcrumb, alert 等）
- 操作組件（action-buttons, back-button 等）

---

## 快速參考卡

```
┌────────────────────────────────────────────────────────────┐
│                    AI 開發流程速查                          │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  📝 開發新功能？                                            │
│     → 說「開發 XXX 功能」，AI 自動執行完整流程              │
│                                                            │
│  🐛 修復 Bug？                                              │
│     → 說「修復 XXX 問題」，AI 自動分析並修復                │
│                                                            │
│  🎨 開發 UI？                                               │
│     → 先執行 /components，再說「做 XXX 頁面」               │
│                                                            │
│  🔧 重構程式碼？                                            │
│     → 說「重構 XXX」，AI 自動分析影響並安全重構             │
│                                                            │
│  ✅ 提交程式碼？                                            │
│     → git add . → /review-staged → git commit              │
│                                                            │
├────────────────────────────────────────────────────────────┤
│                     常用指令                                │
├────────────────────────────────────────────────────────────┤
│  /components       掃描可用組件（UI 開發必用）              │
│  /impact-analysis  分析影響範圍（修改前必用）               │
│  /review-staged    審查暫存區（提交前必用）                 │
│  /tdd              測試驅動開發                             │
│  /bdd              行為驅動開發                             │
│  /backend-spec     後端規格                                 │
│  /frontend-spec    前端規格                                 │
└────────────────────────────────────────────────────────────┘
```

---

**📚 進階閱讀：**
- [架構規範](/.claude/standards/architecture.md)
- [工作流程](/.claude/standards/workflow.md)
- [命名規範](/.claude/standards/naming.md)
- [安全規範](/.claude/standards/security.md)

---

**版本記錄：**
- v1.0 (2025-12-23): 初版建立
