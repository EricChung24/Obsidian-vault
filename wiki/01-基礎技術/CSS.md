---
tags: [前端基礎, CSS, 樣式]
created: 2026-04-16
---

# CSS

> CSS 負責網頁的外觀與排版。前端工程師不只要會把畫面做出來，還要理解選擇器、盒模型、排版系統與樣式優先級。

> [[前端工程師]]

---

## 為什麼重要

CSS 掌控畫面的外觀、版型與互動視覺。不理解盒模型、選擇器優先級和 Flexbox/Grid 排版系統，樣式就會頻繁失控，也很難維護。

切版能力是面試中最常被忽略卻最能快速區分工程師實力的技能之一。

---

## 盒模型

每個元素都可以理解成一個盒子：

- content
- padding
- border
- margin

```css
.card {
  width: 300px;
  padding: 16px;
  border: 1px solid #ddd;
  margin-bottom: 24px;
}
```

---

## 選擇器與優先級

常見優先級順序：

```txt
!important > inline style > id > class / attribute / pseudo-class > element
```

不要一有衝突就先上 `!important`，先理解為什麼樣式被蓋掉。

---

## Flexbox

```css
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
}
```

Flexbox 很適合：

- 一維排版
- 水平或垂直對齊
- 元件內部布局

---

## Grid

```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
}
```

Grid 更適合：

- 二維布局
- 整頁區塊排列
- 較複雜版型

---

## 常見實務觀念

### spacing

不要亂塞數字，最好有一致的 spacing 規則。

### typography

字級、行高、字重要有階層。

### responsive

不要只做桌面版，要考慮不同螢幕寬度。

---

## 常見誤區

### 只會靠 margin 硬推版

短期能做出來，但長期難維護。

### 樣式壞掉就先加 `!important`

通常是在掩蓋結構或選擇器問題。

---

## 相關連結

- [[HTML]]
- [[CSS動畫]]
- [[切版技巧]]
- [[Sass]]
- [[Tailwind CSS]]
