---
tags: [效能, Core Web Vitals, SEO]
created: 2026-04-16
---

# Core Web Vitals

> Google 用來評估使用者體驗的核心指標，影響 SEO 排名。

← [[前端工程師]]

---

## 三大指標

| 指標 | 全名 | 說明 | 好 | 需改善 |
|------|------|------|-----|--------|
| **LCP** | Largest Contentful Paint | 最大內容繪製時間 | ≤ 2.5s | > 4s |
| **INP** | Interaction to Next Paint | 互動回應時間 | ≤ 200ms | > 500ms |
| **CLS** | Cumulative Layout Shift | 累積版面偏移 | ≤ 0.1 | > 0.25 |

---

## LCP 優化（最大內容繪製）

LCP 通常是英雄圖片或大型標題。

```html
<!-- 1. 預載關鍵資源 -->
<link rel="preload" href="/hero.jpg" as="image" fetchpriority="high">

<!-- 2. 圖片加上 priority -->
<!-- Next.js -->
<Image src="/hero.jpg" priority alt="Hero" />

<!-- 3. 避免 lazy loading hero 圖 -->
<img src="/hero.jpg" alt="Hero"> <!-- 不要加 loading="lazy" -->
```

**常見原因與修復：**
- 伺服器回應慢 → CDN、快取
- 阻塞 render 的 JS/CSS → defer、async
- 圖片未壓縮 → WebP/AVIF、適當尺寸

---

## INP 優化（互動回應）

```js
// 長任務拆分（> 50ms 的任務）
function processLargeData(data) {
  // 使用 scheduler.yield() 或 setTimeout 拆分
  const chunks = chunk(data, 100);

  async function processChunks() {
    for (const chunk of chunks) {
      processChunk(chunk);
      await scheduler.yield(); // 讓出主執行緒
    }
  }

  processChunks();
}

// 或使用 requestIdleCallback 處理非緊急任務
requestIdleCallback(() => {
  sendAnalytics(data);
});
```

---

## CLS 優化（版面偏移）

```css
/* 1. 圖片預留空間 */
img {
  aspect-ratio: 16 / 9;
  width: 100%;
}

/* 2. 廣告/嵌入元件預留高度 */
.ad-container {
  min-height: 250px;
}
```

```html
<!-- 3. HTML 中指定寬高 -->
<img src="photo.jpg" width="800" height="450" alt="">
```

---

## 測量工具

| 工具 | 說明 |
|------|------|
| **Lighthouse** | Chrome DevTools 內建，本地測量 |
| **PageSpeed Insights** | Google 線上工具，RUM 數據 |
| **Web Vitals JS** | 程式碼中收集真實用戶數據 |
| **Chrome UX Report** | 真實用戶體驗數據 |

```ts
// 在程式碼中收集 Web Vitals
import { onLCP, onINP, onCLS } from 'web-vitals';

function sendToAnalytics(metric) {
  console.log(metric.name, metric.value);
}

onLCP(sendToAnalytics);
onINP(sendToAnalytics);
onCLS(sendToAnalytics);
```

---

## 相關連結

- [[渲染優化]] — Virtual DOM、Memoization
- [[程式碼分割]] — Lazy Loading
- [[HTML]] — 圖片最佳化
