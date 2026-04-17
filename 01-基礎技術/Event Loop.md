---
tags: [JavaScript, 瀏覽器, Event Loop, 非同步]
created: 2026-04-17
---

# Event Loop

> Event Loop 是 JavaScript 處理同步與非同步任務的核心機制。它會協調 [[JavaScript]]、Call Stack、Web APIs、Task Queue 與 Microtask Queue，決定程式實際執行順序。

---

## 它是什麼

JavaScript 在瀏覽器中通常是單執行緒。

這代表同一時間只能做一件事，但實際上我們又常常會寫：

- `setTimeout`
- `fetch`
- `Promise.then`
- `async/await`
- DOM 事件監聽

這些非同步工作之所以能運作，就是因為 Event Loop 會負責安排它們什麼時候回到主執行緒執行。

---

## 核心組成

### 1. Call Stack

Call Stack 是目前正在執行的函式堆疊。

同步程式碼會直接進入 Call Stack，先進後出。

### 2. Web APIs

像 `setTimeout`、DOM 事件、`fetch` 這些瀏覽器能力，不是在 JavaScript 引擎裡直接執行，而是交給瀏覽器提供的 Web APIs 處理。

### 3. Task Queue

也常被叫做 Macrotask Queue。

常見來源：

- `setTimeout`
- `setInterval`
- DOM 事件 callback
- `postMessage`

### 4. Microtask Queue

優先級比 Task Queue 高。

常見來源：

- `Promise.then`
- `Promise.catch`
- `Promise.finally`
- `queueMicrotask`
- `MutationObserver`

---

## 執行順序

Event Loop 會一直重複這個流程：

1. 先執行 Call Stack 中的同步程式
2. Call Stack 清空後，先清空 Microtask Queue
3. 再從 Task Queue 取出一個任務執行
4. 重複以上流程

一句話記：

`同步程式 > Microtask > Task`

---

## 範例 1：Promise 與 setTimeout

```js
console.log('start');

setTimeout(() => {
  console.log('timeout');
}, 0);

Promise.resolve().then(() => {
  console.log('promise');
});

console.log('end');
```

輸出順序：

```js
start
end
promise
timeout
```

原因：

- `console.log('start')` 和 `console.log('end')` 是同步程式
- `Promise.then` 進入 Microtask Queue
- `setTimeout` 進入 Task Queue
- 同步跑完後，先執行 microtask，再執行 task

---

## 範例 2：async / await

```js
async function run() {
  console.log('A');
  await Promise.resolve();
  console.log('B');
}

console.log('C');
run();
console.log('D');
```

輸出順序：

```js
C
A
D
B
```

原因：

- `run()` 開始執行時，`console.log('A')` 是同步
- `await` 之後的程式會被放進 microtask
- 所以 `D` 先印，`B` 最後印

---

## 實務上你會在哪裡遇到

### React 狀態更新觀察

你在 [[React]] 裡 `setState` 後立刻 `console.log`，可能看到的是舊值。這不完全只是 Event Loop 問題，但和更新排程、批次更新、非同步流程很有關係。

### API 請求順序

當你發多個請求、接 `Promise.then`、又混用 `setTimeout` 時，如果不懂 microtask 和 task，常常會誤判執行順序。

### 面試題

很多前端面試都會直接給一段程式碼，問你輸出順序。這其實就是在考：

- 同步與非同步
- Promise 優先順序
- `await` 本質
- Event Loop 流程

---

## 常見誤區

### `setTimeout(fn, 0)` 不是立刻執行

它只是表示「最早可以進入 Task Queue」，不是馬上進 Call Stack。

### Promise 一定比 setTimeout 先執行

更精確地說，不是 Promise 本身，而是 `then/catch/finally` 產生的 microtask 會比 task 先執行。

### async function 是完全非同步

不對。`async function` 進去之後，`await` 前面的部分還是同步執行。

---

## 怎麼 debug

1. 先分清楚哪些是同步程式
2. 再找哪些 callback 會進 microtask
3. 再找哪些 callback 會進 task queue
4. 最後用 `console.log` 標記順序驗證

如果是面試題，最穩定的拆法就是：

- 先畫同步
- 再畫 microtask
- 最後畫 task

---

## 面試怎麼回答

你可以用這個簡短版本：

> JavaScript 是單執行緒，Event Loop 會在同步程式跑完後，先處理 Microtask Queue，再處理 Task Queue。像 `Promise.then` 屬於 microtask，`setTimeout` 屬於 task，所以通常 Promise callback 會比 setTimeout 先執行。

---

## 相關連結

- [[JavaScript]]
- [[Async-Await深度指南]]
- [[瀏覽器渲染原理]]
- [[React渲染與重渲染]]
