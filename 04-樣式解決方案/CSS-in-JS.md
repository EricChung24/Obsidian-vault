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

## 基本範例（styled-components）

```tsx
import styled from 'styled-components';

const Button = styled.button<{ $primary?: boolean }>`
  padding: 8px 16px;
  border-radius: 8px;
  background: ${props => (props.$primary ? '#2563eb' : '#e5e7eb')};
  color: ${props => (props.$primary ? '#fff' : '#111827')};
  cursor: pointer;

  &:hover {
    opacity: 0.85;
  }
`;

// 使用
<Button>普通</Button>
<Button $primary>主要</Button>
```

---

## Theme Provider：全域設計 Token

```tsx
import { ThemeProvider } from 'styled-components';

const theme = {
  colors: {
    primary: '#2563eb',
    danger: '#dc2626',
    text: '#111827',
  },
  spacing: {
    sm: '8px',
    md: '16px',
    lg: '24px',
  },
};

// 包在 App 最外層
function App() {
  return (
    <ThemeProvider theme={theme}>
      <YourApp />
    </ThemeProvider>
  );
}

// 在任意元件裡使用 theme
const Card = styled.div`
  padding: ${({ theme }) => theme.spacing.md};
  color: ${({ theme }) => theme.colors.text};
`;
```

---

## Emotion 版本

Emotion 的 API 很像 styled-components，但也支援 `css` prop 寫法：

```tsx
import { css } from '@emotion/react';

const buttonStyle = css`
  padding: 8px 16px;
  background: #2563eb;
  color: white;
`;

<button css={buttonStyle}>按鈕</button>
```

---

## 優點

- 樣式與元件集中，搬移元件不怕忘記帶樣式
- 可讀 props 動態變化，避免複雜的 class 條件邏輯
- 對 React 元件封裝很直覺

---

## 缺點

- 心智模型比純 CSS 重
- 執行時注入樣式有效能成本（每次渲染都要計算）
- React Server Components 環境不相容（Next.js App Router 要特別處理）
- 團隊若沒有一致規範，容易變亂

---

## styled-components vs Emotion

| 面向 | styled-components | Emotion |
|------|------------------|---------|
| 語法 | 只有 tagged template | 支援 `css` prop + template |
| 效能 | 類似 | 略快 |
| SSR | 支援 | 支援 |
| 社群 | 較大 | 略小但活躍 |

---

## 什麼情況適合用

- React 專案
- 元件設計系統（有 ThemeProvider 需求）
- 樣式強烈依賴 props / component state
- 不在 Next.js App Router 的 Server Component 中

---

## 相關連結

- [[React]]
- [[Sass]]
- [[Tailwind CSS]]
