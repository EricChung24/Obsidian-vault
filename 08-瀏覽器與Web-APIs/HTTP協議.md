---
tags: [HTTP, 網路, API, REST]
created: 2026-04-16
---

# HTTP協議

> HTTP 是前後端溝通最核心的協議。前端工程師不只要會呼叫 API，還要理解方法、狀態碼、快取與請求流程。

> [[前端工程師]]

---

## 常見 HTTP 方法

| 方法 | 用途 |
|------|------|
| `GET` | 取得資料 |
| `POST` | 新增資料 |
| `PUT` | 完整更新 |
| `PATCH` | 部分更新 |
| `DELETE` | 刪除資料 |
| `HEAD` | 只取 header |
| `OPTIONS` | 詢問伺服器支援能力，常見於 CORS preflight |

---

## 常見狀態碼

### 2xx 成功

- `200 OK`
- `201 Created`
- `204 No Content`

### 3xx 重新導向

- `301 Moved Permanently`
- `302 Found`
- `304 Not Modified`

### 4xx 用戶端錯誤

- `400 Bad Request`
- `401 Unauthorized`
- `403 Forbidden`
- `404 Not Found`
- `409 Conflict`
- `422 Unprocessable Entity`
- `429 Too Many Requests`

### 5xx 伺服器錯誤

- `500 Internal Server Error`
- `502 Bad Gateway`
- `503 Service Unavailable`
- `504 Gateway Timeout`

---

## RESTful API 基本觀念

```txt
GET    /users
POST   /users
GET    /users/:id
PATCH  /users/:id
DELETE /users/:id
```

query string 常用在：

- 分頁
- 排序
- 篩選

例如：

```txt
GET /users?page=1&limit=20&sort=createdAt&order=desc
```

---

## 快取觀念

### 強快取

```http
Cache-Control: max-age=3600
```

### 協商快取

```http
ETag: "abc123"
If-None-Match: "abc123"
```

如果資源沒變，伺服器可回 `304 Not Modified`。

更完整的前端實務可看 [[前端快取策略]]。

---

## CORS 與 OPTIONS

當瀏覽器判定跨來源請求需要預檢時，會先送一個 `OPTIONS` 請求。

所以如果你看到：

- `OPTIONS 204`
- 後面正式 `GET` / `POST`

這通常和 [[CORS]] 有關，不是多送一次 bug。

---

## 前端最常遇到的幾種問題

### 1. API 回 401

通常代表：

- 沒登入
- token 過期
- cookie 沒帶上

### 2. API 回 304

這不是錯誤，通常代表瀏覽器可直接使用快取。

### 3. Postman 可以、瀏覽器不行

通常先檢查 [[CORS]]。

---

## 相關連結

- [[Fetch API]]
- [[Axios]]
- [[CORS]]
- [[前端快取策略]]
- [[Cookie、Session、JWT]]
- [[網路協定]]
