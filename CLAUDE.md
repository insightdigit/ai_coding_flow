# Laravel 開發規範

> Laravel 12 + Blade + Tailwind CSS 全端開發指南
>
> **版本:** 1.0 | **最後更新:** 2025-11-09

---

## 📑 文件目錄

1. [專案概覽](#-專案概覽)
2. [命名規範大全](#-命名規範大全)
3. [程式碼風格規範](#-程式碼風格規範)
4. [後端開發規範](#-後端開發規範)
5. [前端開發規範](#-前端開發規範)
6. [開發工作流程](#-開發工作流程)
7. [快速參考](#-快速參考)

---

## 🎯 專案概覽

### 基本資訊
* **開發框架:** Laravel 12 全端開發專案
* **開發語言:** 繁體中文（文檔與註解）、英文（程式碼）
* **AI 互動模式:** 您(AI)必須扮演資深 Laravel 全端開發者，提供 100% 符合本規範的可運作程式碼

### 技術堆疊

| 類別 | 技術 | 版本 |
|-----|------|------|
| **後端框架** | Laravel | 12 |
| **PHP 版本** | PHP | 8.4 |
| **資料庫 (開發)** | MySQL | 8 |
| **資料庫 (正式)** | MariaDB | 11.4.5 |
| **前端模板** | Blade | - |
| **前端樣式** | Tailwind CSS | - |

### 專案結構

```
laravel-project/
├── app/                    # Laravel 核心應用
│   ├── Http/
│   │   ├── Controllers/   # 控制器層
│   │   ├── Middleware/    # 中介層
│   │   └── Requests/      # 表單驗證請求
│   ├── Models/            # Eloquent Models
│   ├── Services/          # 商業邏輯層
│   ├── Repositories/      # 資料存取層
│   └── Providers/         # 服務提供者
├── resources/
│   ├── views/             # Blade 模板
│   ├── css/               # 樣式檔案
│   └── js/                # JavaScript 檔案
├── routes/                # 路由定義
│   ├── web.php           # Web 路由
│   └── api.php           # API 路由（若需要）
├── database/
│   ├── migrations/        # 資料庫遷移
│   ├── seeders/          # 資料填充
│   └── factories/        # Model 工廠
├── tests/                 # 測試檔案
│   ├── Feature/          # 功能測試
│   └── Unit/             # 單元測試
├── config/                # 配置檔案
├── public/                # 公開資源
├── specs/                 # Spec-Kit 規格文件
├── .claude/              # Claude Code 配置
│   ├── agents/           # AI Agents 定義
│   └── commands/         # Slash Commands
└── .specify/             # Spec-Kit 系統
```

---

## 📝 命名規範大全

> **核心原則:** 程式碼使用英文，註解使用繁體中文，嚴格遵守各層級命名規範

### 1. PHP 元素命名

| 元素 | 命名法 | 範例 | 說明 |
|-----|--------|------|------|
| **類別 (Classes)** | PascalCase | `ProductController`, `UserService` | 大駝峰，首字母大寫 |
| **介面 (Interfaces)** | PascalCase | `RepositoryInterface` | 大駝峰，建議加 Interface 後綴 |
| **Trait** | PascalCase | `HasTimestamps` | 大駝峰 |
| **方法 (Methods)** | camelCase | `getProductList()`, `createUser()` | 小駝峰，首字母小寫 |
| **變數 (Variables)** | snake_case | `$user_name`, `$product_list` | 蛇形，全小寫加底線 |
| **常數 (Constants)** | SCREAMING_SNAKE_CASE | `MAX_RETRY_COUNT`, `API_KEY` | 全大寫蛇形 |
| **屬性 (Properties)** | snake_case | `$is_active`, `$created_at` | 蛇形，與變數相同 |

### 2. 專案目錄與檔案命名

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

### 3. 前端檔案命名

| 類型 | 命名法 | 範例 |
|-----|--------|------|
| **Blade 模板** | kebab-case | `product-list.blade.php`, `user-profile.blade.php` |
| **Blade Components** | kebab-case | `data-table.blade.php`, `search-form.blade.php` |
| **CSS/SCSS 檔案** | kebab-case | `main-style.css`, `product-card.css` |
| **JavaScript 檔案** | kebab-case | `app.js`, `form-handler.js` |
| **資料夾 (views)** | snake_case | `product_management/`, `user_settings/` |
| **資料夾 (public)** | snake_case | `images/`, `css/`, `js/` |

### 4. 路由命名

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

### 5. 資料庫命名

#### 資料表命名
```php
// ✅ 正確：使用 snake_case，複數形式
'products', 'product_variants', 'user_profiles'

// ❌ 錯誤
'Product', 'productVariants', 'UserProfile'
```

#### 資料欄位命名
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

#### 關聯命名
```php
// ✅ 外鍵命名：{關聯表單數}_id
'user_id', 'product_id', 'category_id'

// ✅ 中間表命名：按字母順序排列的兩個表名（單數）
'category_product', 'order_product'

// ❌ 錯誤
'userId', 'product_category' (順序錯誤)
```

### 6. 命名速查表

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

---

## 🎨 程式碼風格規範

### 1. 縮排與空格

```php
// ✅ 正確：使用 4 個空格縮排
class ProductService
{
    public function getProductList()
    {
        if ($condition) {
            return $result;
        }
    }
}

// ❌ 錯誤：使用 Tab 或不一致的空格
class ProductService
{
	public function getProductList()  // 使用 Tab（錯誤）
	{
	  return $result;  // 2 個空格（錯誤）
	}
}
```

**規則:**
- ✅ 必須使用 **4 個空格**縮排
- ❌ 禁止使用 Tab 鍵
- ✅ 每個縮排層級增加 4 個空格

### 2. 條件語句

```php
// ✅ 正確：條件前後有空格，使用 elseif，總是用花括號
if ($user->isActive()) {
    return true;
} elseif ($user->isPending()) {
    return false;
} else {
    return null;
}

// ✅ 即使只有一行也要用花括號
if ($condition) {
    doSomething();
}

// ❌ 錯誤：使用 else if、沒有花括號、沒有空格
if($condition) doSomething();  // 錯誤
else if ($other) { }           // 錯誤，應為 elseif
```

**規則:**
- ✅ `if` 條件前後必須有空格：`if ($condition)`
- ✅ 必須使用 `elseif`，禁止 `else if`
- ✅ 總是使用花括號 `{}`，即使只有一行
- ✅ 左花括號在同一行，右花括號獨立一行

### 3. 註解規範

```php
// ✅ 正確：每行程式碼都附帶繁體中文註解
/**
 * 取得產品列表
 *
 * @param array $filters 篩選條件
 * @param int $per_page 每頁筆數
 * @return \Illuminate\Pagination\LengthAwarePaginator
 */
public function getProductList(array $filters = [], int $per_page = 15)
{
    // 從儲存庫取得產品列表
    $products = $this->product_repository->getFilteredProducts($filters, $per_page);

    // 檢查是否有資料
    if ($products->isEmpty()) {
        // 記錄查詢為空的情況
        Log::info('產品列表查詢結果為空', ['filters' => $filters]);
    }

    // 回傳分頁結果
    return $products;
}

// ❌ 錯誤：沒有註解或使用英文註解
public function getProductList(array $filters = [], int $per_page = 15)
{
    $products = $this->product_repository->getFilteredProducts($filters, $per_page);  // Get products
    return $products;
}
```

**規則:**
- ✅ **所有程式碼必須附帶繁體中文註解**
- ✅ 類別、方法使用 PHPDoc 格式
- ✅ 複雜邏輯必須詳細註解說明
- ✅ 參數、返回值必須說明類型和用途
- ❌ 禁止使用英文註解（除非是專有名詞）

### 4. 類別與方法結構

```php
<?php

namespace App\Services;

use App\Repositories\ProductRepository;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\DB;

/**
 * 產品服務類別
 *
 * 負責處理產品相關的商業邏輯
 */
class ProductService
{
    /**
     * 產品資料儲存庫
     *
     * @var ProductRepository
     */
    private ProductRepository $product_repository;

    /**
     * 建構函式 - 注入依賴
     *
     * @param ProductRepository $product_repository
     */
    public function __construct(ProductRepository $product_repository)
    {
        $this->product_repository = $product_repository;
    }

    /**
     * 取得產品列表
     *
     * @param array $filters 篩選條件
     * @param int $per_page 每頁筆數
     * @return \Illuminate\Pagination\LengthAwarePaginator
     */
    public function getProductList(array $filters = [], int $per_page = 15)
    {
        // 實作內容
    }
}
```

**結構順序:**
1. Namespace 宣告
2. Use 引入（按類型分組：Laravel、第三方、專案內）
3. 類別 PHPDoc
4. 類別宣告
5. 常數定義
6. 屬性定義（private → protected → public）
7. 建構函式
8. Public 方法
9. Protected 方法
10. Private 方法

---

## 🔒 後端開發規範

### 1. 安全性規範（強制）

#### 1.1 SQL 注入防護

```php
// ✅ 正確：使用 Eloquent ORM
$products = Product::where('category_id', $category_id)
    ->where('is_active', true)
    ->get();

// ✅ 正確：使用查詢建構器的參數綁定
$products = DB::table('products')
    ->where('category_id', '=', $category_id)
    ->get();

// ✅ 正確：使用 whereRaw 時必須使用參數綁定
$products = Product::whereRaw('price > ? AND stock > ?', [$min_price, $min_stock])->get();

// ❌ 禁止：直接拼接 SQL
$products = DB::select("SELECT * FROM products WHERE category_id = $category_id");  // 危險！

// ❌ 禁止：使用未綁定參數的原生 SQL
$query = "SELECT * FROM products WHERE name = '$name'";  // 危險！
```

#### 1.2 XSS 防護

```blade
{{-- ✅ 正確：使用 {{ }} 自動轉義 --}}
<h1>{{ $product->name }}</h1>
<p>{{ $user->description }}</p>

{{-- ✅ 當確實需要 HTML 時，先清理內容 --}}
<div>{!! clean($product->html_content) !!}</div>

{{-- ❌ 禁止：直接輸出未轉義的內容 --}}
<h1>{!! $product->name !!}</h1>  {{-- 危險！ --}}
```

#### 1.3 輸入驗證（強制）

```php
// ✅ 正確：使用 FormRequest
class StoreProductRequest extends FormRequest
{
    /**
     * 驗證規則
     *
     * @return array
     */
    public function rules(): array
    {
        return [
            'name' => 'required|string|max:255',
            'category_id' => 'required|exists:categories,id',
            'price' => 'required|numeric|min:0',
            'is_active' => 'required|boolean',
        ];
    }

    /**
     * 驗證錯誤訊息
     *
     * @return array
     */
    public function messages(): array
    {
        return [
            'name.required' => '產品名稱為必填',
            'category_id.exists' => '所選分類不存在',
            'price.min' => '價格不能小於 0',
        ];
    }
}

// ✅ Controller 中使用
public function store(StoreProductRequest $request)
{
    // 資料已經驗證過，可以安全使用
    $validated_data = $request->validated();

    // 繼續處理
}

// ❌ 禁止：不驗證直接使用
public function store(Request $request)
{
    $product = Product::create($request->all());  // 危險！
}
```

#### 1.4 Mass Assignment 防護

```php
// ✅ 正確：在 Model 中使用 $fillable
class Product extends Model
{
    /**
     * 可批量賦值的屬性
     *
     * @var array
     */
    protected $fillable = [
        'name',
        'category_id',
        'price',
        'description',
        'is_active',
    ];

    // 或使用 $guarded 保護特定欄位
    // protected $guarded = ['id', 'created_at', 'updated_at'];
}

// ✅ 正確：只傳入允許的欄位
$product = Product::create($request->only(['name', 'category_id', 'price']));

// ❌ 禁止：沒有定義 $fillable 或 $guarded
class Product extends Model
{
    // 沒有 $fillable 或 $guarded 定義（危險！）
}
```

### 2. 錯誤處理（強制）

```php
// ✅ 正確：完整的錯誤處理
public function createProduct(array $data)
{
    try {
        // 開始資料庫事務
        DB::beginTransaction();

        // 建立產品
        $product = $this->product_repository->create($data);

        // 建立產品變體
        $product->variants()->create([
            'sku' => $data['sku'],
            'stock' => $data['stock'],
        ]);

        // 提交事務
        DB::commit();

        // 記錄成功操作
        Log::info('產品建立成功', [
            'product_id' => $product->id,
            'user_id' => auth()->id(),
        ]);

        return $product;

    } catch (\Illuminate\Database\QueryException $exception) {
        // 回滾事務
        DB::rollBack();

        // 記錄資料庫錯誤
        Log::error('資料庫錯誤：產品建立失敗', [
            'error' => $exception->getMessage(),
            'data' => $data,
            'user_id' => auth()->id(),
        ]);

        throw new \Exception('產品建立失敗，請稍後再試');

    } catch (\Exception $exception) {
        // 回滾事務
        DB::rollBack();

        // 記錄一般錯誤
        Log::error('產品建立失敗', [
            'error' => $exception->getMessage(),
            'trace' => $exception->getTraceAsString(),
        ]);

        throw new \Exception('系統錯誤，請聯繫管理員');
    }
}

// ❌ 禁止：沒有錯誤處理
public function createProduct(array $data)
{
    $product = Product::create($data);  // 如果失敗會怎樣？
    return $product;
}
```

**錯誤處理規則:**
- ✅ 所有資料庫操作必須用 try-catch 包覆
- ✅ 捕獲具體的 Exception（如 QueryException）
- ✅ 記錄詳細的錯誤訊息和上下文
- ✅ 返回對使用者友善的錯誤訊息
- ✅ 使用事務確保資料一致性

### 3. 架構模式（強制）

#### 3.1 Controller → Service → Repository 分層

```php
// ✅ Controller 層：只處理 HTTP 請求和回應
class ProductController extends Controller
{
    /**
     * 產品服務
     *
     * @var ProductService
     */
    private ProductService $product_service;

    /**
     * 建構函式
     *
     * @param ProductService $product_service
     */
    public function __construct(ProductService $product_service)
    {
        $this->product_service = $product_service;
    }

    /**
     * 顯示產品列表
     *
     * @param Request $request
     * @return \Illuminate\View\View
     */
    public function index(Request $request)
    {
        // 只負責接收請求和返回回應
        $filters = $request->only(['category_id', 'keyword', 'is_active']);
        $products = $this->product_service->getProductList($filters);

        return view('products.index', compact('products'));
    }

    /**
     * 建立產品
     *
     * @param StoreProductRequest $request
     * @return \Illuminate\Http\RedirectResponse
     */
    public function store(StoreProductRequest $request)
    {
        try {
            // 委派給 Service 處理商業邏輯
            $product = $this->product_service->createProduct($request->validated());

            return redirect()
                ->route('products.show', $product->id)
                ->with('success', '產品建立成功');

        } catch (\Exception $exception) {
            return redirect()
                ->back()
                ->with('error', $exception->getMessage())
                ->withInput();
        }
    }
}

// ✅ Service 層：處理商業邏輯
class ProductService
{
    private ProductRepository $product_repository;

    public function __construct(ProductRepository $product_repository)
    {
        $this->product_repository = $product_repository;
    }

    /**
     * 取得產品列表
     *
     * @param array $filters
     * @return \Illuminate\Pagination\LengthAwarePaginator
     */
    public function getProductList(array $filters = [])
    {
        // 商業邏輯處理
        $products = $this->product_repository->getFilteredProducts($filters);

        // 可以在這裡加入額外的商業邏輯
        // 例如：計算統計資料、檢查權限等

        return $products;
    }

    /**
     * 建立產品
     *
     * @param array $data
     * @return Product
     */
    public function createProduct(array $data)
    {
        // 複雜的商業邏輯
        // 資料驗證、格式化、關聯處理等

        return $this->product_repository->create($data);
    }
}

// ✅ Repository 層：只處理資料存取
class ProductRepository
{
    /**
     * 取得篩選後的產品列表
     *
     * @param array $filters
     * @param int $per_page
     * @return \Illuminate\Pagination\LengthAwarePaginator
     */
    public function getFilteredProducts(array $filters = [], int $per_page = 15)
    {
        // 只負責資料庫查詢
        $query = Product::query();

        if (!empty($filters['category_id'])) {
            $query->where('category_id', $filters['category_id']);
        }

        if (!empty($filters['keyword'])) {
            $query->where('name', 'like', "%{$filters['keyword']}%");
        }

        if (isset($filters['is_active'])) {
            $query->where('is_active', $filters['is_active']);
        }

        return $query->paginate($per_page);
    }

    /**
     * 建立產品
     *
     * @param array $data
     * @return Product
     */
    public function create(array $data)
    {
        return Product::create($data);
    }
}
```

**架構規則:**
- ✅ Controller：只處理 HTTP 請求和回應，不包含商業邏輯
- ✅ Service：包含所有商業邏輯、驗證、計算
- ✅ Repository：只處理資料庫操作，不包含商業邏輯
- ✅ 使用依賴注入傳遞依賴
- ❌ 禁止在 Controller 中直接操作 Model
- ❌ 禁止在 Repository 中包含商業邏輯

### 4. 資料庫操作規範

```php
// ✅ 正確：使用 Eloquent 關聯載入，避免 N+1 問題
$products = Product::with(['category', 'user', 'variants'])
    ->where('is_active', true)
    ->get();

// ✅ 正確：使用 select 只取需要的欄位
$products = Product::select('id', 'name', 'price')
    ->where('category_id', $category_id)
    ->get();

// ✅ 正確：使用分頁
$products = Product::paginate(15);

// ✅ 正確：使用 chunk 處理大量資料
Product::chunk(100, function ($products) {
    foreach ($products as $product) {
        // 處理每個產品
    }
});

// ❌ 禁止：N+1 查詢問題
$products = Product::all();  // 查詢 1 次
foreach ($products as $product) {
    $category = $product->category;  // 每個 product 都查詢 1 次（N+1 問題！）
}

// ❌ 禁止：取出所有資料到記憶體
$all_products = Product::all();  // 如果有 10 萬筆資料會怎樣？
```

### 5. Laravel 最佳實踐檢查清單

#### 5.1 Eloquent 使用

```php
// ✅ 使用 Query Scopes 組織查詢邏輯
class Product extends Model
{
    public function scopeActive($query)
    {
        return $query->where('is_active', true);
    }

    public function scopeInCategory($query, $category_id)
    {
        return $query->where('category_id', $category_id);
    }
}

// 使用
$products = Product::active()->inCategory(1)->get();
```

#### 5.2 快取策略

```php
// ✅ 快取查詢結果
use Illuminate\Support\Facades\Cache;

$products = Cache::remember('products.active', 3600, function () {
    return Product::where('is_active', true)->get();
});

// ✅ Model 事件中清除快取
protected static function booted()
{
    static::saved(function () {
        Cache::forget('products.active');
    });
}
```

#### 5.3 效能優化檢查

**查詢優化:**
- ✅ 使用 `select()` 只取需要的欄位
- ✅ 使用 `with()` 避免 N+1 問題
- ✅ 使用 `chunk()` 或 `lazy()` 處理大量資料
- ✅ 為常用查詢條件建立索引
- ❌ 避免在迴圈中執行查詢

**快取使用:**
- ✅ 快取不常變動的資料
- ✅ 使用 Cache Tags 組織快取
- ✅ Model 更新時清除相關快取
- ❌ 不要快取經常變動的資料

**資料庫設計:**
- ✅ 外鍵加上索引
- ✅ 複合索引遵循最左匹配原則
- ✅ 使用適當的欄位類型和長度
- ❌ 避免過度正規化影響查詢效能

---

## 🎭 前端開發規範

### 1. Blade 模板規範

#### 1.1 模板結構

```blade
{{-- ✅ 正確：清晰的模板結構 --}}
@extends('layouts.app')

@section('title', '產品列表')

@section('content')
<div class="container mx-auto px-4 py-8">
    {{-- 頁面標題 --}}
    <h1 class="text-3xl font-bold mb-6">產品管理</h1>

    {{-- 搜尋表單 --}}
    @include('products.partials.search-form')

    {{-- 產品列表 --}}
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        @forelse($products as $product)
            {{-- 產品卡片元件 --}}
            <x-product-card :product="$product" />
        @empty
            {{-- 空狀態 --}}
            <div class="col-span-full text-center py-12">
                <p class="text-gray-500">尚無產品資料</p>
            </div>
        @endforelse
    </div>

    {{-- 分頁 --}}
    <div class="mt-8">
        {{ $products->links() }}
    </div>
</div>
@endsection

@push('scripts')
<script>
    // 頁面專用的 JavaScript
</script>
@endpush
```

#### 1.2 Blade Components

```blade
{{-- resources/views/components/product-card.blade.php --}}
@props(['product'])

<div class="bg-white rounded-lg shadow-md p-6 hover:shadow-lg transition-shadow">
    {{-- 產品圖片 --}}
    <img src="{{ $product->image_url }}"
         alt="{{ $product->name }}"
         class="w-full h-48 object-cover rounded-md mb-4">

    {{-- 產品資訊 --}}
    <h3 class="text-xl font-semibold mb-2">{{ $product->name }}</h3>
    <p class="text-gray-600 mb-4">{{ Str::limit($product->description, 100) }}</p>

    {{-- 操作按鈕 --}}
    <div class="flex justify-between items-center">
        <span class="text-sm text-gray-500">{{ $product->category->name }}</span>
        <a href="{{ route('products.show', $product->id) }}"
           class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
            查看詳情
        </a>
    </div>
</div>
```

**使用方式:**
```blade
{{-- 使用 Component --}}
<x-product-card :product="$product" />

{{-- 傳遞多個屬性 --}}
<x-data-table
    :columns="$columns"
    :data="$data"
    :sortable="true"
    class="mt-4"
/>
```

### 2. Tailwind CSS 規範

```blade
{{-- ✅ 正確：使用 Tailwind 工具類別 --}}
<div class="container mx-auto px-4 py-8">
    <h1 class="text-3xl font-bold text-gray-800 mb-6">標題</h1>
    <p class="text-base text-gray-600 leading-relaxed">內容</p>

    <button class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600
                   focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50
                   transition-colors duration-200">
        按鈕
    </button>
</div>

{{-- ✅ 響應式設計 --}}
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    {{-- 手機: 1 列, 平板: 2 列, 桌機: 3 列 --}}
</div>

{{-- ❌ 禁止：內嵌樣式 --}}
<div style="padding: 20px; margin: 10px;">  {{-- 不要這樣做 --}}
    內容
</div>

{{-- ❌ 禁止：自訂 CSS（除非必要） --}}
<div class="custom-style">  {{-- 盡量避免 --}}
    內容
</div>
```

**Tailwind 最佳實踐:**
- ✅ 使用 Tailwind 工具類別
- ✅ 使用響應式前綴 (sm:, md:, lg:, xl:)
- ✅ 使用 hover:, focus:, active: 等狀態前綴
- ✅ 複雜的樣式抽取為 Component
- ❌ 避免內嵌 style 屬性
- ❌ 避免自訂 CSS 類別

### 3. JavaScript 整合

```blade
{{-- ✅ 使用 Alpine.js（輕量級互動） --}}
<div x-data="{ open: false }">
    {{-- 按鈕 --}}
    <button @click="open = !open" class="px-4 py-2 bg-blue-500 text-white rounded">
        切換
    </button>

    {{-- 內容 --}}
    <div x-show="open" x-transition class="mt-4 p-4 bg-gray-100 rounded">
        可切換的內容
    </div>
</div>

{{-- ✅ 使用 @push 載入頁面專用的 JS --}}
@push('scripts')
<script>
    // 頁面專用的 JavaScript
    document.addEventListener('DOMContentLoaded', function() {
        // 初始化
    });
</script>
@endpush
```

### 4. 表單處理

```blade
{{-- ✅ 正確：完整的表單處理 --}}
<form method="POST" action="{{ route('products.store') }}" class="space-y-6">
    @csrf

    {{-- 產品名稱 --}}
    <div>
        <label for="name" class="block text-sm font-medium text-gray-700 mb-2">
            產品名稱 <span class="text-red-500">*</span>
        </label>
        <input type="text"
               id="name"
               name="name"
               value="{{ old('name') }}"
               required
               class="w-full px-4 py-2 border border-gray-300 rounded-md
                      focus:ring-2 focus:ring-blue-500 focus:border-blue-500
                      @error('name') border-red-500 @enderror">

        @error('name')
            <p class="mt-1 text-sm text-red-500">{{ $message }}</p>
        @enderror
    </div>

    {{-- 分類選擇 --}}
    <div>
        <label for="category_id" class="block text-sm font-medium text-gray-700 mb-2">
            分類 <span class="text-red-500">*</span>
        </label>
        <select id="category_id"
                name="category_id"
                required
                class="w-full px-4 py-2 border border-gray-300 rounded-md">
            <option value="">請選擇分類</option>
            @foreach($categories as $category)
                <option value="{{ $category->id }}"
                        {{ old('category_id') == $category->id ? 'selected' : '' }}>
                    {{ $category->name }}
                </option>
            @endforeach
        </select>

        @error('category_id')
            <p class="mt-1 text-sm text-red-500">{{ $message }}</p>
        @enderror
    </div>

    {{-- 送出按鈕 --}}
    <div class="flex justify-end space-x-4">
        <a href="{{ route('products.index') }}"
           class="px-6 py-2 border border-gray-300 rounded-md hover:bg-gray-50">
            取消
        </a>
        <button type="submit"
                class="px-6 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">
            建立產品
        </button>
    </div>
</form>

{{-- 顯示成功訊息 --}}
@if(session('success'))
    <div class="mb-4 p-4 bg-green-100 border border-green-400 text-green-700 rounded">
        {{ session('success') }}
    </div>
@endif

{{-- 顯示錯誤訊息 --}}
@if(session('error'))
    <div class="mb-4 p-4 bg-red-100 border border-red-400 text-red-700 rounded">
        {{ session('error') }}
    </div>
@endif
```

---

## 🔄 開發工作流程

> **核心理念:** 文件驅動開發 (Spec-Kit) + 專業化協作 (Agents) + 自動化生成 (MCP)

### 工作流程三大支柱

| 工具 | 用途 | 適用場景 |
|-----|------|---------|
| **Spec-Kit** | 規格化文件與任務管理 | 所有新功能開發、重構、架構設計 |
| **Agents** | 專業化開發代理 | 程式碼實作、測試、審查 |
| **MCP 工具** | 程式碼生成與規範檢查 | 所有程式碼生成（自動搭配使用） |

### 1. Spec-Kit 指令完整列表

| 指令 | 用途 | 生成文件 | 使用時機 |
|-----|------|---------|---------|
| `/speckit.constitution` | 建立/更新專案憲章 | `constitution.md` | 專案初始化或更新原則 |
| `/speckit.specify [描述]` | 建立功能規格 | `spec.md` | 開始新功能開發 |
| `/speckit.clarify` | 澄清模糊需求 | 更新 `spec.md` | 規格不明確時 |
| `/speckit.plan` | 執行技術規劃 | `plan.md`, `research.md`, `data-model.md`, `contracts/`, `quickstart.md` | 規格確定後的技術設計 |
| `/speckit.tasks` | 生成任務清單 | `tasks.md` | 設計完成後準備實作 |
| `/speckit.analyze` | 分析文件一致性 | 分析報告 | 任務生成後驗證完整性 |
| `/speckit.implement` | 執行實作計劃 | 實際程式碼 | 開始編碼 |
| `/speckit.checklist` | 生成檢查清單 | 檢查清單 | 驗證功能完整性 |

### 2. Agents 完整列表

**如何調用 Agents:**
```bash
Task agent: [agent名稱]
Prompt: "[具體任務描述]"
```

**可用的 Agents:**

| 分類 | Agent | 用途 |
|-----|-------|------|
| **核心開發** | `researcher` | 分析程式碼結構、研究依賴關係 |
| | `coder` | 實作程式碼、遵循規範 |
| | `tester` | 編寫測試、驗證品質（支援 TDD） |
| | `reviewer` | 程式碼審查、檢查規範 |
| **模板生成** | `base-template-generator` | 建立基礎模板和樣板程式碼 |
| **UI/UX** | `ui-designer` | UI 設計、設計系統 |
| | `component-architect` | 元件架構設計 |
| **架構** | `system-architect` | 系統架構設計、技術選型 |
| **協調** | `pr-manager` | Pull Request 管理 |

### 3. MCP 工具

| 工具 | 用途 | 使用時機 |
|-----|------|---------|
| `context7` | 確保最新 Laravel/PHP 語法 | **所有程式碼生成**（Controller/Service/Repository/Model） |
| `tailwindcss-mcp-server` | Tailwind CSS 規範與元件生成 | **僅前端程式碼**（Blade/HTML/CSS） |

**MCP 使用規則：**
- ✅ 寫 PHP 程式碼時：**必須**使用 `context7`
- ✅ 寫 Blade 模板時：**必須同時**使用 `context7` + `tailwindcss-mcp-server`
- ✅ 寫純 CSS/Tailwind 時：**必須**使用 `tailwindcss-mcp-server`
- ❌ 只寫文件、規格時：不需要使用 MCP 工具

### 4. 完整開發流程

```
📋 階段 0: 專案初始化
   └─ /speckit.constitution

📝 階段 1: 規格定義
   └─ /speckit.specify [功能描述]
   └─ /speckit.clarify (可選)

🔍 階段 2: 需求研究
   └─ Task agent: researcher (分析現有程式碼和需求)

🏗️ 階段 3: 技術規劃
   └─ /speckit.plan
   └─ Task agent: system-architect (系統架構設計) [可選，並行]
   └─ Task agent: ui-designer (UI/UX 設計) [可選，並行]
   └─ Task agent: component-architect (元件架構) [可選，並行]

📋 階段 4: 任務分解
   └─ /speckit.tasks
   └─ /speckit.analyze

💻 階段 5: 開發實作（TDD 方式）
   5.1 建立基礎架構
       └─ Task agent: base-template-generator
       └─ 使用 MCP: context7 (生成 PHP 類別)

   5.2 測試先行（TDD）
       └─ Task agent: tester (先寫測試)
       └─ 使用 MCP: context7 (PHPUnit 測試)

   5.3 後端開發
       └─ Task agent: coder (實作功能讓測試通過)
       └─ 使用 MCP: context7 (Controller/Service/Repository/Model)

   5.4 前端開發
       └─ Task agent: coder
       └─ 使用 MCP: context7 + tailwindcss-mcp-server (Blade 模板)

   或使用 /speckit.implement (自動處理所有步驟)

✅ 階段 6: 品質保證
   └─ Task agent: reviewer (程式碼審查)
       使用 MCP: context7 (審查 PHP/Blade)
   └─ /speckit.checklist (可選)

🚀 階段 7: 發布管理
   └─ Task agent: pr-manager
```

### 5. 常見場景快速指令

#### 場景 A: 新功能開發（完整流程 + TDD）
```bash
# 步驟 1: 建立規格
/speckit.specify 我想開發一個產品管理系統

# 步驟 2: 技術規劃
/speckit.plan

# 步驟 3: 生成任務
/speckit.tasks

# 步驟 4: TDD 開發（先測試後實作）
Task agent: tester (使用 MCP: context7)
Prompt: "為 ProductService 建立 PHPUnit 測試"

Task agent: coder (使用 MCP: context7)
Prompt: "實作 ProductService 讓測試通過"

Task agent: coder (使用 MCP: context7 + tailwindcss-mcp-server)
Prompt: "建立產品列表 Blade 頁面"

# 或使用自動流程：
/speckit.implement

# 步驟 5: 程式碼審查（使用 MCP: context7）
Task agent: reviewer

# 步驟 6: 建立 PR
Task agent: pr-manager
```

#### 場景 B: Bug 修復（快速流程）
```bash
# 不需要 Spec-Kit，直接使用 Agents

Task agent: researcher
Prompt: "分析產品列表載入緩慢問題"

Task agent: coder (使用 MCP: context7)
Prompt: "優化資料庫查詢，解決 N+1 問題"

Task agent: tester (使用 MCP: context7)
Prompt: "驗證效能改善"

Task agent: reviewer (使用 MCP: context7)
Prompt: "審查程式碼變更"
```

#### 場景 C: 重構（中等流程 + TDD）
```bash
/speckit.specify 重構產品模組，將邏輯移到 Service 層
/speckit.plan
/speckit.tasks

Task agent: tester (使用 MCP: context7)
Prompt: "建立重構前的回歸測試（確保功能不變）"

Task agent: coder (使用 MCP: context7)
Prompt: "執行重構，保持測試通過"

Task agent: reviewer (使用 MCP: context7)
Prompt: "審查重構後的程式碼品質"
```

#### 場景 D: 建立新模組（使用模板生成器 + TDD）
```bash
# 步驟 1: 生成基礎模板（使用 MCP: context7）
Task agent: base-template-generator
Prompt: "建立新的訂單管理模組,包含 Controller、Service、Repository 和 Blade 模板"

# 步驟 2: TDD 開發（使用 MCP: context7）
Task agent: tester
Prompt: "為訂單建立和查詢功能建立 PHPUnit 測試"

# 步驟 3: 實作功能（使用 MCP: context7 + tailwindcss-mcp-server）
Task agent: coder
Prompt: "基於模板實作訂單功能讓測試通過"
```

#### 場景 E: 複雜功能規劃（使用 system-architect + Spec-Kit）
```bash
# 當功能複雜且需要完整架構設計時
/speckit.specify 建立會員系統,包含註冊、登入、權限管理、積分系統

Task agent: system-architect
Prompt: "設計會員系統的整體架構"

/speckit.plan
/speckit.tasks

# 按照生成的任務逐步執行（使用 TDD 方式開發）
```

#### 場景 F: UI 元件開發（前端專注）
```bash
# 步驟 1: UI 設計
Task agent: ui-designer
Prompt: "設計產品卡片元件的 UI 規範"

# 步驟 2: 元件架構
Task agent: component-architect
Prompt: "設計可重用的產品卡片元件架構"

# 步驟 3: 實作元件（使用 MCP: context7 + tailwindcss-mcp-server）
Task agent: coder
Prompt: "實作產品卡片 Blade 元件"

# 步驟 4: 審查
Task agent: reviewer (使用 MCP: context7 + tailwindcss-mcp-server)
Prompt: "審查元件的可重用性和 Tailwind 規範"
```

### 6. 工作流程決策樹

```
開始新任務
    │
    ├─ 是新功能嗎？
    │   ├─ 是 ↓
    │   │   ├─ 需要從頭建立模組？
    │   │   │   └─ 是 → base-template-generator → 使用完整 Spec-Kit 流程
    │   │   └─ 否 → 使用完整 Spec-Kit 流程
    │   └─ 否 ↓
    │
    ├─ 需要架構設計嗎？
    │   ├─ 是 → /speckit.plan + system-architect
    │   └─ 否 ↓
    │
    ├─ 需要複雜的任務規劃嗎？
    │   ├─ 是 → planner → coder → tester
    │   └─ 否 ↓
    │
    ├─ 是重構或改版嗎？
    │   ├─ 是 → /speckit.specify + /speckit.plan + /speckit.tasks
    │   └─ 否 ↓
    │
    └─ 是簡單修復嗎？
        └─ 是 → 直接使用 Agents (researcher → coder → tester → reviewer)
```

---

## ⚡ 快速參考

### 命名規範速查表

```
類別/介面          → PascalCase          (ProductController)
方法/函式          → camelCase           (getProductList)
變數/屬性          → snake_case          ($product_list)
常數              → SCREAMING_SNAKE_CASE (MAX_COUNT)
路由路徑          → kebab-case          (/product-management)
路由參數          → snake_case          ({product_id})
資料表            → snake_case (複數)    (products)
資料欄位          → snake_case          (created_at)
Blade 檔案        → kebab-case.blade.php (product-list.blade.php)
PHP 類別檔案      → PascalCase.php       (ProductController.php)
```

### 必須遵守的安全規範

```php
✅ 使用 Eloquent ORM (禁止原生 SQL)
✅ 使用 {{ $var }} 輸出 (避免 XSS)
✅ 使用 FormRequest 驗證
✅ 使用 try-catch 錯誤處理
✅ 商業邏輯放 Service 層
✅ 每行程式碼附繁體中文註解
✅ Model 必須定義 $fillable 或 $guarded
✅ 使用事務確保資料一致性
```

### 快速指令卡片

**新功能開發:**
```bash
/speckit.specify [描述] → /speckit.plan → /speckit.tasks → /speckit.implement
MCP: 自動使用 context7 + tailwindcss-mcp-server
```

**新模組建立（TDD）:**
```bash
base-template-generator (context7) → tester (context7) → coder (context7 + tailwindcss-mcp-server) → reviewer
```

**Bug 修復:**
```bash
researcher → tester (context7, 寫測試重現 bug) → coder (context7, 修復) → reviewer (context7)
```

**重構（TDD）:**
```bash
/speckit.specify [描述] → tester (context7, 回歸測試) → coder (context7, 重構) → reviewer (context7)
```

**架構設計:**
```bash
/speckit.plan → system-architect → reviewer
```

**複雜功能（Spec-Kit）:**
```bash
/speckit.specify → system-architect → /speckit.tasks → TDD 開發
```

**UI 元件開發:**
```bash
ui-designer → component-architect → coder (context7 + tailwindcss-mcp-server) → reviewer
```

### 檔案位置速查

```
規格文件: specs/[功能名稱]/
Agents: .claude/agents/
Spec-Kit: .specify/
Controllers: app/Http/Controllers/
Models: app/Models/
Services: app/Services/
Repositories: app/Repositories/
Views: resources/views/
測試: tests/Feature/ 和 tests/Unit/
```

---

## 🎯 結語

**本文件是 Laravel 專案開發規範，所有開發人員必須嚴格遵守。**

### 三大開發原則

1. **文件驅動** - 先寫規格 (Spec-Kit)，再寫程式碼
2. **測試先行** - TDD 確保品質，測試覆蓋率 >80%
3. **持續審查** - 程式碼必須經過 code-analyzer + reviewer 才能合併

### 記住這些關鍵點

- 🔒 **安全第一** - SQL 注入、XSS、CSRF 防護
- 📝 **註解必備** - 每行程式碼都要有繁體中文註解
- 🏗️ **分層架構** - Controller → Service → Repository
- ✅ **錯誤處理** - 所有可能失敗的操作都要 try-catch
- 🎨 **規範一致** - 嚴格遵守命名規範和程式碼風格
- 🔄 **流程完整** - 使用 Spec-Kit + Agents 確保品質

### 遇到問題時

1. 先查閱本文件的快速參考
2. 查看 `specs/` 目錄的相關規格文件
3. 參考 `.claude/agents/` 的 Agent 定義
4. 使用 `/speckit.clarify` 澄清需求

---

**祝開發順利！** 🚀

*如有任何問題或建議，請聯繫技術團隊*

---

**文件版本:** 1.0
**最後更新:** 2025-11-09
**維護者:** 技術團隊
