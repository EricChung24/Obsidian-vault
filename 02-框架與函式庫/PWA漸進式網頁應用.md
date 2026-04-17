---
tags: [PWA, Service Worker, 離線快取, Web App Manifest]
created: 2026-04-16
---

# PWA 漸進式網頁應用

> PWA 讓網頁擁有 App 的體驗：可安裝到桌面、離線使用、推播通知。

← [[前端工程師]] | 相關：[[Vite(主流)]] | [[Next.js]]

---

## 什麼是 PWA？

PWA（Progressive Web App）透過三個關鍵技術，讓網頁接近原生 App 體驗：

1. **Web App Manifest** — 讓網頁可安裝到主畫面
2. **Service Worker** — 攔截網路請求、實現離線快取
3. **HTTPS** — 安全傳輸（Service Worker 強制要求）

```
用戶第一次訪問
    ↓
瀏覽器安裝 Service Worker
    ↓
快取靜態資源（HTML/CSS/JS/圖片）
    ↓
之後訪問：直接從快取回應（不需網路）
```

---

## Web App Manifest

```json
// public/manifest.json
{
  "name": "我的應用程式",
  "short_name": "MyApp",
  "description": "一個超棒的 PWA",
  "start_url": "/",
  "display": "standalone",       // standalone = 無瀏覽器 UI（像 App）
  "background_color": "#ffffff",
  "theme_color": "#3b82f6",
  "orientation": "portrait",
  "icons": [
    {
      "src": "/icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "/icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ],
  "screenshots": [
    {
      "src": "/screenshots/desktop.png",
      "sizes": "1280x720",
      "type": "image/png",
      "form_factor": "wide"
    }
  ]
}
```

```html
<!-- index.html 引入 -->
<link rel="manifest" href="/manifest.json">
<meta name="theme-color" content="#3b82f6">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<link rel="apple-touch-icon" href="/icons/icon-192.png">
```

---

## Service Worker 核心概念

```
主執行緒（你的應用）
      ↕ postMessage
Service Worker（背景執行緒）
      ↕ 攔截
  網路請求（fetch events）
      ↕
  快取（Cache API）/ 網路
```

### 生命週期

```js
// main.js — 註冊 Service Worker
if ('serviceWorker' in navigator) {
  window.addEventListener('load', async () => {
    try {
      const registration = await navigator.serviceWorker.register('/sw.js');
      console.log('SW 已註冊:', registration.scope);
    } catch (error) {
      console.error('SW 註冊失敗:', error);
    }
  });
}
```

```js
// sw.js — Service Worker 檔案
const CACHE_NAME = 'my-app-v1';
const STATIC_ASSETS = [
  '/',
  '/index.html',
  '/assets/index.css',
  '/assets/index.js',
];

// 1. install — 安裝時快取靜態資源
self.addEventListener('install', (event) => {
  event.waitUntil(
    caches.open(CACHE_NAME).then(cache => cache.addAll(STATIC_ASSETS))
  );
  self.skipWaiting(); // 立即啟用，不等舊 SW 卸載
});

// 2. activate — 清理舊快取
self.addEventListener('activate', (event) => {
  event.waitUntil(
    caches.keys().then(keys =>
      Promise.all(
        keys
          .filter(key => key !== CACHE_NAME)
          .map(key => caches.delete(key))
      )
    )
  );
  self.clients.claim(); // 立即接管所有頁面
});

// 3. fetch — 攔截請求
self.addEventListener('fetch', (event) => {
  event.respondWith(
    // Cache First 策略：先找快取，快取沒有再走網路
    caches.match(event.request).then(cached => {
      if (cached) return cached;
      return fetch(event.request).then(response => {
        // 動態快取新請求
        const copy = response.clone();
        caches.open(CACHE_NAME).then(cache => cache.put(event.request, copy));
        return response;
      });
    })
  );
});
```

---

## 快取策略

```js
// 1. Cache First（靜態資源：CSS/JS/圖片）
// → 快取有就用快取，沒有再請求網路
async function cacheFirst(request) {
  const cached = await caches.match(request);
  return cached ?? fetch(request);
}

// 2. Network First（API 請求，需要最新資料）
// → 先請求網路，失敗才用快取
async function networkFirst(request) {
  try {
    const response = await fetch(request);
    const cache = await caches.open(CACHE_NAME);
    cache.put(request, response.clone());
    return response;
  } catch {
    return caches.match(request);
  }
}

// 3. Stale While Revalidate（平衡：回傳快取但背景更新）
// → 先回快取（快速），同時背景重新請求更新快取
async function staleWhileRevalidate(request) {
  const cached = await caches.match(request);
  const fetchPromise = fetch(request).then(response => {
    caches.open(CACHE_NAME).then(cache => cache.put(request, response.clone()));
    return response;
  });
  return cached ?? fetchPromise;
}
```

