---
tags: [建置工具, Webpack, 打包]
created: 2026-04-16
---

# Webpack

> 功能強大的模組打包器，高度可設定，生態豐富。

← [[前端工程師]]

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

## 相關連結

- [[Vite(主流)]] — 更現代的替代品
- [[程式碼分割]] — 優化策略
