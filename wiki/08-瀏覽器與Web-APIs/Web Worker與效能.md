---
tags: [瀏覽器, Web Worker, 效能, 多執行緒]
created: 2026-04-16
---

# Web Worker 與效能 APIs

> 瀏覽器效能相關 API：Web Worker、Service Worker、Performance API。

← [[前端工程師]]

---

## Web Worker

將耗時任務移到背景執行緒，不阻塞主執行緒。

```ts
// main.ts
const worker = new Worker(new URL('./worker.ts', import.meta.url), {
  type: 'module'
});

worker.postMessage({ type: 'SORT', data: largeArray });

worker.onmessage = (e) => {
  const { type, result } = e.data;
  if (type === 'SORT_DONE') {
    updateUI(result);
  }
};

// worker.ts（在獨立執行緒運行）
self.onmessage = (e) => {
  const { type, data } = e.data;

  if (type === 'SORT') {
    const sorted = data.sort((a, b) => a - b);
    self.postMessage({ type: 'SORT_DONE', result: sorted });
  }
};
```

### Comlink（簡化 Worker 通訊）

```ts
// worker.ts
import { expose } from 'comlink';

expose({
  async processImage(imageData: ImageData): Promise<ImageData> {
    // 複雜圖片處理...
    return processedData;
  }
});

// main.ts
import { wrap } from 'comlink';

const worker = new Worker('./worker.ts', { type: 'module' });
const api = wrap(worker);

const result = await api.processImage(imageData); // 像呼叫普通函式！
```

---

## Service Worker

PWA 的核心，攔截網路請求，實現離線快取。

```ts
// sw.ts
const CACHE_NAME = 'v1';
const STATIC_ASSETS = ['/', '/index.html', '/app.css', '/app.js'];

// 安裝 — 快取靜態資源
self.addEventListener('install', (e) => {
  e.waitUntil(
    caches.open(CACHE_NAME).then(cache => cache.addAll(STATIC_ASSETS))
  );
});

// 攔截請求 — Cache First 策略
self.addEventListener('fetch', (e) => {
  e.respondWith(
    caches.match(e.request).then(cached => {
      if (cached) return cached;

      return fetch(e.request).then(response => {
        const clone = response.clone();
        caches.open(CACHE_NAME).then(cache => cache.put(e.request, clone));
        return response;
      });
    })
  );
});
```

---

## Performance API

```ts
// 測量程式碼執行時間
performance.mark('start');
doHeavyWork();
performance.mark('end');
performance.measure('heavy-work', 'start', 'end');

const [measure] = performance.getEntriesByName('heavy-work');
console.log(`耗時: ${measure.duration}ms`);

// 取得各類型效能資料
performance.getEntriesByType('navigation')[0];  // 頁面載入
performance.getEntriesByType('resource');        // 資源載入
performance.getEntriesByType('paint');           // FP, FCP

// PerformanceObserver — 持續監控
const observer = new PerformanceObserver((list) => {
  list.getEntries().forEach(entry => {
    console.log(entry.entryType, entry.name, entry.startTime);
  });
});

observer.observe({ entryTypes: ['largest-contentful-paint', 'layout-shift'] });
```

---

## 相關連結

- [[Core Web Vitals]] — 效能指標
- [[渲染優化]] — 主執行緒優化
