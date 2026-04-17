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

這等於把 TypeScript 的價值直接關掉。

### 型別寫越複雜越厲害

不對。型別的目的應該是幫助理解與保護程式，不是炫技。

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
