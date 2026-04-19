---
tags: [設計模式, SOLID, 架構, 最佳實踐]
created: 2026-04-16
---

# SOLID 原則

> SOLID 是五個物件導向設計原則，讓程式碼更易維護、擴展和測試。

← [[前端工程師]] | 相關：[[JavaScript設計模式]] | [[元件設計模式]] | [[TypeScript]]

---

## 總覽

| 字母 | 原則 | 核心思想 |
|------|------|---------|
| S | Single Responsibility | 一個類別/函式只做一件事 |
| O | Open/Closed | 對擴展開放，對修改封閉 |
| L | Liskov Substitution | 子類別可替換父類別 |
| I | Interface Segregation | 不強迫依賴不需要的介面 |
| D | Dependency Inversion | 依賴抽象，不依賴實作 |

---

## S — 單一職責原則（Single Responsibility）

> 一個模組應該只有一個改變的理由。

```ts
// ❌ 違反 SRP：一個類別做太多事
class UserManager {
  getUser(id: string) { /* 查 DB */ }
  saveUser(user: User) { /* 存 DB */ }
  sendWelcomeEmail(user: User) { /* 發信 */ }
  renderUserCard(user: User) { /* 渲染 HTML */ }
  validateUser(user: User) { /* 驗證 */ }
}

// ✅ 遵守 SRP：職責分離
class UserRepository {
  getUser(id: string) { /* 查 DB */ }
  saveUser(user: User) { /* 存 DB */ }
}

class EmailService {
  sendWelcomeEmail(user: User) { /* 發信 */ }
}

class UserValidator {
  validate(user: User): ValidationResult { /* 驗證 */ }
}
```

```tsx
// React 元件的 SRP 應用

// ❌ 一個元件承擔太多：資料獲取 + 格式化 + 渲染
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  useEffect(() => { fetch(`/api/users/${userId}`).then(r => r.json()).then(setUser); }, [userId]);

  return (
    <div>
      <h1>{user?.name.toUpperCase()}</h1>
      <p>{new Date(user?.createdAt).toLocaleDateString('zh-TW')}</p>
      <p>{user?.email.replace(/(.{3}).*(@.*)/, '$1***$2')}</p>
    </div>
  );
}

// ✅ 關注點分離
function useUser(userId: string) {       // Hook：負責資料獲取
  const [user, setUser] = useState(null);
  useEffect(() => { fetchUser(userId).then(setUser); }, [userId]);
  return user;
}

function formatUser(user: User) {         // 工具函式：負責格式化
  return {
    displayName: user.name.toUpperCase(),
    joinDate: new Date(user.createdAt).toLocaleDateString('zh-TW'),
    maskedEmail: user.email.replace(/(.{3}).*(@.*)/, '$1***$2'),
  };
}

function UserProfile({ userId }) {        // 元件：只負責渲染
  const user = useUser(userId);
  if (!user) return <Skeleton />;
  const { displayName, joinDate, maskedEmail } = formatUser(user);
  return (
    <div>
      <h1>{displayName}</h1>
      <p>{joinDate}</p>
      <p>{maskedEmail}</p>
    </div>
  );
}
```

---

## O — 開放封閉原則（Open/Closed）

> 對擴展開放，對修改封閉。加新功能不應該改動既有程式碼。

```ts
// ❌ 每新增一種折扣就要修改 calculateDiscount
function calculateDiscount(user: User, amount: number): number {
  if (user.type === 'vip') return amount * 0.8;
  if (user.type === 'member') return amount * 0.9;
  if (user.type === 'student') return amount * 0.85;
  // 下次再加？又要來改這裡...
  return amount;
}

// ✅ 策略模式：新增折扣只需新增一個策略，不改既有程式碼
interface DiscountStrategy {
  calculate(amount: number): number;
}

class VipDiscount implements DiscountStrategy {
  calculate(amount: number) { return amount * 0.8; }
}

class MemberDiscount implements DiscountStrategy {
  calculate(amount: number) { return amount * 0.9; }
}

class StudentDiscount implements DiscountStrategy {
  calculate(amount: number) { return amount * 0.85; }
}

// 新增「生日折扣」只需加這個，不動任何既有程式碼
class BirthdayDiscount implements DiscountStrategy {
  calculate(amount: number) { return amount * 0.7; }
}

class PriceCalculator {
  constructor(private discount: DiscountStrategy) {}
  calculate(amount: number) { return this.discount.calculate(amount); }
}
```

```tsx
// React 的 OCP：用 props 擴展，不修改元件

// ❌ 每加一種 icon 就要改 Button
function Button({ type }) {
  if (type === 'submit') return <button><SaveIcon /> 儲存</button>;
  if (type === 'delete') return <button><TrashIcon /> 刪除</button>;
  // ...
}

// ✅ 開放擴展：傳入什麼 icon 就渲染什麼
function Button({ icon: Icon, children, ...props }) {
  return (
    <button {...props}>
      {Icon && <Icon className="mr-2" />}
      {children}
    </button>
  );
}

// 使用者自由組合
<Button icon={SaveIcon}>儲存</Button>
<Button icon={TrashIcon} variant="danger">刪除</Button>
<Button icon={DownloadIcon}>匯出報表</Button>
```

