# AI 輔助開發流程指南

> **版本:** 2.0 | **最後更新:** 2025-12-26
>
> 使用 Claude Code 的 AI 輔助開發完整指南

---

## 目錄

1. [快速開始](#快速開始)
2. [核心概念](#核心概念)
3. [AI 工具總覽](#ai-工具總覽)
4. [Skills（自動流程）](#skills自動流程)
5. [Commands（指令）](#commands指令)
6. [Agents（代理）](#agents代理)
7. [MCP 工具](#mcp-工具)
8. [常見開發場景](#常見開發場景)
9. [最佳實踐](#最佳實踐)
10. [FAQ](#faq)

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
「幫我重構這段程式碼」       → AI 自動執行重構流程
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
               │       PRD         │  ← 產品需求文件
               └───────────────────┘
```

### 檔案結構

```
.claude/
├── skills/           # 自動觸發的工作流程（4 個）
│   ├── new-feature/  # 新功能開發
│   ├── bug-fix/      # Bug 修復
│   ├── testing/      # 測試開發
│   └── refactor/     # 重構/優化
│
├── commands/         # 明確調用的指令（8 個）
│   ├── create-prd.md     # 產品需求文件
│   ├── bdd.md            # 行為驅動開發
│   ├── backend-spec.md   # 後端規格
│   ├── frontend-spec.md  # 前端規格
│   ├── tdd.md            # 測試驅動開發
│   ├── components.md     # 掃描共用組件
│   ├── impact-analysis.md # 影響範圍分析
│   └── review-staged.md  # 審查暫存區
│
├── agents/           # AI 代理（2 個）
│   ├── coder.md      # 程式碼實作
│   └── ui-designer.md # UI 設計
│
└── standards/        # 開發規範
    ├── backend.md        # 後端規範
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

---

## Skills（自動流程）

### 什麼是 Skills？

Skills 是**自動觸發的完整開發流程**。當你的訊息包含特定關鍵字時，AI 會自動識別並執行對應的流程。

### 四大 Skills 一覽

| Skill | 觸發關鍵字 | 執行流程 |
|-------|-----------|----------|
| **new-feature** | 新功能、新增、開發、實作、建立 | PRD → Spec → BDD → TDD → Code → Review |
| **bug-fix** | bug、錯誤、問題、修復、fix、壞掉 | 分析 → 影響評估 → TDD → 修復 → Review |
| **testing** | 測試、test、TDD、單元測試、Feature Test | 分析 → TDD → 實作 → Review |
| **refactor** | 重構、優化、調整、改善、瘦身、拆分、效能 | 影響分析 → 回歸測試 → 重構 → Review |

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

#### 1. `/create-prd` - 產品需求文件

**用途：** 建立產品需求文件（PRD）

**使用時機：** 開發新功能前，先定義產品需求

**範例：**
```
/create-prd 會員點數系統

需求背景：
- 目前會員沒有忠誠度機制，回購率偏低
- 希望透過點數獎勵提高會員黏著度

核心功能：
- 消費累積點數（每 100 元 = 1 點）
- 點數可折抵消費（每 10 點 = 1 元）
- 點數有效期限 1 年
- 會員可查看點數餘額和歷史紀錄

使用者角色：
- 一般會員：累積、使用、查詢點數
- 管理員：調整點數規則、手動補發點數
```

---

#### 2. `/bdd` - 行為驅動開發

**用途：** 用 Gherkin 語法定義功能行為

**使用時機：** 定義 PRD 後，撰寫行為規格

**範例：**
```
/bdd 點數折抵功能

場景需求：
- 結帳時可選擇使用點數折抵
- 折抵金額不能超過訂單金額的 50%
- 點數不足時顯示提示訊息
- 折抵後顯示實際付款金額

邊界條件：
- 點數餘額為 0 時，折抵選項應該 disabled
- 訂單金額低於 100 元時不能使用點數
```

**輸出範例：**
```gherkin
# language: zh-TW
功能: 點數折抵
  作為一個會員
  我想要使用點數折抵消費金額
  以便節省購物支出

  場景: 成功使用點數折抵
    假設 我的點數餘額為 500 點
    而且 我的購物車金額為 1000 元
    當 我選擇使用 100 點折抵
    那麼 折抵金額應為 10 元
    而且 應付金額應為 990 元
    而且 點數餘額應更新為 400 點

  場景: 折抵金額超過上限
    假設 我的點數餘額為 10000 點
    而且 我的購物車金額為 500 元
    當 我嘗試使用 5000 點折抵
    那麼 應顯示錯誤「折抵金額不能超過訂單金額的 50%」
    而且 最多只能折抵 250 元
```

---

#### 3. `/backend-spec` - 後端規格

**用途：** 撰寫後端技術規格文檔

**使用時機：** 定義 BDD 後，規劃後端架構

**範例：**
```
/backend-spec 會員點數系統

功能範圍：
- 點數累積（訂單完成後自動累積）
- 點數使用（結帳時折抵）
- 點數查詢（餘額、歷史紀錄）
- 點數過期處理（每日排程檢查）

已知限制：
- 需要與現有 Order、Member Model 整合
- 點數變動需要記錄完整的異動歷程
- 要考慮高併發下的點數扣減問題
```

**產出內容：**
- 資料表設計（points、point_transactions）
- Model 規格（$fillable、關聯、Scope）
- Repository 方法（getBalance、getHistory）
- Service 方法與流程（earn、redeem、expire）
- Controller 設計
- 驗證規則

---

#### 4. `/frontend-spec` - 前端規格

**用途：** 撰寫前端技術規格文檔

**使用時機：** 開發 UI 前，定義頁面架構

**範例：**
```
/frontend-spec 會員點數頁面

頁面清單：
1. 點數總覽頁 - 顯示目前餘額、即將過期點數、快速連結
2. 點數歷史頁 - 列出所有累積/使用/過期紀錄，支援日期篩選
3. 結帳點數折抵區塊 - 嵌入現有結帳頁面

設計需求：
- 點數餘額要夠醒目（大字體、主色調）
- 即將過期的點數用警示色標示
- 歷史紀錄用不同顏色區分累積(綠)/使用(藍)/過期(灰)
- 手機版要能正常操作
```

**產出內容：**
- 頁面結構圖（版面配置、區塊劃分）
- 組件使用清單（需要用到哪些共用組件）
- 表單欄位定義（輸入框、選單等）
- 使用者操作流程（點擊路徑、互動行為）

---

#### 5. `/tdd` - 測試驅動開發

**用途：** 執行 TDD 流程（紅燈 → 綠燈 → 重構）

**使用時機：** 定義規格後，開始實作前

**範例：**
```
/tdd PointService 點數折抵功能

測試案例：
1. 正常折抵 - 100 點折抵 10 元
2. 餘額不足 - 點數不夠時拋出例外
3. 超過上限 - 折抵金額超過訂單 50% 時拋出例外
4. 併發處理 - 同時扣減點數時不會超扣

相關方法：
- redeem(int $member_id, int $points, int $order_id): PointTransaction
- validateRedemption(int $member_id, int $points, int $order_amount): bool
```

**其他範例：**
```
/tdd unit PointRepository::getBalance
/tdd feature POST /api/points/redeem
```

**流程：**
1. **紅燈** - 先寫失敗的測試
2. **綠燈** - 寫最少程式碼讓測試通過
3. **重構** - 改善程式碼，保持測試通過

---

#### 6. `/components` - 掃描共用組件

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
└── x-section        區塊容器

操作組件
├── x-action-buttons 操作按鈕組（查看/編輯/刪除）
└── x-back-button    返回按鈕
```

---

#### 7. `/impact-analysis` - 影響範圍分析

**用途：** 分析程式碼變更的影響範圍

**使用時機：** 修改現有程式碼前、重構前、修復 Bug 前

**範例：**
```
/impact-analysis OrderService::calculateTotal

變更內容：
- 要在 calculateTotal 方法中加入點數折抵邏輯
- 需要新增 $points_to_redeem 參數
- 回傳值要包含折抵金額明細

擔心的問題：
- 不確定有多少地方呼叫這個方法
- 改了參數會不會影響現有功能
- 有哪些測試需要更新
```

**其他範例：**
```
/impact-analysis app/Models/Order.php
/impact-analysis Member::points 關聯
```

**輸出內容：**
- 依賴關係圖（誰呼叫這個方法/類別）
- 受影響的檔案清單（Controller、View、其他 Service）
- 需要執行的測試（列出相關測試檔案）
- 風險等級評估（低/中/高 + 建議）

---

#### 8. `/review-staged` - 審查暫存區

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

## Agents（代理）

### 什麼是 Agents？

Agents 是**專業的 AI 代理**，各自負責特定類型的任務。通常由 AI 在執行 Skill 時自動調用。

### 核心 Agents

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

#### 🎨 UI Designer（UI 設計師）

**職責：** 設計直觀且美觀的使用者介面

**何時被調用：**
- 開發前端頁面時
- 設計 UI 組件時

**遵循原則：**
- 優先使用共用組件
- 遵循 Tailwind CSS 規範
- 響應式設計

---

## MCP 工具

### 什麼是 MCP 工具？

MCP (Model Context Protocol) 工具是增強 AI 能力的外部服務。

### 可用的 MCP 工具

| 工具 | 用途 | 連結 |
|-----|------|------|
| **Sequential Thinking** | 結構化思考、問題分解 | [Smithery](https://smithery.ai/server/@smithery-ai/server-sequential-thinking) |
| **Tailwind CSS MCP** | Tailwind CSS 樣式生成 | [NPM](https://www.npmjs.com/package/tailwindcss-mcp-server) |
| **Context7 MCP** | 提供最新的文檔內容 | [Smithery](https://smithery.ai/server/@upstash/context7-mcp) |

### 使用時機

- **Sequential Thinking**：分析複雜問題、拆解需求、規劃架構
- **Tailwind CSS MCP**：查詢 Tailwind 類別、轉換 CSS
- **Context7 MCP**：獲取最新的程式庫文檔

---

## 常見開發場景

### 場景 A：新功能開發

**AI 會先判斷功能類型，再執行對應流程：**

#### A-1：純後端功能（API、排程、資料處理）

**範例 prompt：**
```
幫我開發一個「訂單匯出」功能：
- 管理員可以選擇日期範圍匯出訂單資料
- 支援 CSV 和 Excel 兩種格式
- 大量資料時使用背景任務處理，完成後發 Email 通知
- 匯出檔案保留 7 天後自動刪除
```

**執行流程：**
```
1. Sequential Thinking → 分析需求、判斷類型
2. /create-prd        → 建立產品需求文件
3. /backend-spec      → 產生後端規格
4. /bdd               → 定義行為（Given/When/Then）
5. /tdd               → 先寫測試（紅燈）
6. coder agent        → 實作（綠燈→重構）
7. reviewer agent     → 程式碼審查
```

#### A-2：純前端功能（頁面、樣式、組件）

**範例 prompt：**
```
幫我優化「產品列表」頁面的 UI：
- 目前表格太擠，希望增加欄位間距
- 新增「批次操作」功能列（勾選多筆後可批次刪除、批次上架）
- 狀態欄位改用顏色標籤顯示（上架=綠色、下架=灰色、缺貨=紅色）
- 手機版要能正常瀏覽，表格改成卡片式呈現
```

**執行流程：**
```
1. Sequential Thinking → 分析需求、判斷類型
2. /create-prd        → 建立產品需求文件
3. /frontend-spec     → 產生前端規格
4. /components        → 掃描可用組件
5. ui-designer agent  → 設計 UI
6. coder agent        → 實作程式碼
7. reviewer agent     → 程式碼審查
```

#### A-3：全端功能（完整 CRUD、新模組）

**範例 prompt：**
```
幫我開發一個「優惠券管理」模組：

功能需求：
- 管理員可以建立、編輯、刪除優惠券
- 優惠券類型：固定金額折扣、百分比折扣、免運費
- 可設定使用條件：最低消費金額、指定商品類別、會員等級限定
- 可設定使用期限和總使用次數上限

頁面需求：
- 優惠券列表頁（含搜尋、篩選、分頁）
- 優惠券新增/編輯表單頁
- 優惠券使用紀錄查詢頁
```

**執行流程：**
```
1. Sequential Thinking → 分析需求、判斷類型
2. /create-prd        → 建立產品需求文件
3. /backend-spec      → 產生後端規格
4. /frontend-spec     → 產生前端規格
5. /bdd               → 定義行為（Given/When/Then）
6. /tdd               → 先寫測試（紅燈）
7. ui-designer agent  → 設計 UI
8. coder agent        → 實作（綠燈→重構）
9. reviewer agent     → 程式碼審查
```

---

### 場景 B：Bug 修復

**AI 會先判斷 Bug 類型，再執行對應流程：**

#### B-1：純後端 Bug（邏輯錯誤、資料問題）

**範例 prompt：**
```
訂單金額計算有 bug：

問題描述：
- 當使用「滿千折百」優惠券時，折扣金額計算錯誤
- 訂單金額 1500 元，應該折 100 元，但實際折了 150 元

重現步驟：
1. 建立一張 1500 元的訂單
2. 套用優惠券代碼 "SAVE100"
3. 查看訂單金額，顯示 1350 元（應該是 1400 元）

相關程式碼應該在 OrderService 的 calculateDiscount 方法
```

**執行流程：**
```
1. Sequential Thinking → 分析問題根因、判斷類型
2. /impact-analysis    → 評估影響範圍
3. /tdd               → 先寫失敗測試重現 bug
4. coder agent        → 修復讓測試通過
5. reviewer agent     → 程式碼審查
```

#### B-2：純前端 Bug（顯示問題、樣式錯誤）

**範例 prompt：**
```
產品列表頁面在手機上顯示異常：

問題描述：
- iPhone 14 (390px 寬) 上，表格欄位重疊無法閱讀
- 操作按鈕被切掉一半
- 分頁元件跑到畫面外

預期行為：
- 手機版應該改成卡片式排列
- 每張卡片顯示：圖片、名稱、價格、狀態、操作按鈕
- 分頁改成「上一頁/下一頁」簡化版

請檢查 resources/views/admin/products/index.blade.php
```

**執行流程：**
```
1. Sequential Thinking → 分析問題根因、判斷類型
2. /impact-analysis    → 評估影響範圍
3. /components        → 確認共用組件使用
4. ui-designer agent  → 修正 UI
5. coder agent        → 實作修復
6. reviewer agent     → 程式碼審查
```

#### B-3：全端 Bug（資料傳遞、渲染問題）

**範例 prompt：**
```
編輯產品後，頁面顯示的資料沒有更新：

問題描述：
- 在編輯頁修改產品名稱後按儲存
- 系統顯示「儲存成功」
- 但回到列表頁，產品名稱還是舊的
- 重新整理頁面後才會顯示新名稱

重現步驟：
1. 進入產品編輯頁 /admin/products/1/edit
2. 修改產品名稱為「新名稱」
3. 點擊儲存，顯示成功訊息
4. 頁面自動跳轉回列表，但名稱還是舊的

懷疑是 Controller redirect 後沒有清除快取，或是前端沒有重新載入資料
```

**執行流程：**
```
1. Sequential Thinking → 分析問題根因、判斷類型
2. /impact-analysis    → 評估影響範圍
3. /tdd               → 先寫失敗測試重現 bug
4. ui-designer agent  → 修正 UI（如需要）
5. coder agent        → 修復讓測試通過
6. reviewer agent     → 程式碼審查
```

---

### 場景 C：測試開發

**範例 prompt：**
```
幫我補齊 OrderService 的單元測試：

目前情況：
- OrderService 有 8 個 public 方法
- 只有 createOrder 和 cancelOrder 有寫測試
- 測試覆蓋率只有 25%

需要測試的方法：
1. calculateTotal() - 計算訂單總額（含稅、折扣）
2. applyDiscount() - 套用優惠券折扣
3. validateStock() - 驗證庫存是否足夠
4. updateStatus() - 更新訂單狀態（需測試狀態轉換規則）

請特別注意邊界條件：
- 折扣金額不能超過訂單金額
- 庫存為 0 時應該拋出例外
- 狀態只能按照 pending → processing → shipped → completed 順序轉換
```

**執行流程：**
```
1. Sequential Thinking → 分析測試範圍（限後端邏輯）
2. /tdd               → 撰寫測試案例
3. coder agent        → 確保測試通過
4. reviewer agent     → 審查測試品質
```

---

### 場景 D：重構

**範例 prompt：**
```
幫我重構 OrderService，目前程式碼有以下問題：

現況問題：
- createOrder 方法超過 200 行，做太多事情
- 價格計算邏輯散落在多個方法中，重複且難維護
- 庫存檢查和扣減邏輯耦合在一起

重構目標：
1. 把 createOrder 拆分成多個小方法
2. 抽出 PriceCalculator 類別，統一處理價格計算
3. 抽出 StockManager 類別，處理庫存相關邏輯
4. 確保重構後所有現有測試仍然通過

相關檔案：
- app/Services/OrderService.php（主要重構目標）
- tests/Unit/Services/OrderServiceTest.php（現有測試）
```

**執行流程：**
```
1. Sequential Thinking → 分析重構策略
2. /impact-analysis   → 評估影響範圍
3. /tdd              → 先寫回歸測試
4. coder agent       → 重構（保持測試通過）
5. reviewer agent    → 審查
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
- 需要建立需求文件：`/create-prd`

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

### Q3：我的任務沒有被自動識別怎麼辦？

**A：** 嘗試使用對應的關鍵字：

| 任務類型 | 關鍵字 |
|---------|--------|
| 新功能 | 新功能、新增、開發、實作、建立 |
| Bug 修復 | bug、錯誤、問題、修復、fix |
| 重構 | 重構、優化、調整、改善、效能 |
| 測試 | 測試、test、TDD、單元測試 |

---

### Q5：如何知道目前有哪些共用組件？

**A：** 執行 `/components` 指令，會列出所有可用的 Blade 組件，包括：

- 表單組件（text-input, select-field, textarea 等）
- UI 組件（page-header, breadcrumb, alert 等）
- 操作組件（action-buttons, back-button 等）

---

**版本記錄：**
- v2.0 (2025-12-26): 與 CLAUDE.md v3.1 同步，修正流程與 Skills 定義一致，豐富 Commands 與場景範例
- v1.0 (2025-12-23): 初版建立
