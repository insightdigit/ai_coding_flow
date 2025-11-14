# 快速參考

---

## 命名規範速查表

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

## 必須遵守的安全規範

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

---

## 快速指令卡片

### 新功能開發
```bash
/speckit.specify [描述] → /speckit.plan → /speckit.tasks → /speckit.implement
MCP: 自動使用 context7 + tailwindcss-mcp-server
```

### 新模組建立（TDD）
```bash
base-template-generator (context7) → tester (context7) → coder (context7 + tailwindcss-mcp-server) → reviewer
```

### Bug 修復
```bash
researcher → tester (context7, 寫測試重現 bug) → coder (context7, 修復) → reviewer (context7)
```

### 重構（TDD）
```bash
/speckit.specify [描述] → tester (context7, 回歸測試) → coder (context7, 重構) → reviewer (context7)
```

### UI 元件開發
```bash
/components → ui-designer → component-architect → coder (context7 + tailwindcss-mcp-server) → reviewer
```

---

## 檔案位置速查

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

## 三大開發原則

1. **文件驅動** - 先寫規格 (Spec-Kit)，再寫程式碼
2. **測試先行** - TDD 確保品質，測試覆蓋率 >80%
3. **持續審查** - 程式碼必須經過 reviewer 才能合併

---

## 記住這些關鍵點

- 🔒 **安全第一** - SQL 注入、XSS、CSRF 防護
- 📝 **註解必備** - 每行程式碼都要有繁體中文註解
- 🏗️ **分層架構** - Controller → Service → Repository
- ✅ **錯誤處理** - 所有可能失敗的操作都要 try-catch
- 🎨 **規範一致** - 嚴格遵守命名規範和程式碼風格
- 🔄 **流程完整** - 使用 Spec-Kit + Agents 確保品質
