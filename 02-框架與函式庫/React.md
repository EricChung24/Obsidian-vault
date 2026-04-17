---
tags: [框架, React, Hooks]
created: 2026-04-16
---

# React

> React 是用來建立 UI 的函式型元件框架，核心重點是元件化、單向資料流與宣告式渲染。
> [[前端工程師]]

---

## 核心概念

### JSX

```jsx
const element = (
  <div className="card" onClick={handleClick}>
    <h2>{title}</h2>
    {isLoggedIn && <UserMenu />}
    {items.map(item => (
      <li key={item.id}>{item.name}</li>
    ))}
  </div>
);
```

JSX 不是字串模板，而是讓你用比較接近 HTML 的寫法描述 UI。

---

## Hooks

### useState

```jsx
const [count, setCount] = useState(0);
const [user, setUser] = useState(null);

setCount(prev => prev + 1);
setUser(prev => ({ ...prev, name: 'Eric' }));
```

### useEffect

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch(`/api/users/${userId}`, { signal: controller.signal })
    .then(res => res.json())
    .then(setUser);

  return () => controller.abort();
}, [userId]);
```

`useEffect` 只該拿來處理真正的副作用，例如：

- API 請求
- 訂閱與解除訂閱
- DOM / timer 操作

### useRef

```jsx
const inputRef = useRef(null);
const focusInput = () => inputRef.current?.focus();
```

### useMemo 與 useCallback

```jsx
const filteredList = useMemo(
  () => items.filter(i => i.active),
  [items]
);

const handleDelete = useCallback((id) => {
  setItems(prev => prev.filter(i => i.id !== id));
}, []);
```

### useContext

```jsx
const ThemeContext = createContext('light');

function Button() {
  const theme = useContext(ThemeContext);
  return <button className={theme}>按鈕</button>;
}
```

### useReducer

```jsx
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return state + 1;
    case 'decrement':
      return state - 1;
    case 'reset':
      return action.payload;
    default:
      return state;
  }
}

const [count, dispatch] = useReducer(reducer, 0);
```

---

## React 效能觀念

```jsx
const Card = React.memo(function Card({ title, onClick }) {
  return <div onClick={onClick}>{title}</div>;
});
```

React 常見效能關鍵字：

- `React.memo`
- `useMemo`
- `useCallback`
- `useTransition`
- `useDeferredValue`

但不要把它們當預設模板。先搞清楚什麼情況會 render，再決定要不要優化。

---

## 自訂 Hook

```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);

    fetch(url, { signal: controller.signal })
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then(setData)
      .catch(err => {
        if (err.name !== 'AbortError') setError(err);
      })
      .finally(() => setLoading(false));

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}
```

---

## React 18

| 項目 | 說明 |
|------|------|
| `createRoot` | 新版 root API |
| `useTransition` | 讓更新可延後處理 |
| `useDeferredValue` | 延後高成本值的更新 |
| `useId` | 產生穩定 ID |
| Automatic Batching | 自動批次更新 |

---

## 延伸閱讀

- [[React渲染與重渲染]]
- [[React效能優化]]

---

## 相關連結

- [[Next.js]]
- [[Redux]]
- [[Zustand]]
- [[TypeScript]]
- [[React渲染與重渲染]]
- [[React效能優化]]
- [[React狀態設計實戰]]
- [[React元件拆分實戰]]
- [[元件設計模式]]
- [[渲染優化]]
