---
tags: [Web API, Storage, Cookie, IndexedDB]
created: 2026-04-16
---

# Storage APIs

> 前端資料不只存在 state。瀏覽器本身也提供多種儲存機制，每一種的容量、生命週期與安全性都不同。

> [[前端工程師]]

---

## 常見儲存方式比較

| 類型 | 容量 | 是否持久 | 是否可由 JS 直接讀取 | 常見用途 |
|------|------|------|------|------|
| Cookie | 小 | 可 | 通常可，但 `HttpOnly` 不行 | 登入、追蹤、偏好設定 |
| sessionStorage | 中 | 否，分頁關閉即消失 | 可 | 單次分頁資料 |
| localStorage | 中 | 是 | 可 | 偏好設定、草稿 |
| IndexedDB | 大 | 是 | 可 | 離線資料、大量資料 |
| Cache API | 大 | 是 | 可 | PWA 資源快取 |

---

## localStorage

```js
localStorage.setItem('theme', 'dark');

const theme = localStorage.getItem('theme');

localStorage.removeItem('theme');
```

如果要存物件，記得自己做 `JSON.stringify` / `JSON.parse`。

---

## sessionStorage

`sessionStorage` 和 `localStorage` API 很像，但資料跟著單一分頁生命週期走。

適合：

- 單次流程
- 分頁級別暫存
- 不希望關掉頁面後還保留的資料

---

## Cookie

```js
document.cookie = 'theme=dark; max-age=86400; path=/; SameSite=Lax';
```

Cookie 的重點不是 API，而是安全屬性：

- `HttpOnly`
- `Secure`
- `SameSite`

如果要搞懂 Cookie 跟登入驗證的關係，直接看 [[Cookie、Session、JWT]]。

---

## IndexedDB

IndexedDB 適合大量資料或離線資料，不適合只存一個 token 這種簡單場景。

常見用途：

- 離線表單
- 大量列表快取
- 本地資料庫

---

## 怎麼選

### 偏好設定

可用 `localStorage`

### 分頁內暫存

可用 `sessionStorage`

### 登入相關

通常看你的架構，很多情況會用 Cookie；不要直接把它等同於「一定比別的更安全」。

### 大量離線資料

優先考慮 IndexedDB

---

## 常見誤區

### token 一定要放 localStorage

不一定。很多系統反而會放進 `HttpOnly Cookie` 降低 XSS 風險。

### Cookie 就等於 Session

不對。Cookie 是載具，Session 是伺服器端狀態。

---

## 相關連結

- [[Cookie、Session、JWT]]
- [[Fetch API]]
- [[CORS]]
- [[前端資安]]
