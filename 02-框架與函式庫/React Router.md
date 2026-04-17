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

## 動態參數：useParams

```tsx
// 路由設定：path: 'users/:userId'
import { useParams } from 'react-router-dom';

function UserPage() {
  const { userId } = useParams<{ userId: string }>();
  return <div>使用者 ID：{userId}</div>;
}
```

---

## 導航：Link、NavLink、useNavigate

```tsx
import { Link, NavLink, useNavigate } from 'react-router-dom';

// Link：基本頁面跳轉
<Link to="/about">關於我們</Link>

// NavLink：自動偵測是否為當前路由，可加 active 樣式
<NavLink
  to="/dashboard"
  className={({ isActive }) => isActive ? 'text-blue-600 font-bold' : 'text-gray-600'}
>
  儀表板
</NavLink>

// useNavigate：程式碼觸發跳轉（如登入後轉向）
function LoginPage() {
  const navigate = useNavigate();

  const handleLogin = async () => {
    await login();
    navigate('/dashboard');         // 前往
    // navigate(-1);                // 回上一頁
    // navigate('/login', { replace: true }); // 替換歷史紀錄（避免能按回去）
  };
}
```

---

## 巢狀路由與共享 Layout

```tsx
const router = createBrowserRouter([
  {
    path: '/',
    element: <RootLayout />,   // 包含 Navbar、Footer 的外層
    children: [
      { index: true, element: <HomePage /> },
      { path: 'about', element: <AboutPage /> },
      {
        path: 'users',
        element: <UsersLayout />,
        children: [
          { index: true, element: <UserListPage /> },
          { path: ':userId', element: <UserDetailPage /> },
        ],
      },
    ],
  },
]);
```

子路由會渲染在父元件的 `<Outlet />` 位置：

```tsx
import { Outlet } from 'react-router-dom';

function RootLayout() {
  return (
    <>
      <Navbar />
      <main>
        <Outlet />   {/* 子路由內容渲染在這裡 */}
      </main>
      <Footer />
    </>
  );
}
```

---

## 保護路由（Protected Route）

登入驗證後才能進入的頁面：

```tsx
function ProtectedRoute({ children }: { children: React.ReactNode }) {
  const { isLoggedIn } = useAuth();

  if (!isLoggedIn) {
    return <Navigate to="/login" replace />;
  }

  return <>{children}</>;
}

// 用法
{
  path: 'dashboard',
  element: (
    <ProtectedRoute>
      <DashboardPage />
    </ProtectedRoute>
  ),
}
```

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
