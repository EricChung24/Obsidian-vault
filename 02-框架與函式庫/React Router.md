---
tags: [React, 路由, SPA]
created: 2026-04-16
---

# React Router

> React Router 是 React SPA 最常見的路由方案，負責頁面切換、巢狀路由、動態參數與導航邏輯。

> [[前端工程師]] | [[React]]

---

## 基本觀念

路由的核心問題是：

- 網址對應哪個頁面
- 頁面如何切換
- 參數怎麼帶

---

## 基本用法

```tsx
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

const router = createBrowserRouter([
  {
    path: '/',
    element: <RootLayout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: 'users/:userId', element: <UserPage /> },
    ],
  },
]);

<RouterProvider router={router} />;
```

---

## 常見功能

### 巢狀路由

適合共享 layout。

### 動態參數

像：

```txt
/users/:userId
```

### 導航

常見用：

- `Link`
- `NavLink`
- `useNavigate`

---

## Router 與畫面設計

好的路由設計通常會跟：

- layout 結構
- 權限判斷
- 資料預先載入

一起思考。

---

## 常見誤區

### 路由只是切畫面

不只，它也會影響資料載入、SEO、使用者流程與權限控制。

### 所有專案都該用 React Router

如果你本來就是 [[Next.js]] 專案，就通常不需要它。

---

## 相關連結

- [[React]]
- [[Next.js]]
- [[表單處理]]
- [[前端部署排錯實戰]]
