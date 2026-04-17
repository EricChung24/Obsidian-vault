---
tags: [狀態管理, Zustand, React]
created: 2026-04-16
---

# Zustand

> Zustand 是一個輕量的 React 狀態管理工具，特色是 API 簡單、樣板少、上手快。

> [[前端工程師]]

---

## 基本用法

```ts
import { create } from 'zustand';

const useCounterStore = create(set => ({
  count: 0,
  increase: () => set(state => ({ count: state.count + 1 })),
  reset: () => set({ count: 0 }),
}));
```

使用時：

```tsx
function Counter() {
  const count = useCounterStore(state => state.count);
  const increase = useCounterStore(state => state.increase);

  return <button onClick={increase}>{count}</button>;
}
```

---

## 為什麼很多 React 專案喜歡它

- 不需要很多 boilerplate
- 不用包一堆 provider
- selector 寫法直覺
- 小到中型專案很順手

---

## persist

如果想把狀態存進 localStorage，可以搭配 middleware。

```ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

const useSettingsStore = create(
  persist(
    set => ({
      theme: 'light',
      setTheme: theme => set({ theme }),
    }),
    {
      name: 'settings-storage',
    }
  )
);
```

---

## selector 的重要性

建議不要整包 store 都拿出來用。

比較好的寫法：

```ts
const count = useCounterStore(state => state.count);
```

這樣比較不容易造成不必要的重渲染。

---

## Zustand 與 Redux 怎麼選

### Zustand

- 輕量
- 開發快
- 小到中型專案很適合

### Redux

- 規範明確
- DevTools 與生態成熟
- 大型專案與多人協作更穩

---

## 相關連結

- [[React]]
- [[Redux]]
- [[React渲染與重渲染]]
- [[React效能優化]]
