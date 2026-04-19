---
tags: [Next.js, 實戰, 資料取得]
created: 2026-04-17
---

# Next.js資料取得策略

> Next.js 的資料取得重點不是只有 `fetch`，而是你要先決定這筆資料應該在 server 拿、client 拿，還是預先生成。

> [[前端工程師]] | [[Next.js]]

---

## 先分三種情境

### 1. SEO / 首屏重要資料

優先考慮 server side 取得。

### 2. 高互動資料

通常 client side 比較適合。

### 3. 幾乎不變的頁面資料

適合 SSG / ISR。

---

## App Router 常見做法

### Server Component 直接 fetch

```tsx
async function ProductPage() {
  const products = await fetch('https://api.example.com/products', {
    next: { revalidate: 60 },
  }).then(res => res.json());

  return <ProductList products={products} />;
}
```

### Client Component 處理互動

如果有：

- 搜尋
- 即時篩選
- 表單
- 使用者操作後重抓

通常會放到 client component。

---

## 不同策略怎麼選

### SSR / Server fetch

適合：

- SEO
- 初始內容重要

### Client fetch

適合：

- 互動後才需要資料
- 使用者專屬資料

### ISR

適合：

- 內容不常變
- 又希望有一定更新能力

---

## 常見誤區

### 所有資料都 server fetch 最好

不對。很多高互動資料放 server 反而增加複雜度。

### client fetch 比較差

也不對。它只是不同場景的選擇。

---

## 相關連結

- [[Next.js]]
- [[React Query]]
- [[SEO優化]]
- [[前端快取策略]]
