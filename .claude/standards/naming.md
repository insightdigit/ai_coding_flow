# 命名規範大全

> **核心原則:** 程式碼使用英文，註解使用繁體中文，嚴格遵守各層級命名規範

---

## 1. PHP 元素命名

| 元素 | 命名法 | 範例 | 說明 |
|-----|--------|------|------|
| **類別 (Classes)** | PascalCase | `ProductController`, `UserService` | 大駝峰，首字母大寫 |
| **介面 (Interfaces)** | PascalCase | `RepositoryInterface` | 大駝峰，建議加 Interface 後綴 |
| **Trait** | PascalCase | `HasTimestamps` | 大駝峰 |
| **方法 (Methods)** | camelCase | `getProductList()`, `createUser()` | 小駝峰，首字母小寫 |
| **變數 (Variables)** | snake_case | `$user_name`, `$product_list` | 蛇形，全小寫加底線 |
| **常數 (Constants)** | SCREAMING_SNAKE_CASE | `MAX_RETRY_COUNT`, `API_KEY` | 全大寫蛇形 |
| **屬性 (Properties)** | snake_case | `$is_active`, `$created_at` | 蛇形，與變數相同 |

---

## 2. 專案目錄與檔案命名

| 目錄/檔案類型 | 資料夾命名 | 檔案命名 | 範例 |
|------------|-----------|---------|------|
| `app/Http/Controllers` | PascalCase | PascalCase.php | `ProductController.php` |
| `app/Models` | PascalCase | PascalCase.php | `Product.php`, `User.php` |
| `app/Services` | PascalCase | PascalCase.php | `ProductService.php` |
| `app/Repositories` | PascalCase | PascalCase.php | `ProductRepository.php` |
| `app/Jobs` | PascalCase | PascalCase.php | `ProcessOrder.php` |
| `app/Notifications` | PascalCase | PascalCase.php | `OrderCreated.php` |
| `app/Providers` | PascalCase | PascalCase.php | `AppServiceProvider.php` |
| `config/` | - | kebab-case.php | `database.php`, `app-settings.php` |
| `database/migrations` | - | timestamp_snake_case.php | `2024_01_01_000000_create_products_table.php` |
| `database/factories` | PascalCase | PascalCase.php | `ProductFactory.php` |
| `database/seeders` | PascalCase | PascalCase.php | `ProductSeeder.php` |
| `tests/Feature` | PascalCase | PascalCase.php | `ProductTest.php` |
| `tests/Unit` | PascalCase | PascalCase.php | `ProductServiceTest.php` |

---

## 3. 前端檔案命名

| 類型 | 命名法 | 範例 |
|-----|--------|------|
| **Blade 模板** | kebab-case | `product-list.blade.php`, `user-profile.blade.php` |
| **Blade Components** | kebab-case | `data-table.blade.php`, `search-form.blade.php` |
| **CSS/SCSS 檔案** | kebab-case | `main-style.css`, `product-card.css` |
| **JavaScript 檔案** | kebab-case | `app.js`, `form-handler.js` |
| **資料夾 (views)** | snake_case | `product_management/`, `user_settings/` |
| **資料夾 (public)** | snake_case | `images/`, `css/`, `js/` |

---

## 4. 路由命名

```php
// ✅ 正確：使用 kebab-case
Route::get('/product-management/list', [ProductController::class, 'index']);
Route::post('/product-management/create', [ProductController::class, 'store']);
Route::delete('/product-management/delete/{product_id}', [ProductController::class, 'destroy']);

// ✅ 路由參數使用 snake_case
Route::get('/products/{product_id}/variants/{variant_id}', ...);

// ❌ 錯誤：不要使用 camelCase 或 PascalCase
Route::get('/productManagement/list', ...);  // 錯誤
Route::get('/ProductManagement/List', ...);  // 錯誤
```

---

## 5. 資料庫命名

### 資料表命名
```php
// ✅ 正確：使用 snake_case，複數形式
'products', 'product_variants', 'user_profiles'

// ❌ 錯誤
'Product', 'productVariants', 'UserProfile'
```

### 資料欄位命名
```php
// ✅ 正確：使用 snake_case
'product_name', 'created_at', 'is_active', 'user_id'

// ✅ 特定欄位規則
'is_active'      // 布林值：is_* 前綴
'birth_date'     // 日期：*_date 後綴
'start_time'     // 時間：*_time 後綴
'created_at'     // 日期時間：*_at 後綴
'deleted_at'     // 軟刪除：*_at 後綴

// ❌ 錯誤
'productName', 'CreatedAt', 'active' (應為 is_active)
```

### 關聯命名
```php
// ✅ 外鍵命名：{關聯表單數}_id
'user_id', 'product_id', 'category_id'

// ✅ 中間表命名：按字母順序排列的兩個表名（單數）
'category_product', 'order_product'

// ❌ 錯誤
'userId', 'product_category' (順序錯誤)
```

---

## 6. 命名速查表

```
類別/介面/Trait     → PascalCase        (ProductService)
方法/函式           → camelCase         (getProductList)
變數/屬性           → snake_case        ($product_list)
常數               → SCREAMING_SNAKE_CASE (MAX_COUNT)
路由路徑           → kebab-case        (/product-management)
路由參數           → snake_case        ({product_id})
資料表             → snake_case (複數)  (products)
資料欄位           → snake_case        (product_name)
Blade 檔案         → kebab-case.blade.php (product-list.blade.php)
Config 檔案        → kebab-case.php    (app-settings.php)
PHP 類別檔案       → PascalCase.php    (ProductController.php)
```
