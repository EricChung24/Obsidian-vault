# React Server Components

## Summary
React Server Components（RSC）是 React 18+ 的新架構，讓部分組件**在伺服器上執行並渲染**，永遠不發送 JavaScript 到客戶端，大幅降低 bundle 大小並提升效能。

## Plain Explanation
傳統 React：所有組件的 JavaScript 都打包發送給瀏覽器，瀏覽器下載後才能渲染。

Server Components：某些組件只在伺服器執行，結果（HTML/JSON）直接傳給瀏覽器，瀏覽器不需要下載這些組件的 JavaScript。

打個比喻：原本是把食材（JS）寄給客人讓他們自己煮，現在是廚房（伺服器）直接把做好的菜端出來。

## Technical Definition

### 組件類型對比

| 類型 | 執行位置 | 能否使用 useState/useEffect | 能否直接存取 DB | 有無 JS bundle |
|------|---------|--------------------------|--------------|--------------|
| Server Component | 伺服器 | 否 | 是 | 無（0 KB） |
| Client Component | 瀏覽器 | 是 | 否 | 有 |

### 宣告方式（Next.js App Router）
```tsx
// 預設就是 Server Component（不需要特別聲明）
// app/page.tsx
async function Page() {
  // 可以直接呼叫 async，不需要 useEffect
  const data = await fetch('https://api.example.com/data');
  const json = await data.json();
  return <div>{json.title}</div>;
}

// 'use client' 指令 → 變成 Client Component
'use client';
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

## Why It Matters

### 1. 更小的 JavaScript Bundle
Server Components 的程式碼不出現在客戶端 bundle 中。
例如：用 `marked` 解析 Markdown → 這個 250KB 函式庫只存在伺服器，不打包。

### 2. 直接存取後端資源
```tsx
// Server Component 可以直接存取 DB，不需要 API 路由
import { db } from '@/lib/db';

async function UserList() {
  const users = await db.query('SELECT * FROM users'); // 直接！
  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

### 3. 自動程式碼分割
依賴的函式庫只在伺服器載入，不影響客戶端效能。

## Example

### 典型的混合架構
```tsx
// app/dashboard/page.tsx（Server Component）
import { db } from '@/lib/db';
import { LikeButton } from './LikeButton'; // Client Component

async function DashboardPage() {
  // 伺服器端直接查 DB
  const posts = await db.post.findMany();

  return (
    <div>
      {posts.map(post => (
        <div key={post.id}>
          <h2>{post.title}</h2>
          {/* Server Component 可以渲染 Client Component */}
          <LikeButton postId={post.id} initialLikes={post.likes} />
        </div>
      ))}
    </div>
  );
}
```

```tsx
// app/dashboard/LikeButton.tsx（Client Component）
'use client';
import { useState } from 'react';

function LikeButton({ postId, initialLikes }: { postId: string; initialLikes: number }) {
  const [likes, setLikes] = useState(initialLikes);
  return (
    <button onClick={() => setLikes(l => l + 1)}>
      ❤️ {likes}
    </button>
  );
}
```

### Server Actions（表單提交）
```tsx
// Server Component 中直接定義 Server Action
async function ContactForm() {
  async function submitForm(formData: FormData) {
    'use server'; // 標記為 Server Action
    const name = formData.get('name');
    await db.contact.create({ data: { name: String(name) } });
  }

  return (
    <form action={submitForm}>
      <input name="name" type="text" />
      <button type="submit">送出</button>
    </form>
  );
}
```

## Common Mistakes

### 1. 在 Server Component 中使用客戶端 Hook
```tsx
// 錯誤
async function ServerComp() {
  const [state, setState] = useState(0); // 報錯！
}

// 正確：加上 'use client' 或拆出獨立 Client Component
```

### 2. 把 Server Component import 進 Client Component
```tsx
// 錯誤：Client Component 不能直接 import Server Component
'use client';
import ServerComp from './ServerComp'; // 會出錯

// 正確：透過 children prop 傳遞（組合模式）
'use client';
function ClientWrapper({ children }) {
  return <div>{children}</div>;
}

// 在 Server Component 中使用
function Page() {
  return (
    <ClientWrapper>
      <ServerComp /> {/* 這樣可以 */}
    </ClientWrapper>
  );
}
```

### 3. 誤以為 Server Component 每次都重新執行
RSC 的結果可以被 Next.js 快取，受 `cache()` API 和 `fetch` 快取控制。

### 4. 混淆 SSR 與 RSC
| | SSR（傳統） | RSC |
|--|------------|-----|
| HTML 生成位置 | 伺服器 | 伺服器 |
| JS 發送到客戶端 | 是（需要 hydration） | Server Component 不需要 |
| 互動性 | 需要 hydrate | Client Component 負責 |

## RSC 與 Next.js App Router 的關係

Next.js 13+ App Router 是目前最主流的 RSC 實作環境：
- `app/` 目錄下的組件**預設是 Server Component**
- 需要互動性才加 `'use client'`
- `pages/` 目錄（舊 Pages Router）不支援 RSC

## Related Concepts
- [[Next.js]]
- [[Next.js資料取得策略]]
- [[React效能優化]]
- [[React渲染與重渲染]]
- [[React.md]]
- [[程式碼分割]]

## Source Notes
- React 官方 RSC 文件
- Next.js App Router 文件

## Open Questions
- RSC 的 Streaming 與 Suspense 如何搭配？
- React 19 的 Server Actions 與 React 18 有何差異？