---

## L — 里氏替換原則（Liskov Substitution）

> 子類別必須能夠替換父類別，且不改變程式的正確性。

```ts
class Rectangle {
  constructor(protected width: number, protected height: number) {}
  setWidth(w: number) { this.width = w; }
  setHeight(h: number) { this.height = h; }
  getArea() { return this.width * this.height; }
}

// ❌ 違反 LSP：Square 繼承 Rectangle，但行為不一致
class Square extends Rectangle {
  setWidth(w: number) {
    this.width = w;
    this.height = w;  // 正方形強制長寬相等
  }
  setHeight(h: number) {
    this.width = h;
    this.height = h;
  }
}

function testArea(rect: Rectangle) {
  rect.setWidth(5);
  rect.setHeight(10);
  console.log(rect.getArea()); // 期望 50
}

testArea(new Rectangle(0, 0)); // 50 ✅
testArea(new Square(0));       // 100 ❌ — Square 無法替換 Rectangle！

// ✅ 修正：使用組合而非繼承，或共同的抽象介面
interface Shape {
  getArea(): number;
}
class Rectangle implements Shape { /* ... */ }
class Square implements Shape { /* ... */ }
```

---

## I — 介面隔離原則（Interface Segregation）

> 客戶端不應該被強迫依賴它不需要的介面。

```ts
// ❌ 胖介面：所有 Worker 都要實作，但不是每種 Worker 都有所有能力
interface Worker {
  work(): void;
  eat(): void;
  sleep(): void;
}

class Robot implements Worker {
  work() { /* OK */ }
  eat()  { throw new Error('機器人不吃飯！'); }  // 被迫實作，但不合理
  sleep(){ throw new Error('機器人不睡覺！'); }
}

// ✅ 細化介面：按需組合
interface Workable  { work(): void; }
interface Feedable  { eat(): void; }
interface Sleepable { sleep(): void; }

class Human implements Workable, Feedable, Sleepable {
  work()  { /* ... */ }
  eat()   { /* ... */ }
  sleep() { /* ... */ }
}

class Robot implements Workable {
  work() { /* ... */ }
}
```

```tsx
// React 的 ISP：Props 細化，不傳多餘的東西

// ❌ 一個超大的 user 物件傳給每個子元件
function UserCard({ user }: { user: FullUserObject }) {
  // 用到 name, avatar（但 user 裡還有 address, payment, order 等很多東西）
}

// ✅ 只傳元件需要的屬性
interface UserCardProps {
  name: string;
  avatarUrl: string;
}

function UserCard({ name, avatarUrl }: UserCardProps) {
  // 元件只知道自己需要的，解耦更徹底
}

// 呼叫處做解構
<UserCard name={user.name} avatarUrl={user.profile.avatarUrl} />
```

---

## D — 依賴反轉原則（Dependency Inversion）

> 高層模組不應依賴低層模組，兩者都應依賴抽象。

```ts
// ❌ 高層直接依賴具體實作（MySQL）
class OrderService {
  private db = new MySQLDatabase();  // 寫死！換成 PostgreSQL 就得改這裡

  createOrder(order: Order) {
    this.db.save(order);
  }
}

// ✅ 依賴抽象介面，實作由外部注入
interface Database {
  save<T>(data: T): Promise<void>;
  findById<T>(id: string): Promise<T>;
}

class OrderService {
  constructor(private db: Database) {}  // 依賴介面，不依賴實作

  createOrder(order: Order) {
    this.db.save(order);
  }
}

// 生產環境
const orderService = new OrderService(new MySQLDatabase());

// 測試環境（換成 Mock，不需要改 OrderService）
const mockDb: Database = {
  save: jest.fn().mockResolvedValue(undefined),
  findById: jest.fn(),
};
const orderService = new OrderService(mockDb);
```

```tsx
// React 的 DIP：用 Context / Props 注入依賴

// ❌ 元件直接調用具體 API
function UserList() {
  const [users, setUsers] = useState([]);
  useEffect(() => {
    fetch('/api/users').then(r => r.json()).then(setUsers);  // 寫死！
  }, []);
}

// ✅ 透過 Hook 抽象，實作可替換（測試時 mock useUsers）
function useUsers() {
  const [users, setUsers] = useState([]);
  useEffect(() => { fetchUsers().then(setUsers); }, []);
  return users;
}

function UserList() {
  const users = useUsers();  // 元件只依賴 Hook 的介面
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

---

## 實際應用時機

```
SOLID 不是要嚴格遵守所有規則，而是在恰當的時機應用：

✅ 適合應用 SOLID 的場景：
   - 需求頻繁變動的業務邏輯
   - 多人協作的大型專案
   - 需要高測試覆蓋率的核心功能

⚠️ 不必過度設計的場景：
   - 簡單的 CRUD 操作
   - 一次性的腳本
   - 原型/概念驗證階段

記住：過度工程化（Over-engineering）也是一種問題。
先讓程式碼正確運作，再考慮是否需要重構。
```

---

## 相關連結

- [[JavaScript設計模式]] — 具體設計模式實作
- [[元件設計模式]] — React 元件架構模式
- [[TypeScript]] — 介面與型別系統
- [[單元測試]] — 依賴注入讓測試更容易
