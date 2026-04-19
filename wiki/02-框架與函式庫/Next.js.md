---
tags: [框架, Next.js, SSR, React]
created: 2026-04-16
---

# Next.js

> Next.js 是建立在 [[React]] 之上的全端框架，提供路由、SSR、SSG、資料取得與部署整合能力。

> [[前端工程師]]

---

## 為什麼要用 Next.js

如果只用 React，你通常還要自己處理：

- 路由
- SSR / SSG
- API 層
- 檔案結構
- 部署策略

Next.js 就是在這些事情上提供約定與整合。

---

## App Router

Next.js 13 之後主流是 App Router。

常見結構：

```txt
app/
  layout.tsx
  page.tsx
  loading.tsx
  error.tsx
  not-found.tsx
  blog/
    page.tsx
    [slug]/
      page.tsx
```

---

## Server Component 與 Client Component

### Server Component

- 預設就是 Server Component
- 可以直接 `await fetch`
- 不能使用 `useState`、`useEffect`

### Client Component

如果你要互動、事件、瀏覽器 API，就要加：

```tsx
'use client';
```

---

## 常見渲染模式

### SSR

每次請求時在伺服器產生 HTML。

### SSG

建置時先產生好靜態頁面。

### ISR

靜態頁面可在固定時間後重新驗證更新。

---

## 資料取得

```tsx
async function ProductList() {
  const products = await fetch('https://api.example.com/products', {
    next: { revalidate: 60 },
  }).then(res => res.json());

  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>{product.name}</li>
      ))}
    </ul>
  );
}
```

---

## 什麼時候適合用 Next.js

- 需要 SEO
- 需要 SSR / SSG
- 需要檔案式路由
- 想用 React 但不想從零搭整套工程

---

## 常見誤區

### Next.js 一定比 React SPA 好

不一定，要看專案需求。純後台系統不一定需要 SSR。

### Server Component 可以取代所有 client 邏輯

不行。互動型 UI 還是需要 Client Component。

---

## 相關連結

- [[React]]
- [[React效能優化]]
- [[Next.js資料取得策略]]
- [[程式碼分割]]
- [[SEO優化]]
- [[Vite(主流)]]
