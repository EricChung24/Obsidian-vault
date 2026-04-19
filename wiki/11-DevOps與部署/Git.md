---
tags: [Git, 版本控制, 協作]
created: 2026-04-16
---

# Git

> Git 是前端工程師最基本的版本控制工具。它的重點不是背指令，而是理解變更、分支、合併與協作流程。

> [[前端工程師]]

---

## 最常用指令

```bash
git status
git add .
git commit -m "feat: add login page"
git pull origin main --rebase
git push origin feature/login
```

---

## 分支觀念

常見分支：

- `main`
- `develop`
- `feature/*`
- `hotfix/*`

不是每個團隊都用同一套命名，但核心觀念一樣：

- 正式分支要穩
- 新功能應該在獨立分支做

---

## 常見工作流程

### 開新功能

```bash
git switch -c feature/login
```

### 查看變更

```bash
git diff
git status
```

### 提交

```bash
git add .
git commit -m "feat: add login form"
```

### 同步主分支

```bash
git pull origin main --rebase
```

---

## 常見誤區

### 改一堆東西只做一個 commit

會讓 review 與回溯都變難。

### 不看 `git status` 就亂推

很容易把不該上的檔案一起送上去。

### 不理解 rebase 就亂用

rebase 很好用，但要先知道它在改提交歷史。

---

## 實務建議

- commit 訊息寫清楚
- 一次 commit 一個明確目的
- push 前先看 diff
- PR 前先同步主分支

---

## 相關連結

- [[CI-CD]]
