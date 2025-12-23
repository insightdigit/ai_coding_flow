---
description: 分析程式碼變更的影響範圍，識別受影響的檔案、測試和依賴關係，在修改前評估風險。
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Goal

在進行程式碼修改之前，分析變更將影響的範圍，識別：
- 直接受影響的檔案和類別
- 依賴關係鏈（誰依賴這個、這個依賴誰）
- 需要執行的測試
- 潛在風險和建議

**使用時機：**
- 重構現有功能前
- 修改核心類別前
- 修復 Bug 前評估影響
- 評估技術債清理範圍

## Operating Constraints

**STRICTLY READ-ONLY**: 不修改任何檔案，只輸出分析報告。

**分析深度控制**: 根據變更類型自動調整：
- 單一檔案修改：深度 2（直接依賴）
- 多檔案修改：深度 3（間接依賴）
- 架構層級修改：深度 4（完整影響鏈）

## Execution Steps

### 1. 解析輸入

從 `$ARGUMENTS` 解析要分析的目標：

**支援格式：**
```bash
# 單一檔案
/impact-analysis app/Services/ProductService.php

# 多個檔案
/impact-analysis app/Services/ProductService.php app/Repositories/ProductRepository.php

# 目錄
/impact-analysis app/Services/

# 類別名稱
/impact-analysis ProductService

# 方法
/impact-analysis ProductService::createProduct

# 功能描述（自動搜尋相關檔案）
/impact-analysis "修改產品價格計算邏輯"
```

### 2. 定位目標檔案

**2.1 如果是檔案路徑或目錄：**
- 驗證檔案存在
- 如果是目錄，列出所有 PHP/Blade 檔案

**2.2 如果是類別名稱：**
- 搜尋 `app/` 目錄下的 PHP 檔案
- 找出包含該類別定義的檔案

**2.3 如果是功能描述：**
- 使用關鍵字搜尋相關檔案
- 分析 Controller、Service、Repository、Model 層
- 列出候選檔案讓使用者確認

### 3. 分析依賴關係

**3.1 向上依賴（誰依賴這個檔案）**

```php
// 搜尋 use 語句
use App\Services\ProductService;

// 搜尋類型提示
public function __construct(ProductService $service)

// 搜尋 Blade 中的組件使用
<x-component-name />
```

**3.2 向下依賴（這個檔案依賴誰）**

```php
// 分析 use 語句
use App\Repositories\ProductRepository;
use App\Models\Product;

// 分析注入的依賴
private ProductRepository $repository;
```

**3.3 建立依賴圖**

```
ProductController
    └─→ ProductService (向上依賴)
            └─→ ProductRepository (向下依賴)
                    └─→ Product Model (向下依賴)
```

### 4. 識別受影響的層級

根據專案的三層架構，分析影響範圍：

| 變更層級 | 直接影響 | 間接影響 |
|---------|---------|---------|
| **Model** | Repository, Service | Controller, View, Test |
| **Repository** | Service | Controller, View, Test |
| **Service** | Controller | View, Test |
| **Controller** | Route, View | Test |
| **View/Blade** | - | Test |
| **共用組件** | 所有使用該組件的 View | Controller, Test |

### 5. 搜尋相關測試

**5.1 搜尋測試檔案**

```bash
# 單元測試
tests/Unit/{ClassName}Test.php

# 功能測試
tests/Feature/{FeatureName}Test.php

# 搜尋測試中使用該類別的檔案
grep -r "ClassName" tests/
```

**5.2 標記測試類型**
- ⚠️ 必須執行：直接測試目標類別
- 📋 建議執行：測試相關功能
- ℹ️ 可選執行：間接相關測試

### 6. 風險評估

**6.1 風險等級判定**

| 風險等級 | 條件 |
|---------|------|
| **CRITICAL** | 修改 Model 的 $fillable/$guarded、修改資料庫遷移、修改核心 Service |
| **HIGH** | 修改 Repository 查詢邏輯、修改 FormRequest 驗證、修改共用組件 |
| **MEDIUM** | 修改 Controller 邏輯、修改 Blade 模板 |
| **LOW** | 新增方法、修改註解、修改樣式 |

**6.2 檢查潛在問題**

- [ ] 是否修改 `$fillable` 或 `$guarded`？（Mass Assignment 風險）
- [ ] 是否修改資料庫欄位？（需要 Migration）
- [ ] 是否修改 API 回傳格式？（可能破壞前端）
- [ ] 是否修改共用組件參數？（影響所有使用處）
- [ ] 是否修改 Route 路徑？（需要更新連結）
- [ ] 是否修改 Event/Listener？（影響非同步邏輯）

### 7. 輸出分析報告

