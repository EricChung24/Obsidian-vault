# GraphQL

## Summary
GraphQL 是由 Facebook 開發的 API 查詢語言與執行引擎，讓客戶端精確指定所需資料，解決 REST API 的 over-fetching 與 under-fetching 問題。

## Plain Explanation
想像你去餐廳點餐：
- **REST**：只有套餐，不管你吃不吃完，整套都端來。
- **GraphQL**：單點制，你說要什麼，廚房只給你那些東西。

GraphQL 讓前端「精確點菜」，後端「按單出菜」。

## Technical Definition
GraphQL 是一種 API 規格，包含三個核心操作：

| 操作 | 說明 | 類比 REST |
|------|------|-----------|
| `query` | 讀取資料 | GET |
| `mutation` | 修改資料 | POST / PUT / DELETE |
| `subscription` | 即時訂閱資料流 | WebSocket |

所有請求統一送到單一端點（通常是 `/graphql`），用 POST 傳送操作字串。

## Why It Matters
- **減少請求次數**：一次查詢可取得多個資源的資料（替代多個 REST 請求）
- **型別安全**：Schema 即文件，搭配 TypeScript 可自動生成型別
- **前後端解耦**：前端自定義資料形狀，不依賴後端 API 版本迭代
- **強大的開發工具**：GraphiQL、Apollo DevTools

## Example

### 定義 Schema（後端）
```graphql
type User {
  id: ID!
  name: String!
  email: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  title: String!
  content: String!
}

type Query {
  user(id: ID!): User
  users: [User!]!
}

type Mutation {
  createPost(title: String!, content: String!): Post!
}
```

### 發送 Query（前端）
```graphql
# 只取需要的欄位，posts 的 content 不需要就不取
query GetUser($id: ID!) {
  user(id: $id) {
    name
    email
    posts {
      id
      title
    }
  }
}
```

### 用 Apollo Client 在 React 中使用
```tsx
import { useQuery, gql } from '@apollo/client';

const GET_USER = gql`
  query GetUser($id: ID!) {
    user(id: $id) {
      name
      email
    }
  }
`;

function UserProfile({ userId }: { userId: string }) {
  const { loading, error, data } = useQuery(GET_USER, {
    variables: { id: userId },
  });

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return <div>{data.user.name}</div>;
}
```

### Mutation 範例
```tsx
import { useMutation, gql } from '@apollo/client';

const CREATE_POST = gql`
  mutation CreatePost($title: String!, $content: String!) {
    createPost(title: $title, content: $content) {
      id
      title
    }
  }
`;

function CreatePostForm() {
  const [createPost, { loading }] = useMutation(CREATE_POST);

  const handleSubmit = async () => {
    await createPost({
      variables: { title: 'Hello', content: 'World' },
    });
  };

  return <button onClick={handleSubmit} disabled={loading}>發布</button>;
}
```

## Common Mistakes

### 1. N+1 查詢問題
查詢 10 個 User 各自的 Posts → 觸發 1 + 10 = 11 次 DB 查詢。
**解法**：後端使用 DataLoader 做批次處理（batching）。

### 2. 過度查詢導致效能問題
GraphQL 雖然前端可以精確取資料，但惡意查詢可能會拉取大量巢狀資料。
**解法**：設定查詢深度限制、複雜度限制。

### 3. 快取策略與 REST 不同
REST 用 URL 快取，GraphQL 全部 POST 到同一端點，需要用 Apollo Client 的 normalized cache。

### 4. 錯誤回傳格式
GraphQL 永遠回傳 HTTP 200，錯誤在 response body 的 `errors` 欄位。
```json
{
  "data": null,
  "errors": [{ "message": "User not found" }]
}
```
不能靠 HTTP status code 判斷是否成功。

## REST vs GraphQL 比較

| 面向 | REST | GraphQL |
|------|------|---------|
| 端點 | 多個 `/users`, `/posts` | 單一 `/graphql` |
| 資料格式 | 固定由後端決定 | 前端指定所需欄位 |
| Over-fetching | 常見 | 不會 |
| 版本控制 | `/v1/`, `/v2/` | Schema 演進，通常不需版本 |
| 快取 | HTTP 快取天然支援 | 需要額外設定 |
| 學習曲線 | 低 | 中等 |

## 主流工具

| 工具 | 用途 |
|------|------|
| Apollo Client | React 最主流的 GraphQL 客戶端 |
| urql | 輕量替代方案 |
| React Query + graphql-request | 簡單場景 |
| Apollo Server | Node.js GraphQL 伺服器 |
| Hasura | 自動從資料庫生成 GraphQL API |
| GraphQL Code Generator | 從 Schema 自動生成 TypeScript 型別 |

## Related Concepts
- [[Fetch API]]
- [[Axios]]
- [[React Query]]
- [[HTTP協議]]
- [[TypeScript]]
- [[CORS]]

## Source Notes
- 官方文件：graphql.org
- Apollo Client 文件：apollographql.com

## Open Questions
- tRPC 在 TypeScript 全端專案中是否更適合取代 GraphQL？
