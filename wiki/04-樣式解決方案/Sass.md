---
tags: [CSS, Sass, 樣式]
created: 2026-04-16
---

# Sass

> Sass 是 CSS 的預處理器，提供變數、巢狀、mixin、partial 等功能，讓樣式管理更有組織。

> [[前端工程師]]

---

## Sass 在解決什麼問題

原生 CSS 已經越來越強，但 Sass 仍然常被使用，因為它可以幫你處理：

- 重複樣式
- 設計 token 管理
- 巢狀結構
- 樣式模組拆分

---

## 基本語法

```scss
$primary: #3b82f6;
$spacing-sm: 0.5rem;
$spacing-md: 1rem;

.card {
  padding: $spacing-md;
  border-radius: 8px;

  &__title {
    color: $primary;
    font-size: 1.25rem;
  }

  &--featured {
    border: 2px solid $primary;
  }
}
```

---

## Mixin

適合抽出可重複使用的樣式邏輯。

```scss
@mixin flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

@mixin responsive($breakpoint) {
  @if $breakpoint == md {
    @media (min-width: 768px) {
      @content;
    }
  }
}

.hero {
  @include flex-center;

  @include responsive(md) {
    min-height: 60vh;
  }
}
```

---

## 樣式拆分

常見拆法：

- `_variables.scss`
- `_mixins.scss`
- `_reset.scss`
- `components/_button.scss`

再由主檔統一匯入。

---

## 什麼時候適合 Sass

- 專案已經在用 Sass
- 團隊習慣變數與 mixin 管理
- 樣式結構較大，需要模組化

---

## 常見誤區

### 巢狀寫越深越好

不對。巢狀過深反而很難維護。

### mixin 可以到處亂用

如果只是單純重複值，很多時候用 CSS 變數更適合。

---

## Sass 與其他方案比較

### Sass

- 傳統且成熟
- 適合大型樣式檔管理

### Tailwind CSS

- 偏 utility-first
- 樣式直接靠 class 組合

### CSS-in-JS

- 樣式與元件綁在一起
- 適合特定 React 生態

---

## 相關連結

- [[CSS]]
- [[Tailwind CSS]]
- [[CSS-in-JS]]
- [[Bootstrap 5]]
