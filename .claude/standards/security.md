# 安全性規範（強制）

---

## 1. SQL 注入防護

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

---

## 2. XSS 防護

```blade
{{-- ✅ 正確：使用 {{ }} 自動轉義 --}}
<h1>{{ $product->name }}</h1>
<p>{{ $user->description }}</p>

{{-- ✅ 當確實需要 HTML 時，先清理內容 --}}
<div>{!! clean($product->html_content) !!}</div>

{{-- ❌ 禁止：直接輸出未轉義的內容 --}}
<h1>{!! $product->name !!}</h1>  {{-- 危險！ --}}
```

---

## 3. 輸入驗證（強制）

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

---

## 4. Mass Assignment 防護

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

---

## 5. 錯誤處理（強制）

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

---

## 安全檢查清單

```php
✅ 使用 Eloquent ORM (禁止原生 SQL)
✅ 使用 {{ $var }} 輸出 (避免 XSS)
✅ 使用 FormRequest 驗證
✅ 使用 try-catch 錯誤處理
✅ Model 必須定義 $fillable 或 $guarded
✅ 使用事務確保資料一致性
```
