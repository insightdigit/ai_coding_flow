---
name: ui-dev
description: UI/前端開發流程。當用戶提到「頁面」「表單」「畫面」「UI」「前端」「介面」等關鍵字時自動使用。優先使用共用組件。
---

# UI/前端開發流程

## 觸發條件

當用戶提問包含以下關鍵字時自動啟用：
- 「頁面」「表單」「畫面」「UI」「前端」「介面」

## 執行流程

```
1. Sequential Thinking - 分析 UI 需求
2. /components - 掃描可用共用組件（必須）
3. /bdd - 定義 UI 行為
4. /tdd - 撰寫 Feature Test
5. coder agent - 實作 UI（使用共用組件）
6. reviewer agent - 審查
```

## 步驟說明

### 1. 掃描共用組件（必須）

執行 `/components` 查看可用組件。

**禁止：**
- 手動寫 `<input>`, `<textarea>`, `<select>`
- 不檢查就開始寫 UI

### 2. 實作 UI

使用共用組件：

```blade
<x-section-card title="基本資訊">
    <x-forms.text-input name="name" label="名稱" required />
    <x-forms.select-field name="category_id" label="分類" :options="$categories" />
</x-section-card>
```

## 品質要求

- 必須使用共用組件
- 響應式設計
- 所有 Feature Test 通過
