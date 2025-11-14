# 程式碼風格規範

---

## 1. 縮排與空格

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

---

## 2. 條件語句

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

---

## 3. 註解規範

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

---

## 4. 類別與方法結構

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
