# Laravel 開發規範 - 核心指引

> Laravel 12 + Blade + Tailwind CSS 全端開發指南
>
> **版本:** 2.0 | **最後更新:** 2025-11-14

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

---

## 📝 核心原則（必讀）

### 安全第一
- ✅ 使用 Eloquent ORM (禁止原生 SQL)
- ✅ 使用 `{{ $var }}` 輸出 (避免 XSS)
- ✅ 使用 FormRequest 驗證
- ✅ Model 必須定義 `$fillable` 或 `$guarded`

### 分層架構
- ✅ Controller：只處理 HTTP 請求和回應
- ✅ Service：包含所有商業邏輯
- ✅ Repository：只處理資料庫操作
- ❌ 禁止在 Controller 中直接操作 Model

### 程式碼品質
- ✅ 每行程式碼附繁體中文註解
- ✅ 使用 try-catch 錯誤處理
- ✅ 使用事務確保資料一致性
- ✅ 使用 4 個空格縮排（禁止 Tab）

---

## 🔍 命名規範速查

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

## 🚨 前端開發強制規則

### 共用組件優先使用

**開發任何 UI 前，必須：**

1. **執行 `/components` 查看可用組件**
2. **優先使用共用組件**（位於 `resources/views/components/`）
3. **只有在完全無法滿足需求時，才創建新組件**

**絕對禁止：**
- ❌ 不檢查就開始寫 UI
- ❌ 手動寫 `<input>`, `<textarea>`, `<select>` 等表單元素
- ❌ 手動寫操作按鈕（查看/編輯/刪除）
- ❌ 在模組資料夾內創建重複的通用 UI 組件

**正確做法：**
```blade
{{-- ✅ 使用共用組件 --}}
<x-forms.text-input name="name" label="產品名稱" :value="old('name')" required />
<x-forms.select-field name="category_id" label="分類" :options="$categories" />
<x-action-buttons :view-url="..." :edit-url="..." :delete-url="..." />
```

---

## 🔄 開發工作流程

### 三大支柱

| 工具 | 用途 | 適用場景 |
|-----|------|---------|
| **Spec-Kit** | 規格化文件與任務管理 | 新功能開發、重構 |
| **Agents** | 專業化開發代理 | 程式碼實作、測試、審查 |
| **MCP 工具** | 程式碼生成與規範檢查 | 所有程式碼生成 |

### 快速指令

**新功能開發:**
```bash
/speckit.specify [描述] → /speckit.plan → /speckit.tasks → /speckit.implement
```

**Bug 修復:**
```bash
researcher → tester → coder → reviewer
```

**重構（TDD）:**
```bash
/speckit.specify → tester (回歸測試) → coder (重構) → reviewer
```

**UI 開發:**
```bash
/components (必須) → coder → reviewer
```

---

## 📚 詳細規範索引

所有詳細規範文件位於 `.claude/standards/` 目錄：

### 核心規範
- **[命名規範](./.claude/standards/naming.md)** - 完整的命名規則
- **[程式碼風格](./.claude/standards/coding-style.md)** - 縮排、註解、類別結構
- **[安全規範](./.claude/standards/security.md)** - SQL 注入、XSS、輸入驗證
- **[架構模式](./.claude/standards/architecture.md)** - 三層架構、資料庫操作

### 前端規範
- **[前端開發](./.claude/standards/frontend.md)** - Blade、Tailwind、JavaScript
- **[共用組件](./.claude/standards/components.md)** - 組件使用規範（強制）

### 流程規範
- **[工作流程](./.claude/standards/workflow.md)** - Spec-Kit、Agents、MCP
- **[快速參考](./.claude/standards/quick-ref.md)** - 速查表

---

## ⚡ 常用範例

### Controller 範例
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
        // 只負責接收請求和返回回應
        $filters = $request->only(['category_id', 'keyword']);
        $products = $this->product_service->getProductList($filters);

        return view('products.index', compact('products'));
    }
}
```

### Service 範例
```php
class ProductService
{
    private ProductRepository $product_repository;

    public function getProductList(array $filters = [])
    {
        // 商業邏輯處理
        return $this->product_repository->getFilteredProducts($filters);
    }
}
```

### Blade 表單範例（使用共用組件）
```blade
<form method="POST" action="{{ route('products.store') }}">
    @csrf

    <x-section-card title="基本資訊">
        <x-forms.text-input name="name" label="產品名稱" required />
        <x-forms.select-field name="category_id" label="分類" :options="$categories" />
    </x-section-card>

    <div class="flex justify-end space-x-4 mt-6">
        <x-back-button url="{{ route('products.index') }}" />
        <x-button type="primary">建立產品</x-button>
    </div>
</form>
```

---

## 🎯 三大開發原則

1. **文件驅動** - 先寫規格 (Spec-Kit)，再寫程式碼
2. **測試先行** - TDD 確保品質，測試覆蓋率 >80%
3. **持續審查** - 程式碼必須經過 reviewer 才能合併

---

## 📁 專案結構

```
laravel-project/
├── .claude/
│   ├── standards/         # 📚 詳細規範文件（本次新增）
│   ├── agents/           # AI Agents 定義
│   └── commands/         # Slash Commands
├── app/
│   ├── Http/Controllers/ # Controller 層
│   ├── Services/         # Service 層（商業邏輯）
│   ├── Repositories/     # Repository 層（資料存取）
│   └── Models/           # Eloquent Models
├── resources/views/
│   └── components/       # 🚨 共用組件（優先使用）
├── specs/                # Spec-Kit 規格文件
└── tests/                # 測試檔案
```

---

## 📞 遇到問題時

1. 先查閱 [快速參考](./.claude/standards/quick-ref.md)
2. 查看對應的詳細規範文件（`.claude/standards/`）
3. 使用 `/speckit.clarify` 澄清需求
4. 參考 `specs/` 目錄的相關規格文件

---

**祝開發順利！** 🚀

---

**文件版本:** 2.0
**最後更新:** 2025-11-14
**維護者:** 技術團隊

**變更記錄:**
- v2.0 (2025-11-14): 拆分為模組化文件結構，提升效能
- v1.0 (2025-11-09): 初始版本
