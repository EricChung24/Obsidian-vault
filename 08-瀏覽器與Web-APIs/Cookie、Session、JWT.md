---
tags: [認證, Cookie, Session, JWT, 登入流程]
created: 2026-04-17
---

# Cookie、Session、JWT

> 這三個常常一起出現，但它們不是同一層的東西。Cookie 是瀏覽器儲存機制，Session 是伺服器端登入狀態，JWT 是一種 token 格式。

---

## 先講結論

- Cookie：瀏覽器幫你保存小型資料，會自動跟請求一起送出
- Session：伺服器記住「這個使用者現在是誰」
- JWT：把使用者資訊或聲明包進 token，由前後端傳遞驗證

所以不要把它們當成互相替代的三選一，它們常常是搭配關係。

---

## Cookie 是什麼

Cookie 是瀏覽器儲存在本機的小資料。

常見用途：

- 保存登入憑證
- 保存偏好設定
- 保存追蹤資訊

瀏覽器在符合網域與路徑規則時，會自動把 Cookie 帶到請求裡。

### 常見屬性

- `HttpOnly`
- `Secure`
- `SameSite`
- `Expires` / `Max-Age`

### 重要觀念

如果一個 token 放在 HttpOnly Cookie，JavaScript 就無法直接讀取，安全性通常比放 `localStorage` 高。

---

## Session 是什麼

Session 是伺服器端保存的登入狀態。

典型流程：

1. 使用者登入成功
2. 伺服器建立一筆 session 資料
3. 伺服器把 session id 放到 Cookie 回給瀏覽器
4. 瀏覽器之後每次請求都自動帶上這個 Cookie
5. 伺服器收到後，根據 session id 找到對應使用者

這種做法的重點是：

- 真正的登入狀態存在伺服器
- 前端通常只持有 session id

---

## JWT 是什麼

JWT 全名是 JSON Web Token。

它是一種 token 格式，通常由三段組成：

1. Header
2. Payload
3. Signature

看起來像這樣：

```txt
xxxxx.yyyyy.zzzzz
```

常見流程：

1. 使用者登入成功
2. 後端簽發 JWT
3. 前端保存 token
4. 前端之後請求 API 時帶上 token
5. 後端驗證簽章與內容

---

## Session 與 JWT 差異

| 項目 | Session | JWT |
|------|------|------|
| 狀態保存位置 | 伺服器端 | Token 本身 |
| 後端是否需查資料 | 通常要 | 可直接驗證 token |
| 登出控制 | 較容易 | 較麻煩 |
| 適合情境 | 傳統網站、後端集中管理 | API、多服務架構 |

---

## 前端最常見的兩種做法

### 1. Session + Cookie

適合：

- 傳統後端渲染網站
- 同網域站台
- 想把 token 暴露面積降到最低

特點：

- 前端較省事
- 瀏覽器自動帶 Cookie
- 要注意 [[CORS]] 與 CSRF

### 2. JWT + Authorization Header

適合：

- 前後端分離
- App 與 Web 共用 API
- 多服務驗證

常見寫法：

```http
Authorization: Bearer <token>
```

---

## 安全風險

### Cookie / Session 常見風險

- CSRF
- Session fixation
- Cookie 被不安全傳輸攔截

對策：

- 設 `HttpOnly`
- 設 `Secure`
- 設 `SameSite`
- 關鍵操作加 CSRF 防護

### JWT 常見風險

- token 被 XSS 竊取
- 過期時間設太長
- 登出後 token 仍可使用

對策：

- 避免把敏感 token 暴露給前端 JS
- access token 短效
- 搭配 refresh token 與撤銷機制

---

## 常見誤區

### JWT 比 Session 更安全

不一定。安全與否主要看儲存方式、傳輸方式、過期策略與防護設計。

### token 一定要放 localStorage

不一定。很多系統會把 token 放在 HttpOnly Cookie 來降低 XSS 風險。

### Cookie 就等於 Session

不對。Cookie 只是載具，Session 才是伺服器上的登入狀態。

---

## 實務怎麼選

如果是一般 Web 專案，優先考慮：

- 後端集中管理：Session + Cookie
- 前後端分離 API、多平台共用：JWT

如果你沒有很明確的多服務需求，Session 其實往往更直覺、更好控管。

---

## 面試怎麼回答

> Cookie 是瀏覽器儲存資料的機制，Session 是伺服器保存登入狀態的方式，JWT 是一種 token 格式。實務上 Session 常搭配 Cookie，JWT 常搭配 Authorization header。它們不是互斥關係，要看系統架構與安全需求選擇。

---

## 相關連結

- [[CORS]]
- [[HTTP協議]]
- [[Storage APIs]]
- [[前端資安]]
