---
tags: [Web API, Fetch, HTTP, API]
created: 2026-04-16
---

# Fetch API

> Fetch API 是瀏覽器原生提供的 HTTP 請求方式。現在大多數前端專案就算最後有包一層 API client，底層概念也常建立在 fetch 之上。

> [[前端工程師]]

---

## 基本用法

```js
const response = await fetch('/api/users');

if (!response.ok) {
  throw new Error(`HTTP ${response.status}`);
}

const users = await response.json();
```

### POST 範例

```js
await fetch('/api/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    name: 'Eric',
  }),
});
```

---

## 錯誤處理

很多人一開始會誤會：

> fetch 遇到 404 / 500 會直接 throw error

其實不會。

fetch 只有在這些情況比較常 reject：

- 網路中斷
- DNS 失敗
- CORS 被擋
- request 被 abort

如果後端回 `404` 或 `500`，你還是要自己檢查 `response.ok`。

---

## 帶 token 或 cookie

### Bearer token

```js
await fetch('/api/profile', {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

### cookie

```js
await fetch('https://api.example.com/me', {
  credentials: 'include',
});
```

如果你要跨站帶 cookie，就要一起理解 [[CORS]]。

---

## 取消請求

```js
const controller = new AbortController();

setTimeout(() => controller.abort(), 5000);

await fetch('/api/data', {
  signal: controller.signal,
});
```

這在 React 中很常用，尤其是：

- 元件 unmount
- 參數快速切換
- 搜尋輸入時避免舊請求回來覆蓋新結果

---

## 封裝 API Client

```js
class ApiClient {
  constructor(baseUrl, getToken) {
    this.baseUrl = baseUrl;
    this.getToken = getToken;
  }

  async request(path, options = {}) {
    const token = this.getToken();

    const response = await fetch(`${this.baseUrl}${path}`, {
      ...options,
      headers: {
        'Content-Type': 'application/json',
        ...(token ? { Authorization: `Bearer ${token}` } : {}),
        ...options.headers,
      },
    });

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }

    return response.json();
  }
}
```

---

## 跟 Axios 的差異

| 項目 | Fetch | Axios |
|------|------|------|
| 是否內建於瀏覽器 | 是 | 否 |
| 4xx / 5xx 是否自動 throw | 否 | 是 |
| 是否要自己 `.json()` | 是 | 否 |
| 攔截器 | 需自行封裝 | 內建 |

---

## 常見誤區

### `fetch` 比 `axios` 落後

不對。`fetch` 很多情況已經足夠，只是你要自己補錯誤處理與封裝。

### `fetch` 失敗一定是後端壞掉

不一定，有時是 [[CORS]]、網路、DNS、或 credentials 設定問題。

---

## 相關連結

- [[JavaScript]]
- [[Async-Await深度指南]]
- [[HTTP協議]]
- [[CORS]]
- [[Storage APIs]]
- [[Cookie、Session、JWT]]
- [[API錯誤處理實戰]]
