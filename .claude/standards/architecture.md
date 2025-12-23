# 架構模式（強制）

---

## 設計原則：高內聚低耦合

### 高內聚 (High Cohesion)

**定義：** 模組內的元素應該緊密相關，共同完成單一明確的職責。

**檢查清單：**
- ✅ 類別只負責單一職責（SRP）
- ✅ 方法只做一件事，且做好
- ✅ 相關功能集中在同一模組
- ✅ 類別名稱能準確描述其職責
- ❌ 避免「萬能類別」（God Class）
- ❌ 避免方法超過 50 行

**範例：**
```php
// ✅ 高內聚：ProductService 只處理產品相關邏輯
class ProductService
{
    public function createProduct(array $data) { }
    public function updateProduct(int $id, array $data) { }
    public function deleteProduct(int $id) { }
    public function getProductList(array $filters) { }
}

// ❌ 低內聚：混合不相關的職責
class ProductService
{
    public function createProduct(array $data) { }
    public function sendEmail(string $to, string $subject) { }  // 不相關
    public function generateReport() { }  // 不相關
}
```

### 低耦合 (Low Coupling)

**定義：** 模組間應該減少依賴，透過抽象介面溝通。

**檢查清單：**
- ✅ 使用依賴注入（DI）而非直接 `new`
- ✅ 依賴抽象（介面）而非具體實作
- ✅ 模組間透過明確介面溝通
- ✅ 避免循環依賴
- ❌ 禁止在 Service 中直接 `new` 其他 Service
- ❌ 禁止跨層直接存取（Controller 不能直接用 Repository）

**範例：**
```php
// ✅ 低耦合：透過建構函式注入依賴
class ProductService
{
    private ProductRepositoryInterface $product_repository;
    private NotificationServiceInterface $notification_service;

    public function __construct(
        ProductRepositoryInterface $product_repository,
        NotificationServiceInterface $notification_service
    ) {
        $this->product_repository = $product_repository;
        $this->notification_service = $notification_service;
    }
}

// ❌ 高耦合：直接建立依賴
class ProductService
{
    public function createProduct(array $data)
    {
        $repository = new ProductRepository();  // 緊耦合
        $mailer = new Mailer();  // 緊耦合
    }
}
```

### 耦合度評估指標

| 耦合類型 | 說明 | 等級 |
|---------|------|------|
| 無耦合 | 模組完全獨立 | 最佳 |
| 資料耦合 | 只傳遞基本資料 | 良好 |
| 訊息耦合 | 透過介面/契約溝通 | 良好 |
| 控制耦合 | 傳遞控制參數影響行為 | 注意 |
| 共用耦合 | 共用全域變數 | 避免 |
| 內容耦合 | 直接存取內部實作 | 禁止 |

---

## Controller → Service → Repository 分層

### Controller 層：只處理 HTTP 請求和回應

```php
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
```

---

### Service 層：處理商業邏輯

```php
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
```

---

### Repository 層：只處理資料存取

```php
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

---

## 架構規則

- ✅ Controller：只處理 HTTP 請求和回應，不包含商業邏輯
- ✅ Service：包含所有商業邏輯、驗證、計算
- ✅ Repository：只處理資料庫操作，不包含商業邏輯
- ✅ 使用依賴注入傳遞依賴
- ❌ 禁止在 Controller 中直接操作 Model
- ❌ 禁止在 Repository 中包含商業邏輯

---

## 資料庫操作規範

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

---

## Laravel 最佳實踐

### Eloquent 使用

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

### 快取策略

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

### 效能優化檢查

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
