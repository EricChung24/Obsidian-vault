---
tags: [前端基礎, HTML, 語意化]
created: 2026-04-16
---

# HTML

> HTML 是網頁內容的骨架。對前端工程師來說，HTML 不只是把元素排出來，還牽涉語意化、可存取性、SEO 與表單結構。

> [[前端工程師]]

---

## HTML 在做什麼

HTML 主要負責：

- 描述內容結構
- 提供語意
- 讓瀏覽器、搜尋引擎、輔助工具能理解頁面

---

## 語意化標籤

```html
<header>
  <nav>
    <a href="/">首頁</a>
  </nav>
</header>

<main>
  <article>
    <h1>文章標題</h1>
    <section>內容段落</section>
  </article>

  <aside>側邊資訊</aside>
</main>

<footer>版權資訊</footer>
```

比起全部都用 `div`，語意化標籤的好處是：

- 結構更清楚
- SEO 更好
- 無障礙更友善

---

## 常見重要元素

### 標題

- `h1` 到 `h6`
- 一個頁面通常應該有清楚的標題層級

### 連結

```html
<a href="/about">關於我們</a>
```

### 圖片

```html
<img src="/banner.jpg" alt="活動主視覺" />
```

`alt` 很重要，因為它同時關係到：

- 無障礙
- 圖片失敗時的替代文字

---

## 表單基礎

```html
<form>
  <label for="email">Email</label>
  <input id="email" name="email" type="email" />

  <button type="submit">送出</button>
</form>
```

要點：

- `label` 要正確對應 input
- `button` 要寫清楚 `type`
- 表單不是只有視覺，還要考慮鍵盤操作與驗證流程

---

## 常見誤區

### 版面做得出來就代表 HTML 寫對了

不對。畫面能顯示，不代表語意正確。

### 所有區塊都用 `div`

短期看起來快，長期會讓結構與可維護性變差。

---

## 什麼是好的 HTML

- 結構清楚
- 語意正確
- 標題層級合理
- 表單可用
- 圖片與連結資訊完整

---

## 相關連結

- [[CSS]]
- [[SEO優化]]
- [[a11y無障礙設計]]
- [[表單處理]]
