---
tags: [測試, E2E, Playwright]
created: 2026-04-16
---

# E2E測試

> E2E 測試會從使用者視角驗證整條流程是否正常，例如登入、下單、送表單、頁面跳轉。

> [[前端工程師]]

---

## 它在測什麼

不是測單一函式，而是測：

- 頁面互動
- API 串接後的流程
- 路由跳轉
- 真實使用者路徑

---

## Playwright 範例

```ts
import { test, expect } from '@playwright/test';

test('user can log in', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="password"]', 'password123');
  await page.click('button[type="submit"]');

  await expect(page).toHaveURL('/dashboard');
});
```

---

## 適合測什麼

- 登入流程
- 結帳流程
- 關鍵頁面導航
- 權限流程

---

## 不適合測什麼

- 純函式邏輯
- 很細的小 UI 狀態
- 每個元件的所有分支細節

這些通常更適合單元測試。

---

## 常見誤區

### E2E 越多越好

不對。E2E 比較慢、比較貴，應該只保護關鍵流程。

### 用 E2E 補所有測試缺口

這會讓整體測試變慢又難維護。

---

## 相關連結

- [[單元測試]]
- [[測試策略]]
- [[React Router]]
