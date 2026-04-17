---
tags: [React, Server State, Cache, TanStack Query]
created: 2026-04-16
---

# React Query

> React Query 專門處理 server state，也就是來自 API 的資料。它幫你處理快取、重新抓取、失效與背景更新。

> [[前端工程師]]

---

## 先分清楚兩種 state

```txt
Client State：UI 狀態、表單狀態、Modal 開關
Server State：API 回來的資料、列表、使用者資訊
```

Client state 常用：

- [[Zustand]]
- [[Redux]]

Server state 很適合交給 React Query。

---

## 基本設定

```jsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60,
      gcTime: 1000 * 60 * 10,
      retry: 1,
    },
  },
});
```

---

## useQuery

```jsx
const { data, isPending, isError, error } = useQuery({
  queryKey: ['user', userId],
  queryFn: () => api.get(`/users/${userId}`),
  enabled: !!userId,
});
```

重點：

- `queryKey` 要穩定
- `queryFn` 要只做抓資料這件事
- `enabled` 可用來控制是否真的發請求

---

## useMutation

```jsx
const queryClient = useQueryClient();

const mutation = useMutation({
  mutationFn: createUser,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ['users'] });
  },
});
```

mutation 的重點不是「送出成功」，而是送出後有沒有正確更新 cache。

---

## 為什麼它重要

如果沒有 React Query，你常常要自己處理：

- loading
- error
- retry
- cache
- refetch
- 畫面資料失效

這些事情一多，畫面會變得很難維護。

---

## 跟快取策略的關係

React Query 很多概念其實就是前端資料快取：

- `staleTime`
- `gcTime`
- `invalidateQueries`
- background refetch

建議搭配 [[前端快取策略]] 一起理解。

---

## 常見誤區

### React Query 是狀態管理

更精確地說，它是 server state 管理，不是拿來取代所有 client state。

### mutation 後畫面會自動全對

不一定。你通常還是要自己設計 invalidation 或 optimistic update。

---

## 相關連結

- [[React]]
- [[Zustand]]
- [[Fetch API]]
- [[前端快取策略]]
- [[React效能優化]]
- [[React狀態設計實戰]]
