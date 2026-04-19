---
tags: [React, 實戰, 狀態設計]
created: 2026-04-17
---

# React狀態設計實戰

> React 的狀態設計重點不是「把資料存起來」，而是決定哪些狀態該存在、該放哪裡、怎麼流動。

> [[前端工程師]] | [[React]]

---

## 先問三件事

每次準備新增 state 前，先問：

1. 這個值是使用者輸入，還是可推導結果？
2. 這個值只有一個元件用，還是多個地方共用？
3. 這個值是 UI state，還是 server state？

如果答不清楚，後面通常會越寫越亂。

---

## 典型錯誤

### 把 derived state 當成獨立 state

```tsx
const [users, setUsers] = useState<User[]>([]);
const [activeUsers, setActiveUsers] = useState<User[]>([]);
```

如果 `activeUsers` 可以從 `users` 算出來，就不該再存一次。

應改成：

```tsx
const activeUsers = users.filter(user => user.active);
```

---

## state 放哪裡

### 放在元件內

適合：

- 單一區塊 UI 狀態
- Modal 開關
- input 值

### 往上提升

適合：

- 多個子元件共用
- 兄弟元件需要同步

### 放進 store

適合：

- 跨頁共享
- 使用者資訊
- 權限
- 全域設定

---

## UI state 與 server state 分開

這是實戰很重要的一點。

### UI state

- tab
- modal
- filter input

### server state

- users list
- order detail
- profile data

server state 很多時候更適合交給 [[React Query]]，不要全部硬塞 [[Redux]] 或 [[Zustand]]。

---

## 實際拆法範例

假設你有一個用戶列表頁：

- 搜尋字串：元件內 state
- 分頁資訊：元件內或 route query
- API 列表資料：React Query
- 使用者登入資訊：全域 store

這樣邏輯會比「全部集中到一個大 store」乾淨很多。

---

## 常見誤區

### 所有 state 都集中才專業

不對。集中太多只會讓更新範圍變大。

### 先建 store 再說

很多頁面其實根本不需要全域 store。

---

## 相關連結

- [[React]]
- [[React渲染與重渲染]]
- [[React效能優化]]
- [[React Query]]
- [[Zustand]]

