---
tags: [React, 實戰, 元件設計]
created: 2026-04-17
---

# React元件拆分實戰

> React 元件拆分的目的不是把檔案拆小而已，而是讓每個元件的責任更清楚、重用性更高、render 範圍更可控。

> [[前端工程師]] | [[React]]

---

## 拆分時先看三件事

1. 這段 UI 會不會重用？
2. 這段邏輯是不是可以獨立理解？
3. 這段 state 會不會只影響局部？

---

## 什麼情況該拆

### 畫面區塊責任不同

例如：

- SearchBar
- FilterPanel
- UserList
- Pagination

### state 更新頻率不同

如果搜尋輸入每打一個字都會更新，就不應該拖著整頁一起重跑。

### 有明確重用價值

像 Button、Card、Modal 這類。

---

## 什麼情況不要硬拆

### 只是為了「看起來比較專業」

如果一拆反而 props 傳超長，代表可能沒比較好。

### 元件太小到失去語意

像把一個只有一行文字的區塊硬拆成獨立元件，通常沒必要。

---

## 常見實戰拆法

### UI 元件（純顯示）

只管顯示，不碰業務邏輯。資料來自 props。

```tsx
interface UserCardProps {
  name: string;
  avatarUrl: string;
  onClick: () => void;
}

function UserCard({ name, avatarUrl, onClick }: UserCardProps) {
  return (
    <div className="card" onClick={onClick}>
      <img src={avatarUrl} alt={name} />
      <span>{name}</span>
    </div>
  );
}
```

### Feature 元件（組合業務）

負責一個完整功能區域，組合多個 UI 元件，自己管理 state 或串 API。

```tsx
function UserListSection() {
  const { data: users, isLoading } = useUsers();

  if (isLoading) return <Spinner />;

  return (
    <div>
      {users.map(user => (
        <UserCard
          key={user.id}
          name={user.name}
          avatarUrl={user.avatar}
          onClick={() => navigate(`/users/${user.id}`)}
        />
      ))}
    </div>
  );
}
```

### Custom Hook（抽邏輯）

把邏輯抽出來，讓元件變薄。

```tsx
// 抽邏輯前
function SearchPage() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!query) return;
    setLoading(true);
    fetchSearch(query).then(setResults).finally(() => setLoading(false));
  }, [query]);
  // ...
}

// 抽邏輯後
function useSearch(query: string) {
  const [results, setResults] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!query) return;
    setLoading(true);
    fetchSearch(query).then(setResults).finally(() => setLoading(false));
  }, [query]);

  return { results, loading };
}

function SearchPage() {
  const [query, setQuery] = useState('');
  const { results, loading } = useSearch(query);
  // 元件只剩 UI 邏輯
}
```

---

## 常見誤區

### 元件越小越好

不對。重點是邊界清楚，不是越碎越好。

### 所有邏輯都應該抽 hook

如果邏輯只在單一元件用，未必值得抽。

---

## 相關連結

- [[React]]
- [[元件設計模式]]
- [[React效能優化]]
- [[React渲染與重渲染]]

