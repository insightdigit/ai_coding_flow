---
description: 撰寫後端技術規格文檔，定義資料結構、Model、業務邏輯和驗證規則。
---

## User Input

```text
$ARGUMENTS
```

You **MUST** use the user input to understand what backend specification to create.

## Goal

撰寫後端技術規格文檔，在開發前定義：

1. **資料結構** - 資料表、欄位、關聯
2. **Model 設計** - fillable、關聯方法、Accessor
3. **業務邏輯** - Service/Repository 層設計
4. **驗證規則** - FormRequest 驗證
5. **錯誤處理** - 例外類別和錯誤訊息

## Operating Constraints

- **文檔優先**: 先完成規格文檔，不寫任何程式碼
- **遵循架構**: 依據 Controller → Service → Repository 三層架構
- **繁體中文**: 所有說明使用繁體中文

## Execution Steps

### 1. 分析需求

從 `$ARGUMENTS` 解析要設計的功能：

**支援格式：**
```bash
# 功能描述
/backend-spec 產品管理

# 指定模組
/backend-spec 訂單模組 - 建立訂單、取消訂單

# 指定類別
/backend-spec ProductService
```

### 2. 分析現有程式碼

使用 researcher agent 分析：
- 現有 Model 和關聯
- 相似功能的實作方式
- 共用 Service 和 Repository

### 3. 撰寫資料結構規格

**3.1 資料表設計**

```markdown
## 資料庫設計

### products 資料表

| 欄位 | 類型 | Nullable | 預設值 | 說明 |
|-----|------|----------|--------|------|
| id | bigint unsigned | ✗ | AUTO_INCREMENT | 主鍵 |
| name | varchar(255) | ✗ | - | 產品名稱 |
| price | decimal(10,2) | ✗ | 0.00 | 產品價格 |
| category_id | bigint unsigned | ✗ | - | 外鍵 → categories.id |
| description | text | ✓ | NULL | 產品描述 |
| is_active | boolean | ✗ | true | 是否啟用 |
| created_at | timestamp | ✓ | NULL | 建立時間 |
| updated_at | timestamp | ✓ | NULL | 更新時間 |

**索引：**
- PRIMARY KEY: id
- INDEX: category_id
- INDEX: is_active, name
```

**3.2 Migration 規格**

```markdown
### Migration

**檔案**: `database/migrations/xxxx_xx_xx_create_products_table.php`

**欄位定義:**
```php
$table->id();
$table->string('name');
$table->decimal('price', 10, 2)->default(0);
$table->foreignId('category_id')->constrained();
$table->text('description')->nullable();
$table->boolean('is_active')->default(true);
$table->timestamps();
```
```

### 4. 撰寫 Model 規格

```markdown
## Model 設計

### Product Model

**檔案**: `app/Models/Product.php`

**$fillable:**
```php
protected $fillable = [
    'name',
    'price',
    'category_id',
    'description',
    'is_active',
];
```

**$casts:**
```php
protected $casts = [
    'price' => 'decimal:2',
    'is_active' => 'boolean',
];
```

**關聯方法：**
| 方法 | 類型 | 關聯 Model | 說明 |
|-----|------|-----------|------|
| category() | belongsTo | Category | 產品分類 |
| orderItems() | hasMany | OrderItem | 訂單項目 |

**Scope：**
| 方法 | 說明 |
|-----|------|
| scopeActive($query) | 篩選啟用的產品 |
| scopeByCategory($query, $category_id) | 依分類篩選 |

**Accessor：**
| 屬性 | 說明 |
|-----|------|
| formatted_price | 格式化價格 (NT$ 100) |
```

### 5. 撰寫業務邏輯規格

**5.1 Service 層設計**

```markdown
## Service 設計

### ProductService

**檔案**: `app/Services/ProductService.php`

**依賴注入:**
```php
private ProductRepository $product_repository;
```

**方法：**

#### getProductList(array $filters = []): LengthAwarePaginator

取得產品列表

**參數:**
| 名稱 | 類型 | 說明 |
|-----|------|------|
| category_id | int|null | 分類篩選 |
| keyword | string|null | 關鍵字搜尋 |
| is_active | bool|null | 狀態篩選 |

**回傳:** 分頁後的產品資料

---

#### createProduct(array $data): Product

建立產品

**處理流程：**
1. 開啟 Transaction
2. 呼叫 Repository 建立產品
3. 處理關聯資料（如圖片）
4. Commit 並回傳

**例外：**
- ProductCreateException: 建立失敗

---

#### updateProduct(int $id, array $data): Product

更新產品

**處理流程：**
1. 開啟 Transaction
2. 取得現有產品
3. 更新產品資料
4. 更新關聯資料
5. Commit 並回傳

**例外：**
- ProductNotFoundException: 產品不存在
- ProductUpdateException: 更新失敗
```

**5.2 Repository 層設計**

