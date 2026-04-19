---
tags: [DevOps, CI-CD, GitHub Actions, 部署]
created: 2026-04-16
---

# CI-CD

> CI 是持續整合，CD 是持續交付或持續部署。對前端來說，它的價值是把 lint、test、build、deploy 這些流程自動化，降低人為失誤。

> [[前端工程師]]

---

## 先分清楚 CI 與 CD

### CI

持續整合，重點是：

- push / PR 後自動驗證
- 先發現問題
- 確保主分支品質穩定

常見步驟：

- install
- lint
- type-check
- test
- build

### CD

持續交付或持續部署，重點是：

- 通過檢查後自動進部署流程
- 把產物送到正式或測試環境

---

## GitHub Actions 基本概念

GitHub Actions 的核心單位有三個：

- workflow
- job
- step

最常見的位置：

```txt
.github/workflows/
```

---

## 基本 CI 範例

```yaml
name: CI

on:
  pull_request:
    branches: [main]
  push:
    branches: [main, develop]

jobs:
  quality:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - name: Install
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Test
        run: npm run test

      - name: Build
        run: npm run build
```

---

## GitHub Actions 自動部署

前端專案常見流程會是：

1. push 到 `main`
2. GitHub Actions 觸發
3. 先做 lint / test / build
4. 成功後再部署

範例：

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - name: Install
        run: npm ci

      - name: Build
        run: npm run build

      - name: Deploy
        run: echo \"deploy to hosting here\"
```

---

## secrets 與環境變數

如果部署要用到：

- Token
- API Key
- SSH 金鑰

不要直接寫在 workflow 裡，要放到 GitHub repository secrets。

```yaml
env:
  DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
```

---

## 前端專案常見部署目標

### Vercel / Netlify

最省事，通常直接串 GitHub。

### GitHub Pages / Cloudflare Pages

適合靜態網站。

### 自架主機

可能會搭配：

- SSH
- rsync
- Docker

---

## 實務建議

### CI 與 Deploy 分開

比較穩定的做法通常是：

- `ci.yml`
- `deploy.yml`

### staging 與 production 分流

例如：

- `develop` -> staging
- `main` -> production

### 不要跳過 build 檢查

有些專案 lint 跟 test 都過，但 build 還是會炸。

---

## 常見誤區

### 一 push 就直接上正式站

不是不行，但風險比較高。通常建議至少先把品質檢查補齊。

### workflow 能跑就代表流程完整

不夠。你還要考慮：

- 失敗通知
- rollback
- secrets 管理
- branch 策略

---

## 相關連結

- [[Git]]
- [[Vite(主流)]]
- [[Docker與容器化]]
- [[前端監控]]
- [[前端部署排錯實戰]]
