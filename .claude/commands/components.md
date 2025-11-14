---
description: 自動掃描並列出所有可用的共用 Blade 組件
tags: [project, ui, components]
---

# 📦 掃描專案共用組件

> **🚨 開發任何 UI 前，必須先執行此指令檢查可用組件！**

請執行以下操作：

## 第一步：掃描所有組件

使用以下指令掃描 `resources/views/components/` 資料夾：

```bash
find resources/views/components -name "*.blade.php" -type f | sort
```

## 第二步：分類整理

將掃描結果按照以下分類整理並顯示：

### 🎯 通用共用組件（優先使用）

1. **表單組件** (`components/forms/`)
   - 列出所有 `forms/` 資料夾下的組件
   - 用途：文字輸入、下拉選單、圖片上傳等

2. **UI 組件** (`components/` 根目錄)
   - 列出根目錄下的通用組件
   - 如：page-header, breadcrumb, status-badge, alert, button, section-card 等

3. **操作組件**
   - action-buttons, back-button, mobile-action-buttons 等

4. **表格組件**
   - table-row, pagination-info 等

5. **導航組件**
   - navbar, scroll-to-top 等

6. **Dashboard 組件** (`components/dashboard/`)
   - module-card, stat-card 等

7. **圖示組件** (`components/icons/`)
   - document, trash 等

### 📋 模組專用組件（參考用）

- **Breadcrumbs 組件** (`components/breadcrumbs/`)
- **Cost Simulations 組件** (`components/cost-simulations/`)
- **Inspections 組件** (`components/inspections/`)
- **Recipes 組件** (`components/recipes/`)
- **Samplings 組件** (`components/samplings/`)

## 第三步：使用說明

針對最常用的組件，提供使用範例：

### 表單組件

```blade
<!-- 文字輸入 -->
<x-forms.text-input name="name" label="名稱" :value="old('name')" />

<!-- 多行文字 -->
<x-forms.textarea-field name="description" label="描述" />

<!-- 下拉選單 -->
<x-forms.select-field name="category_id" label="分類" :options="$categories" />

<!-- 圖片上傳 -->
<x-forms.image-upload name="image" label="圖片" />
```

### UI 組件

```blade
<!-- 頁面標題 -->
<x-page-header title="產品管理" />

<!-- 麵包屑 -->
<x-breadcrumb :items="[...]" />

<!-- 狀態標籤 -->
<x-status-badge :status="$is_active" />

<!-- 提示訊息 -->
<x-alert type="success" message="操作成功" />

<!-- 按鈕 -->
<x-button type="primary">送出</x-button>

<!-- 區塊容器 -->
<x-section-card title="基本資訊">內容</x-section-card>

<!-- 空狀態 -->
<x-empty-state message="尚無資料" />
```

### 操作組件

```blade
<!-- 操作按鈕組 -->
<x-action-buttons :view-url="..." :edit-url="..." :delete-url="..." />

<!-- 返回按鈕 -->
<x-back-button url="{{ route('products.index') }}" />
```

## 第四步：檢查清單

根據正在開發的頁面類型，提示應該使用的組件：

**開發表單頁面？**
→ 檢查 `forms/`, `page-header`, `breadcrumb`, `section-card`, `button`, `back-button`

**開發列表頁面？**
→ 檢查 `page-header`, `breadcrumb`, `filter-panel`, `status-badge`, `action-buttons`, `pagination-info`, `empty-state`

**開發詳情頁面？**
→ 檢查 `page-header`, `breadcrumb`, `back-button`, `section-card`, `data-field`, `status-badge`

## 第五步：禁止行為提醒

⚠️ **重要：如果發現要開發的 UI 元素已經有共用組件，必須使用共用組件，禁止重複創建！**

❌ 禁止在功能模組資料夾內創建重複的表單組件
❌ 禁止在功能模組資料夾內創建重複的按鈕組件
❌ 禁止在功能模組資料夾內創建重複的狀態標籤
❌ 禁止複製貼上相似的 UI 程式碼到不同檔案

✅ 優先使用 `components/` 資料夾中的共用組件
✅ 如果組件不符需求，考慮擴展現有組件（增加參數）
✅ 只有在完全無法重用時，才創建新組件

---

**完成後，請明確告知用戶：**
1. 掃描到的通用組件數量
2. 針對當前開發需求，建議使用哪些組件
3. 如果沒有合適的組件，才能創建新組件
