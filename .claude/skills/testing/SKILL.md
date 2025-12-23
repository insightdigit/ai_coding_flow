---
name: testing
description: 測試開發流程。當用戶提到「測試」「test」「TDD」「單元測試」「Feature Test」等關鍵字時自動使用。
---

# 測試開發流程

## 觸發條件

當用戶提問包含以下關鍵字時自動啟用：
- 「測試」「test」「TDD」「單元測試」「Feature Test」

## ⚠️ 重要：TDD 僅限後端測試

**TDD 流程只用於後端邏輯，不測試前端 UI。**

### ✅ TDD 測試範圍（後端）

| 層級 | 測試內容 |
|-----|---------|
| Service | 商業邏輯、計算、流程控制 |
| Repository | 資料查詢、篩選、排序 |
| Model | Accessor、Mutator、Scope、關聯 |
| Controller | HTTP 狀態碼、JSON 回應、重導向 |
| FormRequest | 驗證規則 |
| Policy | 權限判斷 |

### ❌ 不在 TDD 範圍（前端）

- Blade 視圖內容
- HTML/CSS 結構
- JavaScript 行為
- 表單 UI 元素
- 頁面文字顯示

## 執行流程

```
1. Sequential Thinking - 分析測試需求（限後端邏輯）
2. /tdd - 執行 TDD 流程（紅燈→綠燈→重構）
3. coder agent - 實作讓測試通過
4. reviewer agent - 審查
```

## TDD 流程

### 紅燈（Red）
先寫失敗的測試。

### 綠燈（Green）
實作最小程式碼讓測試通過。

### 重構（Refactor）
在測試保護下重構。

## 測試類型

### Unit Test
測試單一類別/方法，mock 依賴。
- Service 方法
- Repository 方法
- Model 方法

### Feature Test
測試完整 HTTP 請求流程，使用 `RefreshDatabase`。
- API 端點回應
- Controller 行為
- 驗證規則

**Feature Test 注意事項：**
- 測試 HTTP 狀態碼、JSON 回應結構
- 避免使用 `assertSee()` 測試頁面文字
- 避免測試 Blade 視圖渲染結果

## 品質要求

- 測試覆蓋率 > 80%（後端邏輯）
- 遵循 AAA 模式（Arrange-Act-Assert）
- 測試名稱清楚描述行為
- 只測試後端邏輯，不測試 UI
