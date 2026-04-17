---
tags: [API, 實戰, 錯誤處理]
created: 2026-04-17
---

# API錯誤處理實戰

> 前端接 API 時，最常見的問題不是「打不到 API」，而是錯誤處理策略不一致，導致畫面、提示與重試行為都很混亂。

> [[前端工程師]] | [[Fetch API]]

---

## 先分清楚錯誤類型

實務上至少要區分：

- 網路錯誤
- HTTP 狀態錯誤
- 後端業務錯誤
- 權限錯誤

---

## 基本封裝

```ts
async function request<T>(url: string, options?: RequestInit): Promise<T> {
  const response = await fetch(url, options);

  if (!response.ok) {
    const message = await response.text();
    throw new Error(message || `HTTP ${response.status}`);
  }

  return response.json();
}
```

---

## UI 層該怎麼接

```tsx
try {
  const data = await request<User[]>('/api/users');
  setUsers(data);
} catch (error) {
  setError(error instanceof Error ? error.message : '讀取失敗');
}
```

---

## 常見錯誤對應方式

### 401

- token 過期
- 未登入
- 權限失效

通常要考慮導回登入或清掉 session。

### 403

代表有登入，但沒有權限。

### 422

多半是表單資料錯誤，應該回到欄位或表單層處理。

### 500

通常顯示通用錯誤訊息，同時做錯誤紀錄。

---

## 常見誤區

### 所有錯誤都顯示同一句

這樣使用者不知道問題在哪，你自己也難 debug。

### 只在 API client 處理，不在 UI 處理

API 層與畫面層都要各做自己的事。

---

## 相關連結

- [[Fetch API]]
- [[Axios]]
- [[React Query]]
- [[前端監控]]

