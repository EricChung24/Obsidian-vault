---
tags: [建置工具, Vite, 前端工程]
created: 2026-04-16
---

# Vite

> Vite 是目前前端專案很主流的建置工具，特色是啟動快、HMR 快、設定相對簡單，特別適合 React、Vue 這類現代專案。

> [[前端工程師]]

---

## 為什麼 Vite 會快

開發模式下，Vite 不會先把整包專案都 bundle 完才啟動。

它的核心思路是：

- 利用瀏覽器原生 ESM
- 只在需要時載入模組
- 更新時只處理變動部分

所以你會感覺：

- dev server 啟動快
- HMR 快
- 配置相對輕量

---

## 基本設定

```ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
      },
    },
  },
});
```

---

## 環境變數

```bash
VITE_API_URL=https://api.example.com
VITE_APP_TITLE=My App
```

在程式中：

```ts
const apiUrl = import.meta.env.VITE_API_URL;
const isDev = import.meta.env.DEV;
const isProd = import.meta.env.PROD;
```

要注意：

- 前端可讀的環境變數要以 `VITE_` 開頭

---

## build 與 chunk

```ts
build: {
  target: 'es2022',
  rollupOptions: {
    output: {
      manualChunks: {
        vendor: ['react', 'react-dom'],
      },
    },
  },
}
```

Vite 底層 build 主要使用 Rollup。

---

## Vite 與 Webpack 差異

### Vite

- 開發體驗更快
- 設定通常更簡單
- 適合現代前端專案

### Webpack

- 生態成熟
- 老專案常見
- 客製化空間大，但設定也較重

---

## 什麼情況適合 Vite

- 新專案
- React / Vue SPA
- 想要較快的開發啟動速度

---

## 相關連結

- [[Webpack(舊專案)]]
- [[套件管理]]
- [[React]]
- [[Vue]]
- [[前端快取策略]]
