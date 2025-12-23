# Laravel 開發規範 - 核心指引

> Laravel 12 + Blade + Tailwind CSS 全端開發指南
>
> **版本:** 3.0 | **最後更新:** 2025-12-19

---

## 專案概覽

| 類別 | 技術 | 版本 |
|-----|------|------|
| **後端框架** | Laravel | 12 |
| **PHP 版本** | PHP | 8.4 |
| **資料庫 (開發)** | MySQL | 8 |
| **資料庫 (正式)** | MariaDB | 11.4.5 |
| **前端模板** | Blade | - |
| **前端樣式** | Tailwind CSS | - |

**開發語言:** 繁體中文（文檔與註解）、英文（程式碼）

---

## 核心原則（必讀）

### 安全第一
- 使用 Eloquent ORM（禁止原生 SQL）
- 使用 `{{ $var }}` 輸出（避免 XSS）
- 使用 FormRequest 驗證
- Model 必須定義 `$fillable` 或 `$guarded`

### 分層架構
- **Controller**：只處理 HTTP 請求和回應
- **Service**：包含所有商業邏輯
- **Repository**：只處理資料庫操作
- 禁止在 Controller 中直接操作 Model

### 程式碼品質
- 每行程式碼附繁體中文註解
- 使用 try-catch 錯誤處理
- 使用事務確保資料一致性
- 使用 4 個空格縮排（禁止 Tab）

---

## 命名規範速查

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

---

## 前端開發強制規則

**開發任何 UI 前，必須：**
1. 執行 `/components` 查看可用組件
2. 優先使用共用組件（位於 `resources/views/components/`）
3. 只有在完全無法滿足需求時，才創建新組件

**禁止：**
- 不檢查就開始寫 UI
- 手動寫 `<input>`, `<textarea>`, `<select>` 等表單元素
- 手動寫操作按鈕（查看/編輯/刪除）

**正確做法：**
```blade
<x-forms.text-input name="name" label="產品名稱" :value="old('name')" required />
<x-forms.select-field name="category_id" label="分類" :options="$categories" />
<x-action-buttons :view-url="..." :edit-url="..." :delete-url="..." />
```

---

## 常用範例

### Controller
```php
class ProductController extends Controller
{
    private ProductService $product_service;

    public function __construct(ProductService $product_service)
    {
        $this->product_service = $product_service;
    }

    public function index(Request $request)
    {
        $filters = $request->only(['category_id', 'keyword']);
        $products = $this->product_service->getProductList($filters);
        return view('products.index', compact('products'));
    }
}
```

### Service
```php
class ProductService
{
    private ProductRepository $product_repository;

    public function getProductList(array $filters = [])
    {
        return $this->product_repository->getFilteredProducts($filters);
    }
}
```

---

## 工具清單

### Slash Commands（明確調用）
| 指令 | 用途 |
|-----|------|
| `/bdd` | 定義行為需求（Gherkin） |
| `/components` | 列出可用共用組件 |
| `/tdd` | 測試驅動開發 |
| `/impact-analysis` | 分析變更影響範圍 |
| `/review-staged` | 審查 Git 暫存區 |

### MCP 工具
| 工具 | 用途 |
|-----|------|
| Sequential Thinking | 結構化思考、問題分解 |
| tailwindcss-mcp-server | Tailwind CSS 生成 |

### Agents
| Agent | 用途 |
|-------|------|
| researcher | 分析現有程式碼 |
| tester | 撰寫測試 |
| coder | 實作程式碼 |
| reviewer | 程式碼審查 |

---

## 詳細規範索引

所有詳細規範文件位於 `.claude/standards/` 目錄：

- **[命名規範](/.claude/standards/naming.md)**
- **[程式碼風格](/.claude/standards/coding-style.md)**
- **[安全規範](/.claude/standards/security.md)**
- **[架構模式](/.claude/standards/architecture.md)**
- **[前端開發](/.claude/standards/frontend.md)**
- **[共用組件](/.claude/standards/components.md)**

---

## 專案結構

```
laravel-project/
├── .claude/
│   ├── skills/           # AI Skills（自動觸發的工作流程）
│   ├── commands/         # Slash Commands（明確調用）
│   ├── agents/           # AI Agents 定義
│   └── standards/        # 詳細規範文件
├── app/
│   ├── Http/Controllers/
│   ├── Services/
│   ├── Repositories/
│   └── Models/
├── resources/views/
│   └── components/       # 共用組件
└── tests/
```

---

**文件版本:** 3.0
**最後更新:** 2025-12-19

**變更記錄:**
- v3.0 (2025-12-19): 重構為 Skills 架構，大幅精簡 CLAUDE.md
- v2.3 (2025-12-17): 新增「AI 工具自動化規則」
- v2.0 (2025-11-14): 拆分為模組化文件結構
