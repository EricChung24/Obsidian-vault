---
tags: [React, 實戰, 表單]
created: 2026-04-17
---

# React表單提交流程實戰

> React 表單最常出問題的地方，不是欄位畫不出來，而是提交流程、錯誤處理、loading 狀態與重複送出控制。

> [[前端工程師]] | [[React]] | [[表單處理]]

---

## 一個穩定的提交流程

實務上通常要處理這幾件事：

1. 讀取欄位值
2. 前端驗證
3. 送出 API
4. 處理 loading
5. 處理成功與失敗
6. 避免重複提交

---

## 基本範例

```tsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');
  const [loading, setLoading] = useState(false);

  async function handleSubmit(e: React.FormEvent) {
    e.preventDefault();

    if (!email || !password) {
      setError('請填寫完整欄位');
      return;
    }

    try {
      setLoading(true);
      setError('');

      const response = await fetch('/api/login', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ email, password }),
      });

      if (!response.ok) {
        throw new Error('登入失敗');
      }
    } catch (err) {
      setError(err instanceof Error ? err.message : '發生未知錯誤');
    } finally {
      setLoading(false);
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={e => setEmail(e.target.value)} />
      <input value={password} onChange={e => setPassword(e.target.value)} />
      {error && <p>{error}</p>}
      <button disabled={loading}>{loading ? '送出中...' : '登入'}</button>
    </form>
  );
}
```

---

## 真正常見的坑

### 重複點擊

如果沒有 `loading` 狀態與 `disabled`，使用者很容易連點送出。

### API 成功但畫面沒更新

送出後不只要關 loading，還要決定：

- 要不要 reset 表單
- 要不要跳頁
- 要不要刷新資料

### 後端錯誤訊息沒顯示

前端如果只寫死「提交失敗」，使用者其實不知道問題在哪。

---

## 實務建議

- loading 一定要有
- API error 要顯示給使用者
- 成功後流程要明確
- 表單大一點時優先考慮 [[表單處理]] 裡的工具化方案

---

## 相關連結

- [[表單處理]]
- [[Fetch API]]
- [[React]]
- [[API錯誤處理實戰]]

