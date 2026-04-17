---
tags: [HTTP, Axios, API, 請求]
created: 2026-04-16
---

# Axios

> 基於 Promise 的 HTTP 客戶端，支援瀏覽器與 Node.js，功能比 Fetch 更豐富。

← [[前端工程師]]

---

## 安裝

```bash
pnpm add axios
```

---

## 基本用法

```ts
import axios from 'axios';

// GET
const { data } = await axios.get('/api/users');

// POST
const { data: newUser } = await axios.post('/api/users', {
  name: 'Alice',
  email: 'alice@example.com',
});

// PUT — 完整更新
await axios.put(`/api/users/${id}`, updatedUser);

// PATCH — 部分更新
await axios.patch(`/api/users/${id}`, { name: '新名字' });

// DELETE
await axios.delete(`/api/users/${id}`);
```

---

## 帶型別的請求（TypeScript）

```ts
interface User {
  id: number;
  name: string;
  email: string;
}

// 指定回應型別
const { data } = await axios.get<User[]>('/api/users');
//      ↑ data 的型別是 User[]

const { data: user } = await axios.get<User>(`/api/users/${id}`);
```

---

## 建立 Axios 實例（最佳實踐）

```ts
// lib/api.ts
import axios, { AxiosError, InternalAxiosRequestConfig } from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL ?? 'http://localhost:8080',
  timeout: 10_000,                                  // 10 秒超時
  headers: { 'Content-Type': 'application/json' },
});

// ── 請求攔截器 ──────────────────────────────────
api.interceptors.request.use(
  (config: InternalAxiosRequestConfig) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// ── 回應攔截器 ──────────────────────────────────
api.interceptors.response.use(
  (response) => response,  // 2xx：直接回傳
  async (error: AxiosError) => {
    const originalRequest = error.config as InternalAxiosRequestConfig & { _retry?: boolean };

    // Token 過期自動刷新
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;
      try {
        const { data } = await axios.post('/api/auth/refresh', {
          refreshToken: localStorage.getItem('refreshToken'),
        });
        localStorage.setItem('token', data.token);
        originalRequest.headers.Authorization = `Bearer ${data.token}`;
        return api(originalRequest);  // 重試原請求
      } catch {
        // Refresh 也失敗 → 登出
        localStorage.clear();
        window.location.href = '/login';
      }
    }

    // 統一錯誤處理
    const message = (error.response?.data as any)?.message ?? error.message;
    console.error(`[API Error] ${error.config?.url}: ${message}`);

    return Promise.reject(error);
  }
);

export default api;
```

---

## 在元件中使用

```tsx
import api from '@/lib/api';
import { useQuery, useMutation } from '@tanstack/react-query';

// 配合 React Query
function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: () => api.get<User[]>('/users').then(r => r.data),
  });
}

function useCreateUser() {
  return useMutation({
    mutationFn: (newUser: CreateUserDto) =>
      api.post<User>('/users', newUser).then(r => r.data),
  });
}
```

---

## 進階功能

### 請求取消

```ts
// 使用 AbortController（推薦，Axios v1.1+）
const controller = new AbortController();

api.get('/api/search', {
  params: { q: query },
  signal: controller.signal,
});

// 取消
controller.abort();

// React 中的清理
useEffect(() => {
  const controller = new AbortController();

  api.get('/api/data', { signal: controller.signal })
    .then(({ data }) => setData(data))
    .catch(err => {
      if (!axios.isCancel(err)) setError(err);
    });

  return () => controller.abort();
}, []);
```

### 上傳檔案 + 進度追蹤

```ts
async function uploadFile(file: File, onProgress: (pct: number) => void) {
  const formData = new FormData();
  formData.append('file', file);

  const { data } = await api.post('/api/upload', formData, {
    headers: { 'Content-Type': 'multipart/form-data' },
    onUploadProgress: (progressEvent) => {
      const pct = Math.round(
        (progressEvent.loaded * 100) / (progressEvent.total ?? 1)
      );
      onProgress(pct);
    },
  });

  return data;
}

// 使用
<input type="file" onChange={async (e) => {
  const file = e.target.files?.[0];
  if (!file) return;
  await uploadFile(file, (pct) => setProgress(pct));
}} />
```

### 並行請求

```ts
// Promise.all — 全部成功才回傳
const [users, posts, settings] = await Promise.all([
  api.get<User[]>('/users'),
  api.get<Post[]>('/posts'),
  api.get<Settings>('/settings'),
]);

// Promise.allSettled — 全部完成（無論成敗）
const results = await Promise.allSettled([
  api.get('/api/a'),
  api.get('/api/b'),
]);
results.forEach(result => {
  if (result.status === 'fulfilled') {
    console.log(result.value.data);
  } else {
    console.error(result.reason);
  }
});
```

### 錯誤型別判斷

```ts
import axios, { AxiosError } from 'axios';

try {
  await api.post('/api/users', userData);
} catch (error) {
  if (axios.isAxiosError(error)) {
    // Axios 錯誤
    const status = error.response?.status;
    const message = (error.response?.data as any)?.message;

    if (status === 422) {
      // 驗證錯誤
      setFieldErrors((error.response?.data as any).errors);
    } else if (status === 409) {
      // 衝突（如 email 已存在）
      setError('此 Email 已被使用');
    } else {
      setError(`伺服器錯誤: ${message}`);
    }
  } else {
    // 非 Axios 錯誤（程式錯誤）
    throw error;
  }
}
```

---

## Axios vs Fetch 完整比較

| 特性 | Axios | Fetch |
|------|-------|-------|
| 安裝 | 需安裝 | 原生，無需安裝 |
| 自動 JSON 解析 | ✅ | ❌ 需 `.json()` |
| 錯誤拋出（4xx/5xx） | ✅ 自動 | ❌ 需手動判斷 |
| 請求/回應攔截器 | ✅ 內建 | ❌ 需自封裝 |
| 上傳進度追蹤 | ✅ | ❌ |
| 請求取消 | ✅ AbortController | ✅ AbortController |
| Timeout 設定 | ✅ 內建 | ❌ 需搭配 AbortController |
| 自動 Content-Type | ✅ | ❌ |
| Node.js 支援 | ✅ | ✅（v18+） |
| Bundle 大小 | ~14KB | 0KB |

---

## 相關連結

- [[Fetch API]] — 原生替代方案
- [[HTTP協議]] — 狀態碼、方法語意
- [[React Query]] — 搭配快取層
- [[前端資安]] — Token 安全處理
