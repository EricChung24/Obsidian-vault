---
tags: [面試, JavaScript, 回答模板]
created: 2026-04-17
---

# JavaScript面試回答模板

> 這篇不是題目大全，而是整理 JavaScript 題目的穩定回答套路。

## 回答順序

1. 先講結論
2. 再講原因
3. 最後給例子

---

## 範例 1：Event Loop

回答模板：

> JavaScript 是單執行緒，Event Loop 會在同步程式跑完後，先清掉 microtask，再處理 task。像 `Promise.then` 會比 `setTimeout` 更早執行，所以很多輸出順序題本質上都是在考這個。

延伸連結：

- [[Event Loop]]
- [[Async-Await深度指南]]

---

## 範例 2：closure

回答模板：

> closure 就是內部函式可以存取外層作用域的變數，而且外層函式執行完後，這些變數還能被保留。實務上很常出現在工廠函式、自訂 hook、事件處理器。

延伸連結：

- [[JavaScript]]

---

## 範例 3：var / let / const

回答模板：

> `var` 是函式作用域，會有提升問題；`let` 和 `const` 是區塊作用域。實務上預設用 `const`，需要重新賦值時才用 `let`。

---

## 相關連結

- [[常見面試題]]
- [[JavaScript]]

