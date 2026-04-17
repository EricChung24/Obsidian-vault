---
tags: [SEO, Meta Tags, SSR, Core Web Vitals]
created: 2026-04-16
---

# SEO優化

> SEO 的重點不是塞關鍵字，而是讓搜尋引擎能理解、抓取並正確索引你的內容，同時讓使用者真的願意點進來。

> [[前端工程師]] | [[HTML]] | [[Core Web Vitals]]

---

## SEO 在看什麼

可以先拆成這幾塊：

- 可爬取
- 可索引
- 內容品質
- 頁面速度
- 結構化資訊

---

## 基本 meta 設定

```html
<title>產品頁面標題</title>
<meta name="description" content="這頁的摘要描述" />
<link rel="canonical" href="https://example.com/page" />
```

這些不是全部，但通常是基本盤。

---

## Open Graph

社群分享時常用：

```html
<meta property="og:title" content="文章標題" />
<meta property="og:description" content="文章摘要" />
<meta property="og:image" content="https://example.com/og.jpg" />
```

---

## SEO 與前端的關係

前端工程師常直接影響：

- 標題層級
- semantic HTML
- meta tags
- canonical
- 頁面速度
- SSR / SSG

所以 SEO 不只是行銷的事。

---

## 為什麼 Next.js 常被拿來談 SEO

因為它比較容易提供：

- SSR
- SSG
- 良好的頁面結構

這類專案對搜尋引擎更友善。

---

## 常見誤區

### 有 `title` 就叫做做完 SEO

不對。SEO 是整體工程，不是只補幾個 meta。

### 只看關鍵字密度

現在更重要的是內容品質、結構與使用者體驗。

---

## 相關連結

- [[HTML]]
- [[Next.js]]
- [[Core Web Vitals]]
- [[前端快取策略]]
