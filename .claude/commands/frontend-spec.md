---
description: 撰寫前端技術規格文檔，定義 Blade 頁面結構、組件使用和使用者互動。
---

## User Input

```text
$ARGUMENTS
```

You **MUST** use the user input to understand what frontend specification to create.

## Goal

撰寫前端技術規格文檔，在開發前定義：

1. **頁面結構** - Blade 頁面佈局、區塊組成
2. **組件使用** - 使用哪些共用組件
3. **表單設計** - 表單欄位、驗證、錯誤顯示
4. **使用者流程** - 操作步驟、互動行為
5. **樣式規格** - Tailwind 類別、響應式設計

## Operating Constraints

- **文檔優先**: 先完成規格文檔，不寫任何程式碼
- **組件優先**: 必須先執行 `/components` 查看可用共用組件
- **繁體中文**: 所有說明使用繁體中文

## Execution Steps

### 1. 分析需求

從 `$ARGUMENTS` 解析要設計的功能：

**支援格式：**
```bash
# 功能描述
/frontend-spec 產品管理頁面

# 指定頁面
/frontend-spec 產品列表頁、產品表單頁

# 指定組件
/frontend-spec 訂單狀態追蹤組件
```

### 2. 查看可用組件

**必須執行 `/components` 取得可用組件清單**

常見組件類型：
- `x-forms.*` - 表單輸入元件
- `x-action-buttons` - 操作按鈕
- `x-data-table` - 資料表格
- `x-modal` - 模態框
- `x-alert-message` - 提示訊息

### 3. 撰寫頁面結構規格

**3.1 頁面資訊**

```markdown
## 頁面資訊

| 項目 | 值 |
|-----|-----|
| 頁面名稱 | 產品列表 |
| 路由 | GET /products |
| Blade 檔案 | resources/views/products/index.blade.php |
| Layout | layouts.app |
| Controller | ProductController@index |
```

**3.2 頁面結構圖**

```markdown
## 頁面結構

```
┌─────────────────────────────────────────────┐
│ @section('header')                          │
│  頁面標題：產品管理      [+ 新增產品] 按鈕   │
├─────────────────────────────────────────────┤
│ 篩選區域                                     │
│  [分類▼] [關鍵字______] [搜尋] [清除]       │
├─────────────────────────────────────────────┤
│ 資料表格                                     │
│  ┌─────┬─────┬─────┬─────┬─────┐           │
│  │ 名稱 │ 分類 │ 價格 │ 狀態 │ 操作 │         │
│  ├─────┼─────┼─────┼─────┼─────┤           │
│  │ ... │ ... │ ... │ ●/○ │ 👁✏🗑│           │
│  └─────┴─────┴─────┴─────┴─────┘           │
├─────────────────────────────────────────────┤
│ 分頁                                         │
│          [<] 1 2 3 ... 10 [>]               │
└─────────────────────────────────────────────┘
```
```

### 4. 撰寫組件使用規格

**4.1 共用組件**

```markdown
## 共用組件使用

### 頁面標題區

```blade
<x-page-header title="產品管理">
    <x-slot name="actions">
        <a href="{{ route('products.create') }}" class="btn btn-primary">
            新增產品
        </a>
    </x-slot>
</x-page-header>
```

### 篩選表單

| 欄位 | 組件 | 屬性 |
|-----|------|------|
| 分類 | x-forms.select-field | name="category_id", :options="$categories" |
| 關鍵字 | x-forms.text-input | name="keyword", placeholder="搜尋..." |

```blade
<x-forms.select-field
    name="category_id"
    label="分類"
    :options="$categories"
    :selected="request('category_id')"
    placeholder="全部分類"
/>
```

### 操作按鈕

```blade
<x-action-buttons
    :view-url="route('products.show', $product)"
    :edit-url="route('products.edit', $product)"
    :delete-url="route('products.destroy', $product)"
/>
```
```

**4.2 自訂組件（如需要）**

```markdown
## 自訂組件

### x-products.status-badge

**用途**: 顯示產品狀態標籤

**檔案**: `resources/views/components/products/status-badge.blade.php`

**Props:**
| 名稱 | 類型 | 必填 | 說明 |
|-----|------|------|------|
| active | bool | ✓ | 是否啟用 |

**顯示：**
| 狀態 | 樣式 | 文字 |
|-----|------|------|
| true | bg-green-100 text-green-800 | 啟用 |
| false | bg-gray-100 text-gray-800 | 停用 |

**使用：**
```blade
<x-products.status-badge :active="$product->is_active" />
```
```

### 5. 撰寫表單規格

**5.1 表單欄位**

```markdown
## 表單設計

### 產品表單 (create/edit 共用)

**表單結構：**
```
┌─────────────────────────────────────┐
│ 基本資訊                             │
│  產品名稱: [________________] *      │
│  分類:     [選擇分類▼      ] *      │
│  價格:     [________] 元    *       │
├─────────────────────────────────────┤
│ 詳細資訊                             │
│  描述:     [                  ]     │
│            [                  ]     │
│  狀態:     [✓] 啟用                 │
├─────────────────────────────────────┤
│         [取消]  [儲存]              │
└─────────────────────────────────────┘
```

**欄位定義：**

| 欄位 | 組件 | name | 必填 | 說明 |
|-----|------|------|------|------|
| 產品名稱 | x-forms.text-input | name | ✓ | max:255 |
| 分類 | x-forms.select-field | category_id | ✓ | 下拉選單 |
| 價格 | x-forms.number-input | price | ✓ | min:0 |
| 描述 | x-forms.textarea | description | - | max:1000 |
| 狀態 | x-forms.checkbox | is_active | - | 預設勾選 |

**Blade 範例：**
```blade
<x-forms.text-input
    name="name"
    label="產品名稱"
    :value="old('name', $product->name ?? '')"
    required
