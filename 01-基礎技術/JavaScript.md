---
tags: [前端基礎, JavaScript, ES6]
created: 2026-04-16
---

# JavaScript

> JavaScript 是前端最核心的程式語言。它不只負責互動，也牽涉非同步、資料處理、模組化與瀏覽器執行機制。

> [[前端工程師]]

---

## 為什麼重要

JavaScript 是瀏覽器唯一原生執行的程式語言。無論你用 React、Vue 還是任何框架，最終都會回到 JavaScript。

理解 JS 的作用域、閉包、非同步機制，是你能讀懂框架設計、精準排查 bug、以及通過技術面試的根本基礎。

---

## 基礎觀念

### 變數宣告

```js
var a = 1;
let b = 2;
const c = 3;
```

- `var` 有函式作用域，容易造成提升與污染問題
- `let`、`const` 有區塊作用域
- 預設優先使用 `const`，需要重新賦值時再用 `let`

### 資料型別

```js
typeof 'hello'; // string
typeof 123; // number
typeof true; // boolean
typeof undefined; // undefined
typeof null; // object
typeof Symbol(); // symbol
typeof 10n; // bigint
```

常見要點：

- `null` 的 `typeof` 是 `object`，這是歷史問題
- 陣列與一般物件都屬於 reference type

---

## 作用域與閉包

```js
function makeCounter() {
  let count = 0;

  return {
    increment() {
      count += 1;
    },
    get() {
      return count;
    },
  };
}
```

閉包的重點不是背定義，而是理解：

- 內部函式可以存取外層變數
- 外層函式執行完後，資料仍可能被保留

這在事件處理、工廠函式、自訂 hook 都很常見。

---

## 陣列方法

```js
const nums = [1, 2, 3, 4, 5];

nums.map(n => n * 2);
nums.filter(n => n % 2 === 0);
nums.reduce((acc, n) => acc + n, 0);
nums.find(n => n > 3);
nums.some(n => n > 4);
nums.every(n => n > 0);
```

實務上最常搞混的是：

- `map`：轉換資料
- `filter`：篩選資料
- `reduce`：累積結果

---

## 解構與展開

```js
const [first, second] = [1, 2];
const { name, age } = user;

const merged = { ...defaults, ...overrides };
const list = [...arr1, ...arr2];
```

這些語法在 React props、state 更新、API 資料處理都很常用。

---

## Promise 與非同步

```js
fetch('/api/users')
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

如果你用 `async/await`：

```js
async function loadUser(id) {
  const res = await fetch(`/api/users/${id}`);
  if (!res.ok) throw new Error(`HTTP ${res.status}`);
  return res.json();
}
```

---

## Event Loop

```js
console.log('1');

setTimeout(() => console.log('4'), 0);

Promise.resolve().then(() => console.log('3'));

console.log('2');
```

輸出順序：

```js
1
2
3
4
```

原因是：

- 同步程式先跑
- `Promise.then` 屬於 microtask
- `setTimeout` 屬於 task

想完整理解可以看 [[Event Loop]]。

---

## 模組化

```js
export const PI = 3.14;
export function add(a, b) {
  return a + b;
}

import { PI, add } from './math.js';
```

前端專案現在大多使用 ES Modules。

---

## 常見誤區

### `setTimeout(fn, 0)` 不是立刻執行

它只是盡快排進 task queue，不是直接同步執行。

### `forEach` 不適合搭配 `await`

如果你需要逐筆等待，應該用 `for...of`。

### `==` 與 `===`

實務上預設用 `===`，避免型別轉換造成誤判。

---

## 面試常考

- 閉包是什麼
- `var` / `let` / `const` 差異
- `this` 綁定
- Event Loop 與 Promise 順序
- 淺拷貝與深拷貝
- 陣列方法差異

---

## 相關連結

- [[TypeScript]]
- [[Fetch API]]
- [[Event Loop]]
- [[Async-Await深度指南]]
- [[JavaScript設計模式]]
- [[常見面試題]]
- [[JavaScript面試回答模板]]
