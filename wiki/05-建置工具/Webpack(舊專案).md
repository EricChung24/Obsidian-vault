---
tags: [建置工具, Webpack, 打包]
created: 2026-04-16
---

# Webpack

> 功能強大的模組打包器，高度可設定，生態豐富。新專案通常選 [[Vite(主流)]]，但舊專案維護仍需要理解 Webpack。

← [[前端工程師]]

---

## 核心概念

### Loader vs Plugin

| 面向 | Loader | Plugin |
|------|--------|--------|
| 用途 | 轉換特定檔案格式 | 擴充打包流程 |
| 作用時機 | 模組解析時（個別檔案） | 整個 build 流程 |
| 範例 | `ts-loader`、`css-loader` | `HtmlWebpackPlugin`、`MiniCssExtractPlugin` |

簡單記：**Loader 是「翻譯官」（把非 JS 格式轉換成 JS）；Plugin 是「流程工程師」（修改打包行為）。**

### Mode

```js
module.exports = {
  mode: 'development', // 或 'production'
};
```

- `development`：輸出可讀性高的程式碼，Source Map 完整，速度快
- `production`：自動壓縮、Tree Shaking，輸出最小化

---

## 基本設定

```js
// webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = {
  entry: './src/index.ts',

  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
    clean: true,
  },

  resolve: {
    extensions: ['.ts', '.tsx', '.js'],
    alias: { '@': path.resolve(__dirname, 'src') },
  },

  module: {
    rules: [
      // TypeScript
      { test: /\.tsx?$/, use: 'ts-loader', exclude: /node_modules/ },

      // CSS
      {
        test: /\.css$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'postcss-loader'],
      },

      // 圖片
      {
        test: /\.(png|svg|jpg|webp)$/,
        type: 'asset/resource',
      },
    ],
  },

  plugins: [
    new HtmlWebpackPlugin({ template: './public/index.html' }),
    new MiniCssExtractPlugin({ filename: '[name].[contenthash].css' }),
  ],

  optimization: {
    splitChunks: {
      chunks: 'all',
      cacheGroups: {
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          name: 'vendors',
        },
      },
    },
  },
};
```

---

## 常見誤區

### 每次改 config 都要重啟 server

正確：Webpack dev server 更改 config 後需重啟，但程式碼改動會透過 HMR 自動更新。

### `[contenthash]` 和 `[hash]` 一樣

不一樣。`[contenthash]` 只有在該檔案內容變動時才會改變，更適合長期快取；`[hash]` 是整次 build 的 hash，任何改動都會讓所有檔案 hash 改變。

### 不設定 `splitChunks` 也沒差

沒設定的話第三方套件（node_modules）會跟你的程式碼打包在一起，每次部署都要重新下載所有 vendor 程式碼，快取效益極差。

---

## 相關連結

- [[Vite(主流)]] — 更現代的替代品
- [[程式碼分割]] — 優化策略
- [[套件管理]] — 套件版本管理
