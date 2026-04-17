---
tags: [前端基礎, TypeScript, 型別]
created: 2026-04-16
---

# TypeScript

> TypeScript 是建立在 [[JavaScript]] 之上的靜態型別系統。它的價值不是讓你多寫型別，而是讓大型前端專案更穩、更可維護。

> [[前端工程師]]

---

## 基本型別

```ts
let name: string = 'Eric';
let age: number = 30;
let active: boolean = true;

let tags: string[] = ['react', 'typescript'];
let point: [number, number] = [10, 20];
```

---

## interface 與 type

```ts
interface User {
  id: number;
  name: string;
  email?: string;
}

type Status = 'pending' | 'active' | 'disabled';
type ID = string | number;
```

簡單理解：

- `interface` 常用來描述物件結構
- `type` 很適合 union、alias、組合型別

---

## function 型別

```ts
function add(a: number, b: number): number {
  return a + b;
}
```

重點不是每個地方都標到爆，而是讓輸入輸出清楚。

---

## 泛型

```ts
function firstItem<T>(list: T[]): T | undefined {
  return list[0];
}
```

泛型的重點是：

- 型別可重用
- 不用失去型別資訊

---

## unknown vs any

`any` 關掉型別檢查；`unknown` 保留安全性，使用前必須先做型別縮窄。

```ts
// any：完全不安全
function parseAny(input: any) {
  input.toUpperCase(); // 不會報錯，但 runtime 可能爆炸
}

// unknown：安全，使用前要先確認型別
function parseUnknown(input: unknown) {
  if (typeof input === 'string') {
    input.toUpperCase(); // OK
  }
}
```

原則：能用 `unknown` 就不要用 `any`，特別是接收外部 API 回傳的時候。

---

## 型別縮窄（Type Narrowing）

TypeScript 能根據條件判斷推論更精確的型別。

```ts
type Result = { success: true; data: string } | { success: false; error: string };

function handle(result: Result) {
  if (result.success) {
    console.log(result.data);   // 這裡 TS 知道是第一種
  } else {
    console.log(result.error);  // 這裡 TS 知道是第二種
  }
}
```

這種 `{ success: true/false }` 的設計叫做 **Discriminated Union**，是 API 回傳設計的常見手法。

---

## Utility Types（實用型別工具）

TypeScript 內建一批常用工具型別，不需要自己寫。

```ts
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Partial — 所有欄位變可選（常用於 PATCH 請求 payload）
type UpdateUser = Partial<User>;

// Required — 所有欄位變必填
type FullUser = Required<User>;

// Readonly — 所有欄位變唯讀
type ImmutableUser = Readonly<User>;

// Pick — 挑選特定欄位
type UserPreview = Pick<User, 'id' | 'name'>;

// Omit — 排除特定欄位（常用於表單，排除 id）
type NewUser = Omit<User, 'id'>;

// Record — 建立 key-value 映射
type RoleMap = Record<'admin' | 'editor' | 'viewer', string[]>;

// ReturnType — 取得函式回傳型別
function getUser() {
  return { id: 1, name: 'Eric' };
}
type UserResult = ReturnType<typeof getUser>; // { id: number; name: string }
```

---

## 在 React 裡常見的 TypeScript

### props 型別

```tsx
interface UserCardProps {
  name: string;
  age: number;
}

function UserCard({ name, age }: UserCardProps) {
  return <div>{name} - {age}</div>;
}
```

### API response 型別

```ts
interface User {
  id: number;
  name: string;
}

async function fetchUsers(): Promise<User[]> {
  const res = await fetch('/api/users');
  return res.json();
}
```

---

## 常見誤區

### 把 `any` 當萬用解法

這等於把 TypeScript 的價值直接關掉。遇到型別不確定，先試 `unknown`；遇到第三方庫沒有型別，先找 `@types/xxx`。

### 型別寫越複雜越厲害

不對。型別的目的應該是幫助理解與保護程式，不是炫技。

### 過度標注型別，忽略推論

TypeScript 很強的地方是自動推論。不是每行都要加型別。

```ts
// 不必要的標注（TS 已能推論）
const count: number = 0;
const name: string = 'Eric';

// 推論即可，簡潔
const count = 0;
const name = 'Eric';
```

函式回傳型別也是，除非是公開 API，不然讓 TS 自己推比較好。

### 用 `!` 非空斷言繞過問題

```ts
const el = document.getElementById('app')!; // 強制告訴 TS「它一定存在」
el.style.color = 'red';
```

如果 `el` 真的可能是 `null`，`!` 只是掩蓋問題，應該加真正的判斷。

---

## 什麼時候最有價值

- 中大型專案
- 多人協作
- API 型別要穩
- 元件與資料流較複雜

---

## 相關連結

- [[JavaScript]]
- [[React]]
- [[Vue]]
- [[表單處理]]
