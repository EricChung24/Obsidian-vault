---
tags: [React, 效能, Optimization, Hooks]
created: 2026-04-17
---

# React效能優化

> React 效能優化的重點不是把所有元件都 memo 化，而是找出真正的瓶頸，再用對應的方法處理。

> **本頁範圍**：如何用 memo、useMemo、useCallback 解決效能問題。→ 了解 re-render 觸發條件見 [[React渲染與重渲染]]；Virtual DOM 底層見 [[渲染優化]]。

---

## 先講結論

React 效能問題最常見的來源：

- 不必要的重渲染
- state 放太高
- 大列表一次渲染太多項目
- 重計算在每次 render 都重跑
- 資料抓取與更新策略不佳

所以優化順序通常是：

1. 先量測
2. 找出是哪個元件慢
3. 再決定要切元件、移 state、memo、lazy load 還是列表虛擬化

---

## 1. 先做對的元件切分

如果一個大元件同時管：

- 表單
- 列表
- Modal
- 篩選條件
- 統計資訊

那任何小變動都可能讓整塊一起重跑。

更穩定的做法是：

- UI 分區塊
- 狀態只放在需要它的範圍
- 讓高頻更新區域與低頻區域分開

---

## 2. state 不要放太高

這是最常見也最值得優先處理的問題。

如果某個輸入框 state 放在頁面最上層，使用者每打一個字，整個頁面都可能重渲染。

應該問自己：

- 這個 state 真的需要全頁知道嗎
- 還是只要某個區塊知道就好

---

## 3. 用 `React.memo` 擋住不必要更新

```jsx
const UserCard = React.memo(function UserCard({ user }) {
  return <div>{user.name}</div>;
});
```

適合情境：

- 元件本身不常變
- props 穩定
- 元件 render 成本偏高

不適合情境：

- 元件很小
- props 每次都是新參考
- 沒有實際瓶頸

---

## 4. `useMemo` 與 `useCallback` 要用在對的地方

### `useMemo`

用來快取計算結果。

```jsx
const sortedList = useMemo(() => {
  return list.slice().sort(sortFn);
}, [list, sortFn]);
```

適合：

- 排序
- 篩選
- 昂貴計算

### `useCallback`

用來穩定函式參考。

```jsx
const handleSubmit = useCallback(() => {
  saveForm(data);
}, [data]);
```

適合：

- 子元件搭配 `React.memo`
- context value 需要穩定

不要把它們當預設模板亂加，否則只是在增加心智負擔。

---

## 5. 大列表要做列表優化

如果一次渲染幾百筆、幾千筆資料，真正的瓶頸常常在列表。

可用做法：

- 分頁
- Infinite Scroll
- 虛擬列表
- 避免每列都做昂貴運算

列表場景比單一元件更容易出現明顯卡頓。

---

## 6. lazy loading 與程式碼分割

不是每個畫面都要一進站就把整包 JS 載完。

常見做法：

```jsx
const SettingsPage = React.lazy(() => import('./SettingsPage'));
```

搭配：

- route-based splitting
- 元件延遲載入
- 圖片延遲載入

這和 [[程式碼分割]]、[[Next.js]]、[[Vite(主流)]] 都有關。

---

## 7. 避免不必要的 context 更新

很多人把大量資料都塞進 context，結果任何小變化都讓整片訂閱元件一起更新。

比較穩定的做法：

- context 只放共享且相對穩定的資料
- 高頻資料考慮局部 state 或獨立 store
- 拆多個 provider，不要全部塞同一包

---

## 8. API 與快取策略也影響 React 效能

如果你每次切頁、focus、mutate 都重新抓資料，畫面就可能一直 loading 與重渲染。

這時應該檢查：

- 是否有用 [[React Query]]
- `staleTime` 合不合理
- mutation 後是否只更新必要資料

效能不只是在元件 render，資料層也會影響體驗。

---

## 常見誤區

### 一看到 render 就先加 memo

錯。先找瓶頸，不要先套公式。

### `useCallback` 一定能優化效能

不一定。如果沒有搭配需要穩定參考的場景，可能只是多做一層管理。

### 所有 state 都集中管理比較好

不一定。集中太高反而容易讓更新範圍變大。

---

## 實務上的優化順序

1. 用 React DevTools Profiler 找慢點
2. 看 state 是否放太高
3. 看是不是列表過大
4. 看 props / context 是否讓更新範圍過廣
5. 再決定是否使用 memo、useMemo、useCallback

---

## 面試怎麼回答

> React 效能優化的核心是減少不必要的重渲染與大範圍更新。實務上我會先用 profiler 找瓶頸，再從元件切分、state 位置、列表優化、memo 類工具與資料快取策略下手，而不是一開始就全面加 `useMemo` 或 `useCallback`。

---

## 相關連結

- [[React]]
- [[React渲染與重渲染]]
- [[React Query]]
- [[程式碼分割]]
- [[渲染優化]]
