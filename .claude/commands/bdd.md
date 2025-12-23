---
description: 使用 BDD (Behavior-Driven Development) 撰寫行為驅動測試，以 Gherkin 語法描述功能。
---

## User Input

```text
$ARGUMENTS
```

You **MUST** use the user input to understand what behavior to describe and test.

## Goal

使用 BDD 方法撰寫測試，以使用者行為角度描述功能需求。

**BDD 流程：**
1. **Discovery** - 討論需求，找出行為
2. **Formulation** - 用 Gherkin 語法撰寫 Feature 檔案
3. **Automation** - 實作 Step Definitions

## Execution Steps

### 1. 分析需求

```bash
/bdd 使用者登入功能
/bdd 購物車結帳流程
/bdd 產品搜尋功能
```

### 2. 安裝 Behat

```bash
composer require behat/behat behat/mink behat/mink-extension --dev

# 初始化
vendor/bin/behat --init
```

結構：
```
features/
├── bootstrap/
│   └── FeatureContext.php
├── login.feature
└── cart.feature
```

### 3. 撰寫 Feature 檔案

**Gherkin 語法：**

```gherkin
# language: zh-TW
功能: 使用者登入
  作為一個訪客
  我想要登入系統
  以便存取個人資料

  場景: 使用有效憑證登入
    假設 我在登入頁面
    當 我輸入帳號 "test@example.com"
    而且 我輸入密碼 "password"
    而且 我點擊登入按鈕
    那麼 我應該看到 "歡迎回來"
    而且 我應該在儀表板頁面

  場景: 使用無效密碼登入失敗
    假設 我在登入頁面
    當 我輸入帳號 "test@example.com"
    而且 我輸入密碼 "wrong-password"
    而且 我點擊登入按鈕
    那麼 我應該看到錯誤訊息 "帳號或密碼錯誤"
    而且 我應該還在登入頁面

  場景大綱: 驗證必填欄位
    假設 我在登入頁面
    當 我輸入帳號 "<email>"
    而且 我輸入密碼 "<password>"
    而且 我點擊登入按鈕
    那麼 我應該看到錯誤訊息 "<error>"

    例子:
      | email              | password   | error          |
      |                    | password   | 請輸入電子信箱 |
      | test@example.com   |            | 請輸入密碼     |
      | invalid-email      | password   | 電子信箱格式錯誤 |
```

### 4. 實作 Step Definitions

```php
<?php
// features/bootstrap/FeatureContext.php

use Behat\Behat\Context\Context;
use Behat\MinkExtension\Context\MinkContext;

class FeatureContext extends MinkContext implements Context
{
    /**
     * @Given 我在登入頁面
     */
    public function 我在登入頁面(): void
    {
        $this->visit('/login');
    }

    /**
     * @When 我輸入帳號 :email
     */
    public function 我輸入帳號(string $email): void
    {
        $this->fillField('email', $email);
    }

    /**
     * @When 我輸入密碼 :password
     */
    public function 我輸入密碼(string $password): void
    {
        $this->fillField('password', $password);
    }

    /**
     * @When 我點擊登入按鈕
     */
    public function 我點擊登入按鈕(): void
    {
        $this->pressButton('登入');
    }

    /**
     * @Then 我應該看到 :text
     */
    public function 我應該看到(string $text): void
    {
        $this->assertPageContainsText($text);
    }

    /**
     * @Then 我應該在儀表板頁面
     */
    public function 我應該在儀表板頁面(): void
    {
        $this->assertPageAddress('/dashboard');
    }

    /**
     * @Then 我應該看到錯誤訊息 :message
     */
    public function 我應該看到錯誤訊息(string $message): void
    {
        $this->assertElementContainsText('.alert-error', $message);
    }
}
```

### 5. Gherkin 關鍵字對照

| 英文 | 中文 | 用途 |
|-----|------|------|
| Feature | 功能 | 描述功能 |
| Scenario | 場景 | 單一測試案例 |
| Scenario Outline | 場景大綱 | 參數化測試 |
| Given | 假設 | 前置條件 |
| When | 當 | 執行動作 |
| Then | 那麼 | 預期結果 |
| And | 而且 | 連接多個步驟 |
| But | 但是 | 否定條件 |
| Examples | 例子 | 場景大綱的資料 |

### 6. behat.yml 設定

```yaml
default:
  suites:
    default:
      contexts:
        - FeatureContext
  extensions:
    Behat\MinkExtension:
      base_url: http://localhost:8000
      sessions:
        default:
          goutte: ~
```

### 7. 執行測試

```bash
# 執行所有 Feature
vendor/bin/behat

# 執行特定 Feature
vendor/bin/behat features/login.feature

# 執行特定場景
vendor/bin/behat features/login.feature:10

# 顯示步驟定義
vendor/bin/behat -dl
```

---

## Output Format

```markdown
## BDD 測試

**功能**: [功能名稱]
**檔案**: `features/xxx.feature`

---

### Feature 檔案

```gherkin
[Gherkin 內容]
```

---

### Step Definitions

```php
[FeatureContext 程式碼]
```

---

### 執行

vendor/bin/behat features/xxx.feature
```

---

## Best Practices

- **以使用者角度撰寫**: 描述行為而非實作
- **保持簡潔**: 每個場景專注一個行為
- **可重用步驟**: Step Definitions 設計成可重用
- **中文撰寫**: Feature 檔案用中文，貼近業務語言
- **先寫 Feature**: 與 PM/QA 討論後再實作

## Context

$ARGUMENTS
