---
tags: [CSS, CSS-in-JS, React]
created: 2026-04-16
---

# CSS-in-JS

> CSS-in-JS 是把樣式寫進 JavaScript / TypeScript 裡，讓樣式能更直接跟元件邏輯綁在一起。

> [[前端工程師]]

---

## 核心概念

它的重點不是「把 CSS 改寫成 JS」，而是：

- 樣式跟元件放在一起
- 樣式可以吃 props
- 作用域比較集中

---

## 常見工具

- styled-components
- Emotion

---

## 基本範例

```tsx
import styled from 'styled-components';

const Button = styled.button<{ $primary?: boolean }>`
  padding: 8px 16px;
  border-radius: 8px;
  background: ${props => (props.$primary ? '#2563eb' : '#e5e7eb')};
  color: ${props => (props.$primary ? '#fff' : '#111827')};
`;
```

---

## 優點

- 樣式與元件集中
- 可讀 props 動態變化
- 對 React 元件封裝很直覺

---

## 缺點

- 心智模型比純 CSS 重
- 某些方案有執行時成本
- 團隊若沒有一致規範，容易變亂

---

## 什麼情況適合用

- React 專案
- 元件設計系統
- 樣式強烈依賴 props / component state

---

## 相關連結

- [[React]]
- [[Sass]]
- [[Tailwind CSS]]
