---
tags: [React, render, 效能, re-render]
created: 2026-04-17
---

# React渲染與重渲染

> React 效能問題很多時候不是因為「React 很慢」，而是因為沒有搞清楚什麼情況會 render、哪些變化會讓元件重渲染。

---

## 先講結論

元件會重渲染，通常是因為這幾件事：

- `state` 變了
- `props` 變了
- `context` 變了
- 父元件重渲染，子元件也跟著重新執行

重點不是「重渲染絕對不好」，而是：

- 有沒有造成不必要的計算
- 有沒有讓大型樹一起更新
- 有沒有影響互動流暢度

---

## render 到底是什麼

在 React 裡，render 可以先理解成：

「React 重新執行元件函式，算出新的 UI 描述」

它不等於真的整個 DOM 都重建。

React 會先比對前後差異，再決定真實 DOM 要改哪裡。

所以：

- 元件函式執行了，不代表 DOM 全部重畫
- 但如果元件樹太大，重複 render 還是會有成本

---

## 哪些事情會觸發重渲染

### 1. state 改變

```jsx
const [count, setCount] = useState(0);
```

只要你呼叫 `setCount`，這個元件就會重新執行。

### 2. props 改變

父元件重新傳新的 props，子元件就會重渲染。

### 3. context 改變

只要某個 context value 改了，所有訂閱它的元件都有可能更新。

### 4. 父元件重渲染

就算子元件 props 看起來沒變，子元件本身通常也會重新執行，除非你額外做 memo 類優化。

---

## 常見誤解

### 父元件 render，子元件一定 DOM 全重建

不對。子元件函式可能重新執行，但 React 仍然只更新真正變動的 DOM。

### 只要有 re-render 就是效能問題

不對。小型元件正常 render 通常沒問題，過度優化反而讓程式難維護。

### 用了 `useMemo`、`useCallback` 就一定變快

不一定。這些工具本身也有成本，如果只是很小的運算或簡單元件，未必值得。

---

## 實際例子

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <Child />
    </div>
  );
}

function Child() {
  console.log('Child render');
  return <div>child</div>;
}
```

當 `count` 改變時：

- `Parent` 會重渲染
- `Child` 也會跟著重新執行

這就是為什麼元件切分不只是 UI 問題，也和效能有關。

---

## React 中常見的重渲染來源

### inline object / array

```jsx
<List style={{ color: 'red' }} />
```

每次 render 都會產生新的物件參考。

### inline function

```jsx
<Button onClick={() => doSomething()} />
```

每次 render 都會建立新函式。

這不一定有問題，但如果子元件有搭配 `React.memo`，就可能讓 memo 失效。

### context value 每次都變

如果 `Provider` 傳進去的 value 每次 render 都是新物件，整片訂閱者都可能一起更新。

---

## 怎麼 debug

1. 先用 React DevTools 看哪些元件一直 render
2. 確認是 `state`、`props` 還是 `context` 在變
3. 找出是不是父元件更新把整棵樹一起帶著跑
4. 再決定要不要切元件、移 state、加 memo

不要一開始就亂塞 `useMemo` 或 `useCallback`。

---

## 實務上的穩定做法

- 把 state 放在真正需要它的最小範圍
- 不要讓高頻更新 state 掛在太高層
- 大列表搭配 key 與列表優化
- context 不要塞太多高頻變動資料
- 真的有瓶頸再考慮 `React.memo`

---

## 面試怎麼回答

> React 重渲染通常來自 state、props、context 改變，或父元件更新。render 不等於 DOM 全重建，React 會做 diff。實務上要關注的是不必要的大範圍更新，而不是把每一次 re-render 都當成錯誤。

---

## 相關連結

- [[React]]
- [[React效能優化]]
- [[React Hooks模式]]
- [[渲染優化]]
- [[Event Loop]]
