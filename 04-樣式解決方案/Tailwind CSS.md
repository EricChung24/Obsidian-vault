---
tags: [CSS, Tailwind, Utility-first]
created: 2026-04-16
---

# Tailwind CSS

> Tailwind CSS 是 utility-first 的樣式框架，重點不是提供一堆現成元件，而是提供大量可組合的 class 來快速完成介面。

> [[前端工程師]]

---

## 它的核心思路

傳統做法常是：

1. 先命名 class
2. 再去 CSS 檔寫樣式

Tailwind 比較像：

1. 直接在 HTML / JSX 上組合 class
2. 用設計系統規則快速拼出 UI

---

## 範例

```html
<button class="px-4 py-2 rounded-lg bg-blue-600 text-white hover:bg-blue-700">
  送出
</button>
```

---

## 為什麼很多團隊喜歡它

- 開發快
- 樣式不容易互相污染
- 設計 token 可集中管理
- 很適合元件化開發

---

## 常見疑問

### class 很長會不會很亂

會變長，但優點是樣式來源很直接，回頭看元件時不用一直跳去別的檔案。

### Tailwind 會不會讓 HTML 很醜

這是取捨問題。它換來的是更快的開發與更穩定的樣式邊界。

---

## 什麼情況適合用

- React / Vue 元件化專案
- 團隊接受 utility-first 思維
- 需要快速迭代 UI

---

## 相關連結

- [[CSS]]
- [[Sass]]
- [[Bootstrap 5]]
