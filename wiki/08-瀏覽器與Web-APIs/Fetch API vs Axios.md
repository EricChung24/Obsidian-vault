---
tags: [HTTP, Fetch, Axios, API, 比較]
created: 2026-04-19
---

# Fetch API vs Axios

> Fetch 是瀏覽器原生 API，Axios 是功能更豐富的封裝函式庫。選擇哪個取決於你需要多少開箱即用的功能。

→ [[前端工程師]]

---

## Quick Difference

Fetch 是瀏覽器內建，零依賴，但需要手動處理許多細節（錯誤判斷、JSON 解析、請求攔截）。
Axios 是第三方套件，多一個依賴，但大幅減少重複程式碼，適合需要統一請求處理的中大型專案。

---

## Fetch API

瀏覽器原生，不需要安裝任何套件。

```ts
// 基本 GET
const res = await fetch('https://api.example.com/user/1');
if (!res.ok) throw new Error(`HTTP error: ${res.status}`);
const data = await res.json();

// POST with JSON
const res = await fetch('https://api.example.com/user', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ name: 'Eric' }),
});
```

**注意**：Fetch **不會** 把 4xx / 5xx 視為錯誤（`res.ok` 是 false，但不會 throw），
需要手動判斷 `res.ok`。

---

## Axios

```ts
import axios from 'axios';

// 基本 GET（自動 JSON 解析）
const { data } = await axios.get('https://api.example.com/user/1');

// POST（不需手動設定 Content-Type）
const { data } = await axios.post('https://api.example.com/user', { name: 'Eric' });
```

Axios **會**把 4xx / 5xx 自動拋出錯誤。

---

## 功能對比表

| 功能 | Fetch | Axios |
|------|-------|-------|
| 瀏覽器支援 | 原生（IE 不支援） | 需安裝套件 |
| Node.js 支援 | Node 18+ 原生 | 支援 |
| 自動 JSON 解析 | 需手動 `.json()` | 自動 |
| 4xx/5xx 拋錯 | 不會（需手動判斷 `res.ok`） | 自動拋錯 |
| 請求攔截器 | 無（需自己封裝） | 內建 `interceptors` |
| 回應攔截器 | 無 | 內建 |
| 取消請求 | AbortController | 內建 `cancelToken` / AbortController |
| 上傳進度 | 不支援 | 支援 `onUploadProgress` |
| 自動轉換請求體 | 需手動 `JSON.stringify` | 自動 |
| 設定 baseURL | 需自己封裝 | 內建 `axios.create()` |

---

## 何時選 Fetch

- 小型專案或 side project，不想多一個依賴
- 已有自訂的 API client 封裝層
- Next.js 專案（原生 Fetch 有額外的快取控制 API）

## 何時選 Axios

- 中大型專案，需要統一的請求 / 回應攔截（如：自動帶 token、統一 error toast）
- 需要支援舊瀏覽器（Axios 有 polyfill）
- 需要上傳進度條
- 團隊成員對 Axios API 更熟悉

---

## 常見的 Axios 實例封裝

```ts
// api/client.ts
import axios from 'axios';

const client = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  timeout: 10000,
});

// 請求攔截：自動帶 JWT token
client.interceptors.request.use(config => {
  const token = localStorage.getItem('token');
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

// 回應攔截：統一錯誤處理
client.interceptors.response.use(
  res => res,
  err => {
    if (err.response?.status === 401) {
      // 跳轉登入頁
    }
    return Promise.reject(err);
  }
);

export default client;
```

---

## Common Mistakes

- **誤以為 Fetch 會在 4xx 時 throw**：不會，必須自己加 `if (!res.ok) throw ...`
- **Axios 的錯誤物件結構不同**：錯誤資訊在 `err.response.data`，不是 `err.message`
- **忘記設定 `Content-Type`（Fetch）**：POST JSON 時必須手動加 header

---

## Related Concepts
- [[Fetch API]]
- [[Axios]]
- [[HTTP協議]]
- [[API錯誤處理實戰]]
- [[Cookie、Session、JWT]]