---

## Vite PWA 插件（推薦方式）

```bash
pnpm add -D vite-plugin-pwa
```

```js
// vite.config.ts
import { VitePWA } from 'vite-plugin-pwa';

export default {
  plugins: [
    VitePWA({
      registerType: 'autoUpdate',  // 有新版本自動更新
      workbox: {
        // 快取 API 回應
        runtimeCaching: [
          {
            urlPattern: /^https:\/\/api\.example\.com\//,
            handler: 'NetworkFirst',
            options: {
              cacheName: 'api-cache',
              expiration: { maxEntries: 50, maxAgeSeconds: 60 * 60 * 24 }, // 1天
            },
          },
          {
            urlPattern: /\.(png|jpg|svg|ico)$/,
            handler: 'CacheFirst',
            options: {
              cacheName: 'images-cache',
              expiration: { maxEntries: 100, maxAgeSeconds: 60 * 60 * 24 * 30 }, // 30天
            },
          },
        ],
      },
      manifest: {
        name: '我的 PWA',
        short_name: 'MyPWA',
        theme_color: '#3b82f6',
        icons: [
          { src: '/icon-192.png', sizes: '192x192', type: 'image/png' },
          { src: '/icon-512.png', sizes: '512x512', type: 'image/png' },
        ],
      },
    }),
  ],
};
```

---

## 安裝提示（Add to Home Screen）

```js
// 監聽瀏覽器的安裝提示事件
let deferredPrompt;

window.addEventListener('beforeinstallprompt', (e) => {
  e.preventDefault();  // 阻止自動彈出
  deferredPrompt = e;
  showInstallButton();  // 顯示自訂安裝按鈕
});

// 使用者點擊安裝按鈕
document.getElementById('installBtn').addEventListener('click', async () => {
  if (!deferredPrompt) return;
  deferredPrompt.prompt();  // 顯示瀏覽器安裝對話框
  const { outcome } = await deferredPrompt.userChoice;
  console.log('使用者選擇:', outcome);  // 'accepted' 或 'dismissed'
  deferredPrompt = null;
});

// 安裝後隱藏按鈕
window.addEventListener('appinstalled', () => {
  hideInstallButton();
});
```

---

## 推播通知

```js
// 1. 請求通知權限
async function requestNotificationPermission() {
  const permission = await Notification.requestPermission();
  if (permission !== 'granted') return false;
  return true;
}

// 2. 訂閱推播（需要後端的 VAPID 公鑰）
async function subscribeToNotifications() {
  const registration = await navigator.serviceWorker.ready;
  const subscription = await registration.pushManager.subscribe({
    userVisibleOnly: true,
    applicationServerKey: 'YOUR_VAPID_PUBLIC_KEY',
  });
  // 將 subscription 傳送給後端儲存
  await fetch('/api/push-subscribe', {
    method: 'POST',
    body: JSON.stringify(subscription),
    headers: { 'Content-Type': 'application/json' },
  });
}

// 3. Service Worker 接收推播
self.addEventListener('push', (event) => {
  const data = event.data?.json() ?? {};
  event.waitUntil(
    self.registration.showNotification(data.title || '通知', {
      body: data.body,
      icon: '/icon-192.png',
      badge: '/badge.png',
      data: { url: data.url },
    })
  );
});

// 4. 點擊通知開啟頁面
self.addEventListener('notificationclick', (event) => {
  event.notification.close();
  event.waitUntil(clients.openWindow(event.notification.data.url));
});
```

---

## 偵測網路狀態

```js
// 監聽線上/離線狀態
window.addEventListener('online',  () => showOnlineMessage());
window.addEventListener('offline', () => showOfflineMessage());

// 當前狀態
navigator.onLine  // true / false

// React Hook
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);

  useEffect(() => {
    const setOnline  = () => setIsOnline(true);
    const setOffline = () => setIsOnline(false);
    window.addEventListener('online',  setOnline);
    window.addEventListener('offline', setOffline);
    return () => {
      window.removeEventListener('online',  setOnline);
      window.removeEventListener('offline', setOffline);
    };
  }, []);

  return isOnline;
}

// 使用
function App() {
  const isOnline = useOnlineStatus();
  return isOnline ? <MainContent /> : <OfflinePage />;
}
```

---

## PWA 偵錯工具

- **Chrome DevTools → Application**：查看 Manifest、Service Workers、Cache Storage
- **Lighthouse**：一鍵分析 PWA 分數（可安裝性、效能、SEO）
- `chrome://inspect/#service-workers`：查看所有 SW 狀態

---

## 相關連結

- [[Vite(主流)]] — vite-plugin-pwa 設定
- [[Next.js]] — next-pwa 整合
- [[Web Worker與效能]] — 背景執行緒
- [[HTTP協議]] — 快取控制標頭
