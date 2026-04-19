# Monorepo 工具

## Summary
Monorepo 是將多個相關專案（packages）放在同一個 Git 倉庫中管理的架構策略。主流工具有 **Turborepo**、**Nx**、**pnpm workspaces**，解決多專案程式碼共用、統一版本管理的問題。

## Plain Explanation
想像你有三個專案：
- 一個 React 前端 App
- 一個 Node.js 後端 API
- 一個共用的 UI 元件庫

**Polyrepo（各自獨立）**：三個 Git repo，各自 `npm install`，更新共用元件要改三個地方，版本容易不同步。

**Monorepo（單一倉庫）**：一個 Git repo，共用程式碼直接 import，改一次全部更新。

大型公司（Google、Facebook、Vercel）都使用 Monorepo。

## Technical Definition

### 目錄結構範例
```
my-monorepo/
├── apps/
│   ├── web/          # Next.js 前端
│   └── api/          # Node.js 後端
├── packages/
│   ├── ui/           # 共用 React 元件庫
│   ├── utils/        # 共用工具函式
│   └── config/       # 共用設定（ESLint, TypeScript）
├── package.json      # root package.json
├── pnpm-workspace.yaml
└── turbo.json
```

## Turborepo（推薦）

Vercel 開發，現在最主流的 Monorepo build 工具，核心功能是**智慧快取**：沒有改動的 package 直接使用快取結果，大幅加速 CI/CD。

### 安裝與初始化
```bash
# 全新專案
npx create-turbo@latest

# 現有專案
npm install turbo --save-dev
```

### turbo.json — 定義任務依賴
```json
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],  // 先 build 依賴的 packages
      "outputs": [".next/**", "dist/**"]
    },
    "test": {
      "dependsOn": ["^build"]
    },
    "lint": {},
    "dev": {
      "cache": false,           // dev 模式不快取
      "persistent": true
    }
  }
}
```

### 常用指令
```bash
# 同時執行所有 package 的 build
turbo build

# 只執行特定 package
turbo build --filter=web

# 並行執行 dev
turbo dev

# 查看任務執行樹
turbo build --graph
```

## pnpm Workspaces — 套件管理層

Turborepo 管理「任務」，pnpm workspaces 管理「套件依賴」。

### pnpm-workspace.yaml
```yaml
packages:
  - 'apps/*'
  - 'packages/*'
```

### 在 app 中引用本地 package
```json
// apps/web/package.json
{
  "dependencies": {
    "@myapp/ui": "workspace:*",     // 引用本地 packages/ui
    "@myapp/utils": "workspace:*"
  }
}
```

```tsx
// 可以直接 import，像一般 npm 套件一樣
import { Button } from '@myapp/ui';
import { formatDate } from '@myapp/utils';
```

### 安裝指令
```bash
# 在特定 workspace 安裝依賴
pnpm --filter web add react-query

# 在所有 workspace 安裝
pnpm install

# 在 root 安裝（通常是 devDependencies 如 turbo）
pnpm add -D turbo -w
```

## 共用設定範例

### 共用 TypeScript 設定
```json
// packages/config/tsconfig.base.json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2020",
    "moduleResolution": "bundler"
  }
}

// apps/web/tsconfig.json
{
  "extends": "@myapp/config/tsconfig.base.json",
  "compilerOptions": {
    "plugins": [{ "name": "next" }]
  }
}
```

### 共用 ESLint 設定
```js
// packages/config/eslint-react.js
module.exports = {
  extends: ['eslint:recommended', 'plugin:react/recommended'],
  rules: { 'react/prop-types': 'off' },
};

// apps/web/.eslintrc.js
module.exports = {
  extends: ['@myapp/config/eslint-react'],
};
```

## Turborepo vs Nx

| 面向 | Turborepo | Nx |
|------|-----------|-----|
| 學習曲線 | 低 | 中高 |
| 設定複雜度 | 簡單 | 複雜但功能強 |
| 快取 | 本地 + 遠端（Vercel） | 本地 + 遠端（Nx Cloud） |
| 程式碼生成 | 無 | 強大的 generators |
| 適合規模 | 中小型 | 大型企業 |
| 整合 Vercel | 原生 | 需要額外設定 |

**建議**：新專案或中小型團隊用 Turborepo；大型企業有複雜需求用 Nx。

## Common Mistakes

### 1. 循環依賴
```
packages/a 依賴 packages/b
packages/b 依賴 packages/a
```
這會讓 Turborepo 無法確定 build 順序。使用 `turbo build --graph` 檢查。

### 2. 在 app 之間直接互相 import
```tsx
// apps/web 直接 import apps/api 的程式碼 — 錯誤！
```
App 之間的共用邏輯應該提取到 `packages/` 中。

### 3. 忘記設定 `outputs` 導致快取失效
Turborepo 快取需要知道哪些檔案是輸出，沒設定 `outputs` 會每次重新 build。

### 4. root `node_modules` 版本衝突
同一個 package 在不同 workspace 用不同版本可能造成問題，盡量統一版本。

## Related Concepts
- [[Vite(主流)]]
- [[套件管理]]
- [[ESLint與格式化]]
- [[TypeScript]]
- [[CI-CD]]

## Source Notes
- Turborepo 官方文件：turbo.build
- pnpm workspaces 文件：pnpm.io/workspaces
