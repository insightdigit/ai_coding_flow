# 開發工作流程

> **核心理念:** 文件驅動開發 (Spec-Kit) + 專業化協作 (Agents) + 自動化生成 (MCP)

---

## 工作流程三大支柱

| 工具 | 用途 | 適用場景 |
|-----|------|---------|
| **Spec-Kit** | 規格化文件與任務管理 | 所有新功能開發、重構、架構設計 |
| **Agents** | 專業化開發代理 | 程式碼實作、測試、審查 |
| **MCP 工具** | 程式碼生成與規範檢查 | 所有程式碼生成（自動搭配使用） |

---

## Spec-Kit 指令完整列表

| 指令 | 用途 | 使用時機 |
|-----|------|---------|
| `/speckit.constitution` | 建立/更新專案憲章 | 專案初始化或更新原則 |
| `/speckit.specify [描述]` | 建立功能規格 | 開始新功能開發 |
| `/speckit.clarify` | 澄清模糊需求 | 規格不明確時 |
| `/speckit.plan` | 執行技術規劃 | 規格確定後的技術設計 |
| `/speckit.tasks` | 生成任務清單 | 設計完成後準備實作 |
| `/speckit.analyze` | 分析文件一致性 | 任務生成後驗證完整性 |
| `/speckit.implement` | 執行實作計劃 | 開始編碼 |
| `/speckit.checklist` | 生成檢查清單 | 驗證功能完整性 |

---

## Agents 完整列表

**如何調用 Agents:**
```bash
Task agent: [agent名稱]
Prompt: "[具體任務描述]"
```

| 分類 | Agent | 用途 |
|-----|-------|------|
| **核心開發** | `researcher` | 分析程式碼結構、研究依賴關係 |
| | `coder` | 實作程式碼、遵循規範 |
| | `tester` | 編寫測試、驗證品質（支援 TDD） |
| | `reviewer` | 程式碼審查、檢查規範 |
| **模板生成** | `base-template-generator` | 建立基礎模板和樣板程式碼 |
| **UI/UX** | `ui-designer` | UI 設計、設計系統 |
| | `component-architect` | 元件架構設計 |
| **架構** | `system-architect` | 系統架構設計、技術選型 |
| **協調** | `pr-manager` | Pull Request 管理 |

---

## MCP 工具

| 工具 | 用途 | 使用時機 |
|-----|------|---------|
| `context7` | 確保最新 Laravel/PHP 語法 | **所有程式碼生成**（Controller/Service/Repository/Model） |
| `tailwindcss-mcp-server` | Tailwind CSS 規範與元件生成 | **僅前端程式碼**（Blade/HTML/CSS） |

**MCP 使用規則：**
- ✅ 寫 PHP 程式碼時：**必須**使用 `context7`
- ✅ 寫 Blade 模板時：**必須同時**使用 `context7` + `tailwindcss-mcp-server`
- ✅ 寫純 CSS/Tailwind 時：**必須**使用 `tailwindcss-mcp-server`
- ❌ 只寫文件、規格時：不需要使用 MCP 工具

---

## 完整開發流程

```
📋 階段 0: 專案初始化
   └─ /speckit.constitution

📝 階段 1: 規格定義
   └─ /speckit.specify [功能描述]
   └─ /speckit.clarify (可選)

🔍 階段 2: 需求研究
   └─ Task agent: researcher (分析現有程式碼和需求)

🏗️ 階段 3: 技術規劃
   └─ /speckit.plan
   └─ Task agent: system-architect (系統架構設計) [可選，並行]
   └─ Task agent: ui-designer (UI/UX 設計) [可選，並行]
   └─ Task agent: component-architect (元件架構) [可選，並行]

📋 階段 4: 任務分解
   └─ /speckit.tasks
   └─ /speckit.analyze

💻 階段 5: 開發實作（TDD 方式）
   5.1 建立基礎架構
       └─ Task agent: base-template-generator
       └─ 使用 MCP: context7 (生成 PHP 類別)

   5.2 測試先行（TDD）
       └─ Task agent: tester (先寫測試)
       └─ 使用 MCP: context7 (PHPUnit 測試)

   5.3 後端開發
       └─ Task agent: coder (實作功能讓測試通過)
       └─ 使用 MCP: context7 (Controller/Service/Repository/Model)

   5.4 前端開發
       🚨 強制步驟：
       └─ 第一步：使用 /components 查看可用共用組件
       └─ 第二步：確認使用哪些共用組件
       └─ 第三步：Task agent: coder (開發時優先使用共用組件)
       └─ 使用 MCP: context7 + tailwindcss-mcp-server (Blade 模板)

   或使用 /speckit.implement (自動處理所有步驟)

✅ 階段 6: 品質保證
   └─ Task agent: reviewer (程式碼審查)
       使用 MCP: context7 (審查 PHP/Blade)
   └─ /speckit.checklist (可選)

🚀 階段 7: 發布管理
   └─ Task agent: pr-manager
```

---

## 常見場景快速指令

### 場景 A: 新功能開發（完整流程 + TDD）
```bash
/speckit.specify 我想開發一個產品管理系統
/speckit.plan
/speckit.tasks

Task agent: tester (使用 MCP: context7)
Prompt: "為 ProductService 建立 PHPUnit 測試"

Task agent: coder (使用 MCP: context7)
Prompt: "實作 ProductService 讓測試通過"

/components  # 查看可用組件

Task agent: coder (使用 MCP: context7 + tailwindcss-mcp-server)
Prompt: "建立產品列表 Blade 頁面，優先使用共用組件"
```

### 場景 B: Bug 修復（快速流程）
```bash
Task agent: researcher
Prompt: "分析產品列表載入緩慢問題"

Task agent: coder (使用 MCP: context7)
Prompt: "優化資料庫查詢，解決 N+1 問題"

Task agent: tester (使用 MCP: context7)
Prompt: "驗證效能改善"

Task agent: reviewer (使用 MCP: context7)
Prompt: "審查程式碼變更"
```

### 場景 C: 重構（中等流程 + TDD）
```bash
/speckit.specify 重構產品模組，將邏輯移到 Service 層
/speckit.plan
/speckit.tasks

Task agent: tester (使用 MCP: context7)
Prompt: "建立重構前的回歸測試（確保功能不變）"

Task agent: coder (使用 MCP: context7)
Prompt: "執行重構，保持測試通過"

Task agent: reviewer (使用 MCP: context7)
Prompt: "審查重構後的程式碼品質"
```

---

## 工作流程決策樹

```
開始新任務
    │
    ├─ 是新功能嗎？
    │   ├─ 是 → 使用完整 Spec-Kit 流程
    │   └─ 否 ↓
    │
    ├─ 需要架構設計嗎？
    │   ├─ 是 → /speckit.plan + system-architect
    │   └─ 否 ↓
    │
    ├─ 是重構或改版嗎？
    │   ├─ 是 → /speckit.specify + /speckit.plan + /speckit.tasks
    │   └─ 否 ↓
    │
    └─ 是簡單修復嗎？
        └─ 是 → 直接使用 Agents (researcher → coder → tester → reviewer)
```
