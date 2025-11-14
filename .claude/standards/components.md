# 共用組件使用規範（強制）

> **🚨 重要：開發任何 UI 前，必須先檢查並使用共用組件！**

---

## 強制規範

```bash
# ✅ 正確流程
1. 使用 /components 指令查看可用組件
2. 檢查是否有符合需求的組件
3. 優先使用共用組件
4. 只有在完全無法滿足需求時，才考慮創建新組件

# ❌ 禁止行為
❌ 在功能模組資料夾內創建重複的表單組件
❌ 在功能模組資料夾內創建重複的按鈕組件
❌ 在功能模組資料夾內創建重複的狀態標籤
❌ 複製貼上相似的 UI 程式碼到不同檔案
❌ 不檢查就直接開發 UI
```

---

## 共用組件位置

**所有共用組件位於：** `resources/views/components/`

**分類：**
- **表單組件：** `components/forms/` - text-input, textarea-field, select-field, image-upload
- **UI 組件：** `components/` - page-header, breadcrumb, status-badge, alert, button
- **操作組件：** `components/` - action-buttons, back-button, mobile-action-buttons

---

## 使用前檢查清單

> **開發 UI 前，必須執行：**
> ```bash
> find resources/views/components -name "*.blade.php" -type f | sort
> ```

---

## 新增頁面開發檢查清單

開發任何新增（Create）頁面前，檢查以下組件是否可用：

| 需要的元素 | 使用組件 | 使用範例 |
|-----------|---------|---------|
| **頁面標題** | `<x-page-header>` | `<x-page-header title="新增產品" />` |
| **麵包屑** | `<x-breadcrumb>` | `<x-breadcrumb :items="[...]" />` |
| **文字輸入框** | `<x-forms.text-input>` | `<x-forms.text-input name="name" label="產品名稱" required />` |
| **多行文字框** | `<x-forms.textarea-field>` | `<x-forms.textarea-field name="description" rows="5" />` |
| **下拉選單** | `<x-forms.select-field>` | `<x-forms.select-field name="category_id" :options="$categories" />` |
| **圖片上傳** | `<x-forms.image-upload>` | `<x-forms.image-upload name="image" />` |
| **區塊容器** | `<x-section-card>` | `<x-section-card title="基本資訊">...</x-section-card>` |
| **送出按鈕** | `<x-button>` | `<x-button type="primary">建立產品</x-button>` |
| **取消按鈕** | `<x-back-button>` | `<x-back-button url="{{ route('products.index') }}" />` |

**✅ 正確做法：**
```blade
<form method="POST" action="{{ route('products.store') }}">
    @csrf

    <x-section-card title="基本資訊">
        <x-forms.text-input name="name" label="產品名稱" :value="old('name')" required />
        <x-forms.select-field name="category_id" label="分類" :options="$categories" required />
    </x-section-card>

    <div class="flex justify-end space-x-4 mt-6">
        <x-back-button url="{{ route('products.index') }}" />
        <x-button type="primary">建立產品</x-button>
    </div>
</form>
```

---

## 編輯頁面開發檢查清單

| 需要的元素 | 使用組件 | 使用範例 |
|-----------|---------|---------|
| **文字輸入框** | `<x-forms.text-input>` | `<x-forms.text-input :value="old('name', $product->name)" />` |
| **圖片上傳** | `<x-forms.image-upload>` | `<x-forms.image-upload :current-image="$product->image_url" />` |

**⚠️ 編輯頁面特別注意：**
- ✅ 必須使用 `old('name', $product->name)` 保留舊值（第二個參數是預設值）
- ✅ 必須使用 `@method('PUT')` 或 `@method('PATCH')`
- ✅ 圖片上傳組件要傳入 `:current-image` 顯示現有圖片

---

## 列表頁面開發檢查清單

| 需要的元素 | 使用組件 |
|-----------|---------|
| **狀態標籤** | `<x-status-badge :status="$item->is_active" />` |
| **操作按鈕組** | `<x-action-buttons :view-url="..." :edit-url="..." />` |
| **空狀態** | `<x-empty-state message="尚無資料" />` |

---

## 詳情頁面開發檢查清單

| 需要的元素 | 使用組件 |
|-----------|---------|
| **資訊區塊** | `<x-section-card title="基本資訊">...</x-section-card>` |
| **資料欄位** | `<x-data-field label="產品名稱" :value="$product->name" />` |
| **狀態標籤** | `<x-status-badge :status="$product->is_active" />` |

---

## 絕對禁止的行為

❌ **不檢查就開始寫 UI**
❌ **複製貼上類似的 HTML 結構到多個檔案**
❌ **在模組資料夾內創建通用 UI 組件**
❌ **手動寫 `<input>`, `<textarea>`, `<select>` 等表單元素**
❌ **手動寫操作按鈕（查看/編輯/刪除）**
❌ **手動寫狀態標籤（啟用/停用）**

---

## 正確的開發流程

1. **掃描組件** → 執行 `find resources/views/components -name "*.blade.php"`
2. **選擇組件** → 確認要使用哪些共用組件
3. **開始開發** → 優先使用共用組件
4. **遇到問題** → 考慮擴展現有組件（增加參數）
5. **完全無法重用** → 才創建新組件到 `components/` 資料夾

---

## AI 開發時的強制檢查

**當 AI (Claude) 開發 UI 時，必須遵循：**

1. **第一步：** 使用 `/components` 列出可用組件
2. **第二步：** 在回應中明確說明使用了哪些共用組件
3. **第三步：** 如果沒有合適的組件，必須說明原因
4. **第四步：** 只有在無法使用現有組件時，才能創建新組件