```markdown
## Repository 設計

### ProductRepository

**檔案**: `app/Repositories/ProductRepository.php`

**依賴:**
```php
private Product $model;
```

**方法：**

| 方法 | 參數 | 回傳 | 說明 |
|-----|------|------|------|
| getFilteredProducts | array $filters, int $per_page = 15 | LengthAwarePaginator | 取得篩選後產品 |
| findById | int $id | Product | 依 ID 取得 |
| findByIdOrFail | int $id | Product | 依 ID 取得（失敗拋例外） |
| create | array $data | Product | 建立產品 |
| update | int $id, array $data | Product | 更新產品 |
| delete | int $id | bool | 刪除產品 |

**查詢邏輯：**

```php
// getFilteredProducts 實作概念
$query = $this->model->query()
    ->with(['category'])
    ->when($filters['category_id'], fn($q, $v) => $q->where('category_id', $v))
    ->when($filters['keyword'], fn($q, $v) => $q->where('name', 'like', "%{$v}%"))
    ->when(isset($filters['is_active']), fn($q) => $q->where('is_active', $filters['is_active']))
    ->orderBy('created_at', 'desc');
```
```

### 6. 撰寫驗證規則規格

```markdown
## 驗證規則

### StoreProductRequest

**檔案**: `app/Http/Requests/StoreProductRequest.php`

| 欄位 | 規則 | 錯誤訊息 |
|-----|------|---------|
| name | required, string, max:255 | 產品名稱為必填欄位 |
| price | required, numeric, min:0 | 價格必須大於或等於 0 |
| category_id | required, exists:categories,id | 請選擇有效的分類 |
| description | nullable, string, max:1000 | 描述不可超過 1000 字 |
| is_active | boolean | 狀態格式不正確 |

### UpdateProductRequest

| 欄位 | 規則 | 錯誤訊息 |
|-----|------|---------|
| name | sometimes, required, string, max:255 | 產品名稱格式不正確 |
| price | sometimes, required, numeric, min:0 | 價格必須大於或等於 0 |
| category_id | sometimes, required, exists:categories,id | 請選擇有效的分類 |
| description | nullable, string, max:1000 | 描述不可超過 1000 字 |
| is_active | sometimes, boolean | 狀態格式不正確 |
```

### 7. 撰寫 Controller 規格

```markdown
## Controller 設計

### ProductController

**檔案**: `app/Http/Controllers/ProductController.php`

**依賴注入:**
```php
private ProductService $product_service;
```

**方法：**

| 方法 | HTTP | 路由 | FormRequest | View |
|-----|------|------|-------------|------|
| index | GET | /products | - | products.index |
| create | GET | /products/create | - | products.create |
| store | POST | /products | StoreProductRequest | redirect |
| show | GET | /products/{id} | - | products.show |
| edit | GET | /products/{id}/edit | - | products.edit |
| update | PUT | /products/{id} | UpdateProductRequest | redirect |
| destroy | DELETE | /products/{id} | - | redirect |

**傳遞給 View 的資料：**

| 方法 | 變數 | 類型 | 說明 |
|-----|------|------|------|
| index | $products | Paginator | 產品列表 |
| index | $categories | Collection | 分類選項 |
| create | $categories | Collection | 分類選項 |
| edit | $product | Product | 編輯的產品 |
| edit | $categories | Collection | 分類選項 |
```

### 8. 撰寫錯誤處理規格

```markdown
## 錯誤處理

### 自訂例外類別

| 類別 | 繼承 | HTTP 狀態 | 使用情境 |
|-----|------|----------|---------|
| ProductNotFoundException | Exception | 404 | 產品不存在 |
| ProductCreateException | Exception | 500 | 建立失敗 |
| ProductUpdateException | Exception | 500 | 更新失敗 |

### 錯誤訊息

| 情境 | Session Key | 訊息 |
|-----|-------------|------|
| 建立成功 | success | 產品建立成功 |
| 更新成功 | success | 產品更新成功 |
| 刪除成功 | success | 產品已刪除 |
| 找不到 | error | 找不到指定的產品 |
| 建立失敗 | error | 產品建立失敗，請稍後再試 |
```

---

## Output Format

```markdown
# 後端技術規格

**模組**: [模組名稱]
**版本**: 1.0
**日期**: [日期]

---

## 1. 資料庫設計

### 1.1 資料表
[資料表定義]

### 1.2 Migration
[Migration 規格]

---

## 2. Model 設計

### 2.1 屬性
[$fillable, $casts]

### 2.2 關聯
[關聯方法]

### 2.3 Scope & Accessor
[Scope 和 Accessor]

---

## 3. Repository 設計

[Repository 方法定義]

---

## 4. Service 設計

[Service 方法和流程]

---

## 5. Controller 設計

[Controller 方法和 View 對應]

---

## 6. 驗證規則

[FormRequest 規則]

---

## 7. 錯誤處理

[例外類別和訊息]

---

## 8. 測試計畫

| 測試類型 | 測試重點 |
|---------|---------|
| Unit | Service、Repository |
| Feature | Controller 流程 |
```

---

## Best Practices

- **完整性**: 涵蓋 Model 到 Controller 的完整流程
- **一致性**: 遵循專案既有的命名和設計模式
- **可測試性**: 規格能直接轉換為測試案例

## Operating Principles

- **NEVER** 在規格階段寫實際程式碼
- **ALWAYS** 參考現有程式碼的設計模式
- **ALWAYS** 使用繁體中文撰寫說明

## Context

$ARGUMENTS
