---
description: 使用測試驅動開發 (TDD) 流程，先寫測試再實作功能。
---

## User Input

```text
$ARGUMENTS
```

You **MUST** use the user input to understand what feature/functionality to develop.

## Goal

執行完整的 TDD（測試驅動開發）流程：

1. **紅燈 (Red)** - 先寫失敗的測試
2. **綠燈 (Green)** - 寫最少的程式碼讓測試通過
3. **重構 (Refactor)** - 改善程式碼品質，保持測試通過

## ⚠️ 重要限制：僅限後端測試

**TDD 只適用於後端邏輯測試，不測試前端。**

### ✅ TDD 測試範圍（後端）

- **Service** - 商業邏輯
- **Repository** - 資料存取邏輯
- **Model** - Model 方法、Accessor、Mutator、Scope
- **Controller** - HTTP 請求處理、回應狀態碼、JSON 回應
- **FormRequest** - 驗證規則
- **Policy** - 權限邏輯

### ❌ TDD 不測試（前端）

- Blade 視圖渲染
- HTML 結構
- CSS 樣式
- JavaScript 行為
- 表單 UI 元素
- 頁面文字內容

### Feature Test 注意事項

Feature Test 應測試 API 行為，不應驗證：
- `assertSee()` 檢查頁面文字（除非是錯誤訊息）
- `assertViewHas()` 檢查視圖變數（除非是關鍵資料）
- 視圖渲染結果

## Operating Constraints

- **測試先行**: 必須先完成測試才能寫實作程式碼
- **最小實作**: 只寫讓測試通過的最少程式碼
- **持續驗證**: 每個步驟都要執行測試確認狀態
- **後端專注**: 只測試後端邏輯，不測試 UI

## Execution Steps

### 1. 分析需求

從 `$ARGUMENTS` 解析要開發的功能：

**支援格式：**
```bash
# 功能描述
/tdd 建立產品的 Service 方法

# 指定類別和方法
/tdd ProductService::createProduct

# 指定測試類型
/tdd unit ProductRepository::getActiveProducts
/tdd feature 產品列表 API
```

### 2. 決定測試類型

| 類型 | 路徑 | 用途 |
|-----|------|------|
| **Unit** | `tests/Unit/` | Service、Repository、單一類別邏輯 |
| **Feature** | `tests/Feature/` | Controller、API、完整請求流程 |

### 3. 紅燈階段 - 撰寫測試

**3.1 建立測試檔案**

```bash
# Unit Test
php artisan make:test Services/ProductServiceTest --unit

# Feature Test
php artisan make:test Product/ProductControllerTest
```

**3.2 撰寫測試案例**

遵循以下原則：
- 測試方法名稱使用 `test_` 前綴 + snake_case
- 每個測試只驗證一件事
- 使用 AAA 模式：Arrange（準備）、Act（執行）、Assert（斷言）
- 變數使用 snake_case
- 加上繁體中文註解

**測試案例清單（必須涵蓋）：**
- 正常情況 (Happy Path)
- 邊界條件 (Edge Cases)
- 錯誤處理 (Error Cases)
- 驗證失敗 (Validation Failures)

**3.3 執行測試確認失敗**

```bash
php artisan test --filter=測試類別名稱
```

確認測試失敗（紅燈），才能進入下一階段。

### 4. 綠燈階段 - 最小實作

**4.1 建立必要檔案**

根據三層架構建立：
- Controller（如果是 Feature Test）
- Service（商業邏輯）
- Repository（資料存取）
- Model（如需要）
- FormRequest（如需要）

**4.2 實作最少程式碼**

- 只寫讓測試通過的程式碼
- 不要過度設計
- 不要加入額外功能

**4.3 執行測試確認通過**

```bash
php artisan test --filter=測試類別名稱
```

確認所有測試通過（綠燈），才能進入下一階段。

### 5. 重構階段

**5.1 檢查程式碼品質**

- 命名是否清晰
- 是否有重複程式碼
- 是否符合專案規範
- 是否需要加入註解

**5.2 執行重構**

- 提取方法
- 改善命名
- 移除重複
- 加入適當註解

**5.3 確認測試仍然通過**

```bash
php artisan test --filter=測試類別名稱
```

### 6. 循環

如果還有更多測試案例要加，回到步驟 3 繼續 TDD 循環。

---

## Output Format

每個階段輸出：

```markdown
## TDD 進度報告

**功能**: [功能描述]
**目前階段**: [紅燈/綠燈/重構]

---

### 測試案例清單

| # | 測試案例 | 狀態 |
|---|---------|------|
| 1 | test_create_product_with_valid_data | ✅/❌/⏳ |
| 2 | test_create_product_fails_without_name | ✅/❌/⏳ |

---

### 目前進行

**階段**: [紅燈] 撰寫測試
**檔案**: `tests/Unit/Services/ProductServiceTest.php`

[程式碼或說明]

---

### 測試執行結果

```bash
php artisan test --filter=ProductServiceTest

PASS/FAIL: [結果]
```

---

### 下一步

[下一個動作]
```

---

## Operating Principles

### TDD 原則

- **測試先行**: 絕對不能先寫實作再補測試
- **小步前進**: 一次只加一個測試案例
- **持續整合**: 每個步驟都要執行測試
- **勇於重構**: 綠燈後就是重構的最佳時機

### 測試品質

- **獨立性**: 測試之間不應該互相依賴
- **可重複**: 每次執行結果都應該一致
- **快速**: 單元測試應該在毫秒內完成
- **清晰**: 測試失敗時應該能立即知道問題

### 限制

- **NEVER** 跳過紅燈階段直接寫實作
- **NEVER** 一次寫太多測試案例
- **ALWAYS** 在綠燈後才進行重構
- **ALWAYS** 重構後確認測試仍然通過

## Context

$ARGUMENTS
