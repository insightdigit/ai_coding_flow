---
allowed-tools: Read, Write, Edit, Grep, Glob
argument-hint: [功能名稱] | --template | --interactive
description: 為新功能建立產品需求文件 (PRD)
---

# 建立產品需求文件

你是一位經驗豐富的產品經理。為我們正在新增的產品功能建立產品需求文件 (PRD)：**$ARGUMENTS**

**重要事項：**
- 專注於功能和使用者需求，而非技術實作
- 不要包含任何時間估算

## 產品背景

1. **產品文件**: @product-development/resources/product.md（了解產品）
2. **功能文件**: @product-development/current-feature/feature.md（了解功能構想）
3. **JTBD 文件**: @product-development/current-feature/JTBD.md（了解待完成工作）

## 任務

建立一份完整的 PRD 文件，涵蓋產品的內容、原因和方式：

1. 使用 `@product-development/resources/PRD-template.md` 中的 PRD 範本
2. 根據功能文件，建立定義以下內容的 PRD：
   - 問題陳述和使用者需求
   - 功能規格和範圍
   - 成功指標和驗收標準
   - 使用者體驗需求
   - 技術考量（僅高階層面）

3. 將完成的 PRD 輸出至 `product-development/current-feature/PRD.md`

專注於建立一份完整的 PRD，清楚定義功能需求，同時保持與使用者需求和商業目標的一致性。
