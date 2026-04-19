# 現代 CSS 新特性

## Summary
2023-2025 年間 CSS 新增了多項強大特性，包含 Container Queries、CSS Nesting、`:has()` 選擇器、Cascade Layers、CSS Grid subgrid 等，大幅減少對 JavaScript 和預處理器的依賴。

## Plain Explanation
以前很多 CSS 做不到的事，都要靠 JavaScript 來彌補。

現在這些新特性讓 CSS 更強大：不用 JS 就能做響應式設計（Container Queries）、不用 Sass 就能嵌套樣式（CSS Nesting）、父元素可以根據子元素狀態改變樣式（`:has()`）。

## Container Queries — 依容器寬度響應式

### 傳統 Media Query 的問題
```css
/* Media Query 看的是 viewport，不是元件本身的容器 */
/* 同一個 Card 在不同容器尺寸下無法分別響應 */
@media (min-width: 768px) {
  .card { flex-direction: row; }
}
```

### Container Queries 解決方案
```css
/* 1. 宣告容器 */
.card-wrapper {
  container-type: inline-size;
  container-name: card; /* 可選 */
}

/* 2. 依容器寬度設定樣式 */
@container card (min-width: 400px) {
  .card {
    display: flex;
    flex-direction: row;
  }
}

@container (max-width: 399px) {
  .card {
    flex-direction: column;
  }
}
```

現在同一個 Card 元件，放在窄欄位就垂直排，放在寬欄位就水平排，不管 viewport 多大。

### Container Query Units
```css
/* cqw = 容器寬度的 1% */
.title {
  font-size: clamp(1rem, 5cqw, 2rem);
}
```

## CSS Nesting — 原生巢狀（不需要 Sass）

```css
/* 以前需要 Sass */
.button {
  color: blue;
}
.button:hover {
  color: darkblue;
}
.button .icon {
  margin-right: 8px;
}

/* 現在原生 CSS 支援 */
.button {
  color: blue;

  &:hover {
    color: darkblue;
  }

  & .icon {
    margin-right: 8px;
  }

  /* 媒體查詢也可以嵌套 */
  @media (max-width: 768px) {
    font-size: 14px;
  }
}
```

瀏覽器支援：Chrome 112+、Firefox 117+、Safari 17.2+（2023 年底全面支援）

## `:has()` — 父元素選擇器

CSS 史上最多人要求的功能。根據子元素的狀態選擇父元素。

```css
/* 卡片裡有圖片時，改變佈局 */
.card:has(img) {
  display: grid;
  grid-template-columns: 200px 1fr;
}

/* 表單輸入有值時，改變 label 樣式 */
.form-group:has(input:not(:placeholder-shown)) label {
  transform: translateY(-20px);
  font-size: 12px;
}

/* 導覽列裡的 active 連結的父 li */
li:has(a.active) {
  background: #e0f0ff;
}

/* 沒有子元素時隱藏 */
.container:not(:has(*)) {
  display: none;
}
```

瀏覽器支援：Chrome 105+、Firefox 121+、Safari 15.4+

## Cascade Layers — 控制樣式優先級

解決大型專案樣式衝突問題，讓你顯式控制哪些樣式優先。

```css
/* 宣告 layer 順序，越後面的優先級越高 */
@layer base, components, utilities;

/* 放入對應 layer */
@layer base {
  h1 { font-size: 2rem; }
  a { color: blue; }
}

@layer components {
  .card { padding: 1rem; }
  .btn { background: blue; color: white; }
}

@layer utilities {
  .mt-4 { margin-top: 1rem; }
  .text-red { color: red; } /* 會覆蓋 components layer 中的 color */
}

/* 未放在 layer 中的樣式優先級最高（在所有 layer 之上） */
.override { color: green; }
```

**使用場景**：引入第三方 CSS 時（Bootstrap、Normalize），把它們放在低優先級 layer，避免覆蓋你的樣式。
```css
@import url('bootstrap.css') layer(third-party);
@layer third-party, base, components;
```

## CSS Subgrid

讓子元素對齊到祖先的 Grid，解決多層 Grid 難以對齊的問題。

```css
/* 外層 Grid */
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: auto auto auto;
  gap: 1rem;
}

/* 卡片跨越多列 */
.card {
  grid-row: span 3;
  display: grid;
  grid-template-rows: subgrid; /* 繼承父 Grid 的列軌道 */
}

/* 現在所有卡片的 header/body/footer 都自動對齊 */
.card-header { /* 第 1 列 */ }
.card-body   { /* 第 2 列 */ }
.card-footer { /* 第 3 列 */ }
```

## `@scope` — 限制樣式作用範圍

```css
/* 只在 .card 內部套用這些樣式 */
@scope (.card) {
  h2 { font-size: 1.25rem; }
  p { color: #666; }
}

/* 排除特定區域 */
@scope (.card) to (.card-footer) {
  /* 只套用到 .card-footer 之前 */
  p { color: #333; }
}
```

取代 CSS Modules 或 BEM 命名的需求。

## `color-mix()` — CSS 內建混色

```css
:root {
  --brand: #3b82f6;
}

.button-light {
  /* 混入 40% 白色 */
  background: color-mix(in srgb, var(--brand), white 40%);
}

.button-dark {
  /* 混入 30% 黑色 */
  background: color-mix(in srgb, var(--brand), black 30%);
}
```

## `light-dark()` — 自動 Dark Mode

```css
:root {
  color-scheme: light dark;
}

.text {
  /* 自動根據系統主題切換 */
  color: light-dark(#333, #eee);
  background: light-dark(white, #1a1a1a);
}
```

## 瀏覽器支援速查

| 特性 | Chrome | Firefox | Safari |
|------|--------|---------|--------|
| Container Queries | 105+ | 110+ | 16+ |
| CSS Nesting | 112+ | 117+ | 17.2+ |
| `:has()` | 105+ | 121+ | 15.4+ |
| Cascade Layers | 99+ | 97+ | 15.4+ |
| Subgrid | 117+ | 71+ | 16+ |
| `color-mix()` | 111+ | 113+ | 16.2+ |
| `light-dark()` | 123+ | 120+ | 17.5+ |

## Common Mistakes

### 1. Container Query 忘記宣告 `container-type`
沒有 `container-type`，`@container` 規則不生效。

### 2. `:has()` 效能
過度使用複雜的 `:has()` 選擇器在大型 DOM 上可能有輕微效能影響，但通常不顯著。

### 3. CSS Nesting 的 `&` 用法
```css
/* & 代表父選擇器，必須在最前面 */
.button {
  &.active { } /* .button.active */
  & + .sibling { } /* .button + .sibling */
  /* 不加 & 的嵌套代表後代 */
  span { } /* .button span */
}
```

## Related Concepts
- [[CSS]]
- [[Sass]]
- [[Tailwind CSS]]
- [[切版技巧]]
- [[常見版型模板]]

## Source Notes
- MDN CSS 文件
- web.dev 的現代 CSS 系列
- caniuse.com 支援查詢