```markdown
# 影響範圍分析報告

**分析目標**: [檔案/類別/方法名稱]
**分析時間**: [日期時間]
**風險等級**: [CRITICAL/HIGH/MEDIUM/LOW]

---

## 📁 目標檔案

| 檔案路徑 | 類型 | 行數 |
|---------|------|------|
| app/Services/ProductService.php | Service | 150 |

---

## 🔗 依賴關係圖

```
[向上依賴 - 誰使用這個]
├── ProductController (app/Http/Controllers/ProductController.php)
│   └── 方法: index(), store(), update()
└── OrderService (app/Services/OrderService.php)
    └── 方法: calculateTotal()

[目標]
└── ProductService

[向下依賴 - 這個使用誰]
├── ProductRepository (app/Repositories/ProductRepository.php)
│   └── 方法: getFilteredProducts(), create()
└── Product (app/Models/Product.php)
    └── 屬性: $fillable, relationships
```

---

## 📊 受影響檔案清單

### 直接影響（必須檢查）

| 檔案 | 關係 | 原因 |
|-----|------|------|
| ProductController.php | 上游 | 依賴 ProductService |
| ProductRepository.php | 下游 | ProductService 依賴此類別 |

### 間接影響（建議檢查）

| 檔案 | 關係 | 原因 |
|-----|------|------|
| OrderService.php | 關聯 | 使用 ProductService 計算價格 |
| products/index.blade.php | View | 顯示產品資料 |

---

## 🧪 測試影響

### ⚠️ 必須執行

| 測試檔案 | 測試方法 | 原因 |
|---------|---------|------|
| ProductServiceTest.php | 全部 | 直接測試目標類別 |

### 📋 建議執行

| 測試檔案 | 測試方法 | 原因 |
|---------|---------|------|
| ProductControllerTest.php | test_index, test_store | 測試相關 Controller |

### ℹ️ 可選執行

| 測試檔案 | 原因 |
|---------|------|
| OrderServiceTest.php | 間接依賴 |

---

## ⚠️ 風險評估

### 潛在風險

| 風險 | 等級 | 說明 | 建議 |
|-----|------|------|------|
| 資料一致性 | HIGH | 修改會影響訂單計算 | 執行完整回歸測試 |
| API 契約 | MEDIUM | 可能影響前端顯示 | 確認回傳格式不變 |

### 檢查清單

- [ ] 確認所有測試通過
- [ ] 確認資料庫遷移已建立（如需要）
- [ ] 確認 API 文件已更新（如有變更）
- [ ] 確認相關 Blade 模板正常運作

---

## 📋 建議執行順序

1. **執行測試** (確認現有功能正常)
   ```bash
   php artisan test --filter=ProductServiceTest
   ```

2. **進行修改** (實作變更)

3. **執行受影響測試** (確認修改不破壞功能)
   ```bash
   php artisan test --filter=ProductServiceTest
   php artisan test --filter=ProductControllerTest
   ```

4. **執行完整測試** (確保系統穩定)
   ```bash
   php artisan test
   ```

---

## 🔄 下一步行動

根據分析結果，建議：

- [ ] 如需修改，使用 `Task agent: coder` 執行變更
- [ ] 修改完成後，使用 `Task agent: tester` 驗證
- [ ] 最後使用 `Task agent: reviewer` 審查程式碼
```

---

## Operating Principles

### 分析準則

- **遵循三層架構**: Controller → Service → Repository 的依賴方向
- **優先識別核心影響**: Model 和 Service 層的變更影響最大
- **測試覆蓋優先**: 確保所有必要測試都被識別
- **風險量化**: 使用明確的等級和條件

### 輸出準則

- **結構化報告**: 使用表格和清單，易於閱讀
- **可執行建議**: 提供具體的測試命令和執行順序
- **保守評估**: 寧可多列影響範圍，不遺漏潛在風險

### 限制

- **NEVER** 修改任何檔案
- **NEVER** 執行實際的測試（只列出應執行的測試）
- **ALWAYS** 根據實際程式碼分析，不做假設
- **ALWAYS** 標注不確定的依賴關係

## Integration with Development Workflow

此命令可以整合到以下工作流程：

### Bug 修復流程
```bash
/impact-analysis [bug 相關檔案]  # 先分析影響範圍
researcher → tester → coder → reviewer
```

### 重構流程
```bash
/impact-analysis [要重構的模組]  # 評估重構範圍
/speckit.specify → /speckit.plan → tester (回歸測試) → coder (重構)
```

### 修改共用組件流程
```bash
/impact-analysis resources/views/components/[組件名稱]
# 確認所有使用處後再修改
```

## Context

$ARGUMENTS
