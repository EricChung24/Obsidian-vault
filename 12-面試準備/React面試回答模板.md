---
tags: [面試, React, 回答模板]
created: 2026-04-17
---

# React面試回答模板

> React 題目很多不是在考你有沒有背 API，而是在看你能不能把 render、state、effect 講清楚。

## 回答順序

1. 先定義概念
2. 再講什麼情況會發生
3. 最後補實務例子

---

## 範例 1：什麼會觸發 re-render

回答模板：

> React 元件重渲染通常來自 state、props、context 改變，或父元件重渲染。render 不等於 DOM 全重建，React 會先做 diff，再只更新變動的部分。

延伸連結：

- [[React渲染與重渲染]]

---

## 範例 2：`useEffect` 什麼時候該用

回答模板：

> `useEffect` 應該只用在真正副作用，例如 API 請求、訂閱、timer、DOM 操作。如果只是根據資料推導畫面，應該直接在 render 階段處理，不要濫用 effect。

延伸連結：

- [[React]]

---

## 範例 3：狀態管理怎麼選

回答模板：

> 我會先分清楚是 local state、global UI state，還是 server state。局部狀態就留在元件內，共用狀態才考慮 store，API 資料則優先考慮 React Query 這類工具。

延伸連結：

- [[React狀態設計實戰]]
- [[Redux]]
- [[Zustand]]
- [[React Query]]

---

## 相關連結

- [[常見面試題]]
- [[React]]
- [[React狀態設計實戰]]
- [[React元件拆分實戰]]
