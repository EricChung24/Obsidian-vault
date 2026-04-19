---
tags: [JavaScript, Promise, async/await]
created: 2026-04-16
---

# Async/Await深度指南

> `async/await` 是建立在 Promise 之上的語法糖。它的價值不是讓程式變同步，而是讓非同步流程更好讀、更好維護。

> [[前端工程師]] | [[JavaScript]]

---

## 從 Callback 到 Promise

早期非同步常長這樣：

```js
getUser(userId, (err, user) => {
  if (err) return handleError(err);

  getPosts(user.id, (err, posts) => {
    if (err) return handleError(err);

    renderPage(user, posts);
  });
});
```

這種巢狀 callback 很容易變成 callback hell。

Promise 改善後：

```js
getUser(userId)
  .then(user => getPosts(user.id))
  .then(posts => renderPage(posts))
  .catch(handleError);
```

---

## async / await 的本質

```js
async function loadPageData(userId) {
  const user = await getUser(userId);
  const posts = await getPosts(user.id);
  return { user, posts };
}
```

要記住兩件事：

- `async function` 會回傳 Promise
- `await` 只能在 `async function` 裡使用

---

## 錯誤處理

```js
async function fetchUser(id) {
  try {
    const res = await fetch(`/api/users/${id}`);

    if (!res.ok) {
      throw new Error(`HTTP ${res.status}`);
    }

    return await res.json();
  } catch (error) {
    console.error('取得使用者失敗', error);
    throw error;
  }
}
```

最穩定的寫法通常是：

- `await` 搭配 `try/catch`
- 對 `res.ok` 做額外檢查

---

## 平行與序列

### 序列執行

```js
async function loadSequential(ids) {
  const users = [];

  for (const id of ids) {
    const user = await fetchUser(id);
    users.push(user);
  }

  return users;
}
```

### 平行執行

```js
async function loadParallel(ids) {
  return Promise.all(ids.map(id => fetchUser(id)));
}
```

如果彼此沒有依賴，通常應優先考慮平行執行。

---

## 常見錯誤

### `forEach` 裡直接寫 `await`

```js
items.forEach(async item => {
  await processItem(item);
});
```

這樣通常不符合預期，因為 `forEach` 不會等待裡面的 Promise。

應改成：

```js
for (const item of items) {
  await processItem(item);
}
```

或：

```js
await Promise.all(items.map(item => processItem(item)));
```

### 忘記處理 rejected Promise

`await` 不是自動防呆。Promise reject 時一樣會拋錯。

---

## 跟 Event Loop 的關係

`await` 後面的程式不是立刻同步繼續執行，而是會排進 microtask。

這就是為什麼：

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

輸出會是：

```js
C
A
D
B
```

完整概念可搭配 [[Event Loop]] 一起看。

---

## 實務建議

- API 請求一律處理 `res.ok`
- 流程有依賴才用序列 `await`
- 無依賴任務優先考慮 `Promise.all`
- React 裡要注意 effect cleanup 與 race condition

---

## 相關連結

- [[JavaScript]]
- [[Event Loop]]
- [[Fetch API]]
- [[Axios]]
- [[React]]
