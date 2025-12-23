---
description: 使用標準化流程修復 Bug：分析問題 → 影響評估 → 測試重現 → 修復 → 審查。
---

## User Input

```text
$ARGUMENTS
```

You **MUST** use the user input to understand what bug to fix.

## Goal

執行完整的 Bug 修復流程：

1. **Sequential Thinking** - 結構化分析問題根因
2. **Impact Analysis** - 評估修改影響範圍
3. **TDD** - 先寫失敗測試重現 Bug
4. **Coder** - 修復讓測試通過
5. **Reviewer** - 程式碼審查

## Operating Constraints

- **分析先行**: 必須先理解問題根因再開始修復
- **測試重現**: 必須有測試能重現 Bug 才能修復
- **最小修改**: 只修改必要的程式碼，避免引入新問題
- **持續驗證**: 每個步驟都要確認不破壞現有功能

## Execution Steps

### 1. 解析 Bug 描述

從 `$ARGUMENTS` 解析 Bug 資訊：

**支援格式：**
```bash
# Bug 描述
/bug-fix 產品列表載入很慢

# 指定錯誤檔案
/bug-fix app/Services/ProductService.php 價格計算錯誤

# 指定錯誤訊息
/bug-fix "Call to undefined method" ProductController

# 指定測試失敗
/bug-fix test_create_product_fails ProductServiceTest

# 錯誤行為描述
/bug-fix 訂單金額應該是 100 但顯示 90
```

### 2. Sequential Thinking - 問題分析

使用 `mcp__smithery-ai-server-sequential-thinking__sequentialthinking` 進行結構化分析：

**分析要點：**

| 步驟 | 分析內容 |
|-----|---------|
| **1. 現象理解** | Bug 的具體表現是什麼？ |
| **2. 預期行為** | 正確的行為應該是什麼？ |
| **3. 重現步驟** | 如何重現這個 Bug？ |
| **4. 影響範圍** | 這個 Bug 影響哪些功能？ |
| **5. 根因假設** | 可能的原因有哪些？ |
| **6. 驗證方式** | 如何驗證修復成功？ |

**輸出格式：**
```markdown
## 🔍 Bug 分析報告

**Bug 描述**: [簡要描述]
**嚴重程度**: [CRITICAL/HIGH/MEDIUM/LOW]

### 現象
[具體的錯誤表現]

### 預期行為
[正確應該如何運作]

### 重現步驟
1. [步驟 1]
2. [步驟 2]
3. [觀察到錯誤]

### 根因分析
| 假設 | 可能性 | 驗證方式 |
|-----|-------|---------|
| [假設 1] | HIGH/MEDIUM/LOW | [如何驗證] |

### 相關檔案
| 檔案 | 原因 |
|-----|------|
| [檔案路徑] | [為何相關] |
```

### 3. Impact Analysis - 影響評估

執行影響範圍分析（參照 `/impact-analysis` 流程）：

**3.1 定位相關檔案**
- 根據 Bug 描述搜尋可能的問題檔案
- 分析錯誤訊息中的檔案路徑和行號
- 追蹤呼叫鏈找出問題源頭

**3.2 依賴關係分析**
- 向上依賴：誰呼叫了有問題的程式碼
- 向下依賴：有問題的程式碼依賴什麼

**3.3 測試影響**
- 列出需要執行的現有測試
- 標記可能會失敗的測試

**輸出格式：**
```markdown
## 📊 影響評估

### 直接影響檔案
| 檔案 | 修改可能性 | 原因 |
|-----|-----------|------|
| [檔案] | HIGH/MEDIUM/LOW | [原因] |

### 間接影響檔案
| 檔案 | 關係 |
|-----|------|
| [檔案] | [依賴關係] |

### 風險等級
**[CRITICAL/HIGH/MEDIUM/LOW]**

原因：[為何是這個等級]

### 建議執行測試
```bash
php artisan test --filter=[測試名稱]
```
```

### 4. TDD - 測試重現 Bug

**4.1 撰寫失敗測試**

建立能重現 Bug 的測試案例：

```php
/**
 * 測試 [Bug 描述]
 *
 * Bug: [簡要說明]
 * 預期: [正確行為]
 * 實際: [錯誤行為]
 */
public function test_[bug_scenario](): void
{
    // Arrange - 準備測試資料
    $data = [...];

    // Act - 執行觸發 Bug 的操作
    $result = $this->service->method($data);

    // Assert - 驗證正確行為（目前會失敗）
    $this->assertEquals($expected, $result);
}
```

