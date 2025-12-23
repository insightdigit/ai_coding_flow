---
name: new-feature
description: 新功能開發流程。當用戶提到「新功能」「新增」「開發」「實作」「建立」等關鍵字時自動使用。執行 BDD → TDD → Code → Review 流程。
---

# 新功能開發流程

## 觸發條件

當用戶提問包含以下關鍵字時自動啟用：
- 「新功能」「新增」「開發」「實作」「建立」

## 執行流程

```
1. Sequential Thinking - 分析需求
2. /bdd - 定義行為（Given/When/Then）
3. /tdd - 先寫測試（紅燈→綠燈→重構）
4. coder agent - 實作程式碼
5. reviewer agent - 審查
```

## 步驟說明

### 1. Sequential Thinking

分析需求：
- 功能目標：要解決什麼問題？
- 使用者角色：誰會使用？
- 核心流程：主要操作流程？
- 資料模型：需要哪些資料表？

### 2. BDD 行為定義

執行 `/bdd` 定義功能行為。

### 3. TDD 測試先行

執行 `/tdd`：紅燈→綠燈→重構。

### 4. 實作

使用 `coder agent`，遵循三層架構：Controller → Service → Repository。

### 5. 審查

使用 `reviewer agent` 審查程式碼品質。

## 品質要求

- 所有測試必須通過
- 程式碼必須符合專案規範
- 必須經過 reviewer 審查
