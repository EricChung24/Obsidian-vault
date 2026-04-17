---
tags: [HTTP, CORS, 瀏覽器, API]
created: 2026-04-17
---

# CORS

> CORS 是瀏覽器基於同源政策實作的一套跨來源請求規則。它不是後端框架功能，而是瀏覽器在保護使用者資料時做的限制。

---

## 它是什麼

CORS 全名是 Cross-Origin Resource Sharing。

當前端從一個來源去請求另一個來源的資源時，瀏覽器會檢查這個請求是否符合跨來源規則。

來源通常由這三個組成：

- 協定
- 網域
- Port

只要其中一個不同，就算不同源。

例如：

- `http://localhost:3000`
- `http://localhost:5173`

這兩個只差 port，也算不同源。

---

## 為什麼會有 CORS

因為瀏覽器有同源政策。

同源政策的目的是避免某個網站任意讀取另一個網站的資料，例如：

- 偷拿登入中的使用者資訊
- 假借使用者身份發敏感請求
- 讀取別站 API 回傳內容

CORS 可以理解成：

「預設不給跨來源讀資料，除非伺服器明確說可以。」

---

## 簡單請求與預檢請求

### 簡單請求

如果請求符合某些條件，瀏覽器會直接送出，再看回應標頭是否允許。

常見條件包括：

- 方法是 `GET`、`POST`、`HEAD`
- `Content-Type` 屬於簡單類型

### 預檢請求

如果請求比較敏感，例如：

- 使用 `PUT`、`DELETE`
- 帶自訂 header
- `Content-Type: application/json`

瀏覽器通常會先送一個 `OPTIONS` 請求問伺服器：

「這個正式請求可不可以送？」

這個步驟就是 preflight。

---

## 常見回應標頭

### `Access-Control-Allow-Origin`

指定哪些來源可以存取。

```http
Access-Control-Allow-Origin: https://example.com
```

如果要開放所有來源：

```http
Access-Control-Allow-Origin: *
```

但如果你要帶 cookie 或認證資訊，通常不能搭配 `*`。

### `Access-Control-Allow-Methods`

允許哪些 HTTP 方法。

```http
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
```

### `Access-Control-Allow-Headers`

允許前端送哪些 header。

```http
Access-Control-Allow-Headers: Content-Type, Authorization
```

### `Access-Control-Allow-Credentials`

是否允許攜帶認證資訊，例如 cookie。

```http
Access-Control-Allow-Credentials: true
```

---

## 常見錯誤情境

### 前端以為 API 壞了

很多時候實際上 API 有回應，但瀏覽器因為 CORS 規則直接擋掉，所以你在畫面上只看到錯誤。

### 後端只處理正式請求，沒處理 OPTIONS

結果 preflight 就失敗，正式請求根本不會送出去。

### 帶 cookie 但後端還是用 `*`

如果有 `credentials: 'include'`，後端 `Access-Control-Allow-Origin` 就不能是萬用字元。

---

## 前端怎麼寫

```js
fetch('https://api.example.com/profile', {
  method: 'GET',
  credentials: 'include',
});
```

如果你這樣帶 cookie：

- 前端要加 `credentials: 'include'`
- 後端要回 `Access-Control-Allow-Credentials: true`
- 後端的 `Access-Control-Allow-Origin` 不能是 `*`

---

## 怎麼 debug

1. 先看瀏覽器 console 的錯誤訊息
2. 打開 Network 檢查有沒有 `OPTIONS`
3. 看 response headers 有沒有正確的 `Access-Control-Allow-*`
4. 確認是不是前端送了自訂 header 或 JSON 觸發 preflight
5. 確認 cookie 模式和後端設定是否一致

---

## 常見誤區

### CORS 是前端設定問題

通常不是。真正決定能不能過的是伺服器回應標頭。

### Postman 可以，所以前端也一定可以

不對。Postman 不是瀏覽器，不受瀏覽器同源政策限制。

### 後端 API 可以被 curl 打到，就表示沒問題

也不一定。瀏覽器額外有 CORS 檢查，`curl` 不會幫你驗證這件事。

---

## 面試怎麼回答

> CORS 是瀏覽器為了實作同源政策而加上的跨來源限制。前端能不能跨域讀資料，不是看前端程式寫得對不對，而是看後端有沒有回正確的 `Access-Control-Allow-*` 標頭。如果是複雜請求，瀏覽器還會先送 `OPTIONS` 預檢請求。

---

## 相關連結

- [[HTTP協議]]
- [[網路協定]]
- [[Fetch API]]
- [[Axios]]
- [[Cookie、Session、JWT]]