/>

<x-forms.select-field
    name="category_id"
    label="分類"
    :options="$categories"
    :selected="old('category_id', $product->category_id ?? '')"
    required
/>

<x-forms.number-input
    name="price"
    label="價格"
    :value="old('price', $product->price ?? 0)"
    min="0"
    step="0.01"
    suffix="元"
    required
/>

<x-forms.textarea
    name="description"
    label="描述"
    :value="old('description', $product->description ?? '')"
    rows="4"
/>

<x-forms.checkbox
    name="is_active"
    label="啟用"
    :checked="old('is_active', $product->is_active ?? true)"
/>
```
```

**5.2 錯誤顯示**

```markdown
### 錯誤顯示

**欄位錯誤：** 使用組件內建的 @error 顯示
```blade
<!-- x-forms 組件自動處理 -->
@error('name')
    <span class="text-red-600 text-sm">{{ $message }}</span>
@enderror
```

**全域訊息：** 使用 session flash
```blade
<!-- 在 layout 或頁面頂部 -->
@if(session('success'))
    <x-alert type="success" :message="session('success')" />
@endif

@if(session('error'))
    <x-alert type="error" :message="session('error')" />
@endif
```
```

### 6. 撰寫互動規格

**6.1 使用者操作流程**

```markdown
## 使用者流程

### 查看列表

1. 進入 /products
2. 看到產品列表（分頁顯示）
3. 可使用篩選條件過濾
4. 點擊分頁切換頁面

### 新增產品

1. 點擊「新增產品」按鈕
2. 跳轉到 /products/create
3. 填寫表單
4. 點擊「儲存」
5. 驗證失敗 → 顯示錯誤，留在表單頁
6. 驗證成功 → 跳轉回列表，顯示成功訊息

### 編輯產品

1. 點擊編輯按鈕 (✏)
2. 跳轉到 /products/{id}/edit
3. 表單預填現有資料
4. 修改後點擊「儲存」
5. 驗證成功 → 跳轉回列表

### 刪除產品

1. 點擊刪除按鈕 (🗑)
2. 顯示確認對話框
3. 確認 → 送出 DELETE 請求
4. 成功 → 重新載入列表，顯示成功訊息
```

**6.2 JavaScript 互動**

```markdown
### JavaScript 行為

**刪除確認：**
```blade
<form method="POST" action="{{ route('products.destroy', $product) }}"
      onsubmit="return confirm('確定要刪除這個產品嗎？')">
    @csrf
    @method('DELETE')
    <button type="submit">刪除</button>
</form>
```

**或使用 Alpine.js：**
```blade
<button x-on:click="if(confirm('確定要刪除？')) $refs.deleteForm.submit()">
    刪除
</button>
```
```

### 7. 撰寫資料規格

```markdown
## Controller 傳遞的資料

### index 頁面

| 變數 | 類型 | 說明 |
|-----|------|------|
| $products | LengthAwarePaginator | 產品列表（含分頁） |
| $categories | Collection | 分類選項 [{id, name}] |

### create 頁面

| 變數 | 類型 | 說明 |
|-----|------|------|
| $categories | Collection | 分類選項 |

### edit 頁面

| 變數 | 類型 | 說明 |
|-----|------|------|
| $product | Product | 編輯的產品 |
| $categories | Collection | 分類選項 |
```

### 8. 撰寫樣式規格

```markdown
## 樣式規格

### 主要容器
```blade
<div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6">
```

### 卡片
```blade
<div class="bg-white rounded-lg shadow-sm border border-gray-200 p-6">
```

### 響應式設計

| 斷點 | 行為 |
|-----|------|
| < sm | 表格改為卡片列表 |
| sm-md | 隱藏次要欄位 |
| md+ | 完整表格顯示 |
```

---

## Output Format

```markdown
# 前端技術規格

**模組**: [模組名稱]
**版本**: 1.0
**日期**: [日期]

---

## 1. 頁面清單

| 頁面 | 路由 | Blade 檔案 |
|-----|------|-----------|
| 列表 | /products | products/index.blade.php |
| 新增 | /products/create | products/create.blade.php |
| 編輯 | /products/{id}/edit | products/edit.blade.php |

---

## 2. 頁面結構

### 2.1 列表頁
[結構圖]

### 2.2 表單頁
[結構圖]

---

## 3. 組件使用

### 3.1 共用組件
[組件清單]

### 3.2 自訂組件
[自訂組件定義]

---

## 4. 表單設計

### 4.1 欄位定義
[表單欄位]

### 4.2 錯誤顯示
[錯誤處理方式]

---

## 5. 使用者流程

[操作流程]

---

## 6. Controller 資料

[傳遞的變數]

---

## 7. 樣式規格

[Tailwind 類別]
```

---

## Best Practices

- **組件優先**: 優先使用共用組件
- **一致性**: 遵循專案既有的頁面風格
- **可訪問性**: 考慮表單標籤和錯誤提示

## Operating Principles

- **NEVER** 在規格階段寫實際程式碼
- **ALWAYS** 先執行 `/components` 查看可用組件
- **ALWAYS** 使用繁體中文撰寫說明

## Context

$ARGUMENTS
