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

## 響應式設計

Tailwind 用 `sm:` `md:` `lg:` `xl:` 前綴做 breakpoint，預設是 mobile-first。

```html
<!-- 手機全寬，md 以上改為一半 -->
<div class="w-full md:w-1/2">...</div>

<!-- 手機單欄，lg 以上三欄 -->
<div class="grid grid-cols-1 lg:grid-cols-3 gap-4">...</div>
```

| 前綴 | 最小寬度 |
|------|---------|
| `sm:` | 640px |
| `md:` | 768px |
| `lg:` | 1024px |
| `xl:` | 1280px |
| `2xl:` | 1536px |

---

## 狀態前綴

```html
<!-- hover、focus、active -->
<button class="bg-blue-600 hover:bg-blue-700 focus:ring-2 focus:ring-blue-400 active:scale-95">
  按鈕
</button>

<!-- disabled 狀態 -->
<button class="opacity-50 cursor-not-allowed" disabled>
  不可用
</button>

<!-- group hover：滑到父元素時改變子元素 -->
<div class="group">
  <p class="text-gray-500 group-hover:text-black">文字</p>
</div>
```

---

## Dark Mode

```html
<!-- 在 html 標籤加 class="dark"，子元素的 dark: 前綴就會生效 -->
<div class="bg-white text-black dark:bg-gray-900 dark:text-white">
  內容
</div>
```

`tailwind.config.js` 設定：

```js
module.exports = {
  darkMode: 'class', // 或 'media'（跟著系統設定）
};
```

---

## 客製化設定

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: {
          50:  '#eff6ff',
          500: '#3b82f6',
          900: '#1e3a8a',
        },
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
      spacing: {
        '128': '32rem',
      },
    },
  },
};
```

之後就能用 `bg-brand-500`、`font-sans`、`w-128` 這些自訂的 class。

---

## 常見疑問

### class 很長會不會很亂

會變長，但優點是樣式來源很直接，回頭看元件時不用一直跳去別的檔案。可以用 `clsx` 或 `cva` 管理有條件切換的 class。

### Tailwind 會不會讓 HTML 很醜

這是取捨問題。它換來的是更快的開發與更穩定的樣式邊界。

---

## 常見誤區

### 不知道可以用 `@apply` 抽取重複 class

當 class 組合重複出現，可以用 `@apply` 提取到 CSS：

```css
.btn-primary {
  @apply px-4 py-2 rounded-lg bg-blue-600 text-white hover:bg-blue-700;
}
```

但不要過度用，這樣又回到傳統 CSS 的問題了。

### 忘記 purge / content 設定

Tailwind v3 預設會自動 tree-shake 未使用的 class，但要確保 `content` 設定正確掃到你的檔案。

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
