---
tags: [狀態管理, Redux, React]
created: 2026-04-16
---

# Redux

> Redux 是前端常見的全域狀態管理工具。現在實務上幾乎都搭配 Redux Toolkit 使用，而不是手刻舊版 Redux boilerplate。

> [[前端工程師]]

---

## 什麼時候需要 Redux

不是每個 React 專案都一定要 Redux。

比較適合的情況：

- 多個頁面共用複雜狀態
- 狀態更新規則明確且可預測
- 團隊需要一致的資料流

如果只是小型專案或局部共享狀態，[[Zustand]] 往往更輕。

---

## Redux Toolkit

現在主流寫法是 Redux Toolkit。

```ts
import { configureStore, createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    increment: state => {
      state.value += 1;
    },
    decrement: state => {
      state.value -= 1;
    },
  },
});

export const { increment, decrement } = counterSlice.actions;

export const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
  },
});
```

---

## 使用方式

```tsx
const count = useSelector(state => state.counter.value);
const dispatch = useDispatch();

<button onClick={() => dispatch(increment())}>{count}</button>;
```

---

## Redux 的優點

- 資料流清楚
- 中大型專案可預測性高
- DevTools 強
- 團隊協作時規範明確

---

## Redux 的代價

- 心智負擔較高
- 寫法相對制式
- 小專案可能顯得過重

---

## 常見誤區

### 所有狀態都塞進 Redux

不對。UI 局部狀態很多時候留在元件內更合理。

### Redux 可以取代 server state 工具

不完全。API 資料快取與失效通常更適合 [[React Query]]。

---

## 相關連結

- [[React]]
- [[Zustand]]
- [[React Query]]
- [[React渲染與重渲染]]
