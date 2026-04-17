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
  <input id="email" name="email" type="email" required />

  <!-- input type 決定手機鍵盤與瀏覽器驗證行為 -->
  <input type="tel" />       <!-- 電話數字鍵盤 -->
  <input type="number" />    <!-- 數字輸入 -->
  <input type="date" />      <!-- 日期選擇器 -->
  <input type="password" />  <!-- 密碼遮罩 -->

  <select name="role">
    <option value="admin">管理員</option>
    <option value="user">一般使用者</option>
  </select>

  <button type="submit">送出</button>
  <button type="reset">清除</button>
  <button type="button">不送出表單的按鈕</button>
</form>
```

要點：

- `label` 要正確對應 input（用 `for` 對應 `id`）
- `button` 要寫清楚 `type`，沒寫預設是 `submit`
- 表單不是只有視覺，還要考慮鍵盤操作與驗證流程

---

## Meta Tags（常見重要設定）

```html
<head>
  <!-- 基本 -->
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>頁面標題</title>
  <meta name="description" content="頁面描述，影響搜尋結果摘要" />

  <!-- Open Graph（社群分享時的預覽卡片） -->
  <meta property="og:title" content="頁面標題" />
  <meta property="og:description" content="頁面描述" />
  <meta property="og:image" content="/og-image.jpg" />
  <meta property="og:url" content="https://example.com/page" />

  <!-- 阻止搜尋引擎索引 -->
  <meta name="robots" content="noindex, nofollow" />
</head>
```

`viewport` meta tag 是 RWD 必備，沒有它手機瀏覽器會用桌面版尺寸縮小顯示。

---

## 響應式圖片

```html
<!-- loading="lazy"：延遲載入，提升初始頁面速度 -->
<img src="/photo.jpg" alt="說明文字" loading="lazy" />

<!-- srcset：根據螢幕解析度提供不同圖片 -->
<img
  src="/photo-400.jpg"
  srcset="/photo-400.jpg 400w, /photo-800.jpg 800w, /photo-1200.jpg 1200w"
  sizes="(max-width: 600px) 400px, (max-width: 1024px) 800px, 1200px"
  alt="活動主視覺"
/>
```

---

## data-* 自訂屬性

```html
<!-- HTML 端 -->
<button data-action="delete" data-id="42">刪除</button>

<!-- JS 端讀取 -->
<script>
  btn.addEventListener('click', (e) => {
    const action = e.currentTarget.dataset.action; // 'delete'
    const id = e.currentTarget.dataset.id;         // '42'
  });
</script>
```

`data-*` 是把少量資訊附在 DOM 元素上的標準做法，比起用 `id` 硬塞資料要乾淨很多。

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