**4.2 執行測試確認失敗**

```bash
php artisan test --filter=[測試名稱]
```

- ✅ 測試失敗 = Bug 已被重現，可以進入修復階段
- ❌ 測試通過 = 測試未正確重現 Bug，需要調整測試

**4.3 測試案例清單**

| # | 測試案例 | 目的 | 狀態 |
|---|---------|------|------|
| 1 | test_bug_scenario | 重現 Bug | ❌ 失敗 |
| 2 | test_edge_case | 邊界條件 | ⏳ 待寫 |
| 3 | test_regression | 確保不復發 | ⏳ 待寫 |

### 5. Coder - 修復 Bug

使用 `Task agent: coder` 進行修復：

**5.1 修復原則**

- **最小修改**: 只改必要的程式碼
- **保持風格**: 遵循專案程式碼規範
- **加入註解**: 說明修復原因和方式
- **避免副作用**: 不引入新問題

**5.2 修復步驟**

1. 定位問題程式碼
2. 分析問題根因
3. 實作最小修復
4. 執行測試確認通過
5. 執行相關測試確認無回歸

**5.3 執行測試**

```bash
# 確認 Bug 修復測試通過
php artisan test --filter=[bug_test]

# 確認相關測試通過
php artisan test --filter=[related_tests]

# 確認無回歸
php artisan test
```

### 6. Reviewer - 程式碼審查

使用 `Task agent: reviewer` 進行審查：

**審查重點：**

| 項目 | 檢查內容 |
|-----|---------|
| **正確性** | 修復是否解決根因？ |
| **完整性** | 是否有遺漏的邊界條件？ |
| **安全性** | 是否引入安全風險？ |
| **效能** | 是否影響效能？ |
| **規範** | 是否符合程式碼規範？ |
| **測試** | 測試是否足夠？ |

---

## Output Format

```markdown
# 🐛 Bug 修復報告

**Bug**: [Bug 描述]
**狀態**: ✅ 已修復 / ⏳ 進行中 / ❌ 無法修復
**嚴重程度**: [CRITICAL/HIGH/MEDIUM/LOW]

---

## 📋 修復摘要

### 問題根因
[簡要說明問題原因]

### 修復方式
[簡要說明如何修復]

### 修改檔案

| 檔案 | 變更類型 | 說明 |
|-----|---------|------|
| [檔案路徑] | 修改/新增/刪除 | [變更說明] |

---

## 🧪 測試結果

### 新增測試

| 測試 | 目的 | 結果 |
|-----|------|------|
| test_[name] | [目的] | ✅ 通過 |

### 回歸測試

```bash
php artisan test --filter=[filter]
# 結果: X tests, X assertions, 0 failures
```

---

## ⚠️ 注意事項

- [修復後需要注意的事項]
- [可能的副作用]
- [需要手動驗證的部分]

---

## 📝 變更清單

### [檔案名稱]

```diff
- [舊程式碼]
+ [新程式碼]
```

---

## ✅ 檢查清單

- [ ] Bug 測試通過
- [ ] 相關測試通過
- [ ] 完整測試套件通過
- [ ] 程式碼符合規範
- [ ] 已加入必要註解
- [ ] 無安全性問題
```

---

## Operating Principles

### 修復原則

- **理解優先**: 先完全理解問題再動手修復
- **測試驅動**: 一定要有測試能重現 Bug
- **最小侵入**: 只改必要的部分
- **防止復發**: 加入測試防止 Bug 再次出現

### 品質要求

- **所有測試必須通過**: 包含新測試和現有測試
- **程式碼必須符合規範**: 命名、註解、架構
- **必須經過審查**: 使用 reviewer agent

### 限制

- **NEVER** 不寫測試就修復 Bug
- **NEVER** 只修症狀不修根因
- **NEVER** 引入新的 Bug
- **ALWAYS** 先理解再修復
- **ALWAYS** 執行完整測試套件
- **ALWAYS** 記錄修復過程

---

## Integration with Workflow

### 完整流程

```
Sequential Thinking (分析)
    ↓
/impact-analysis (評估)
    ↓
TDD 紅燈 (測試重現 Bug)
    ↓
Coder (修復)
    ↓
TDD 綠燈 (測試通過)
    ↓
Reviewer (審查)
    ↓
/review-staged (提交審查)
```

### 搭配其他命令

```bash
# 修復前先分析影響
/impact-analysis [相關檔案]

# 修復完成後審查
/review-staged

# 如果需要重構
/tdd [重構目標]
```

## Context

$ARGUMENTS
