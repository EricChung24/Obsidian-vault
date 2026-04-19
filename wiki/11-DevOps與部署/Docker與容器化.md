---
tags: [DevOps, Docker, 容器化, 部署]
created: 2026-04-16
---

# Docker 與容器化

> 打包前端應用到容器，確保環境一致性。

← [[前端工程師]]

---

## 前端 Dockerfile

```dockerfile
# 多階段建置 — 保持映像小
# ── 第一階段：建置 ──────────────────────
FROM node:20-alpine AS builder

WORKDIR /app

# 先複製 package.json，利用 Docker 快取
COPY package.json pnpm-lock.yaml ./
RUN npm install -g pnpm && pnpm install --frozen-lockfile

COPY . .

ARG VITE_API_URL
ENV VITE_API_URL=$VITE_API_URL

RUN pnpm build

# ── 第二階段：執行 ──────────────────────
FROM nginx:alpine AS production

# 複製建置產物
COPY --from=builder /app/dist /usr/share/nginx/html

# Nginx 設定（SPA 路由支援）
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## nginx.conf（SPA 路由）

```nginx
server {
  listen 80;
  root /usr/share/nginx/html;
  index index.html;

  # Gzip 壓縮
  gzip on;
  gzip_types text/plain text/css application/javascript application/json;

  # 靜態資源長期快取（有 hash 的檔名）
  location ~* \.(js|css|png|jpg|svg|woff2)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
  }

  # HTML 不快取
  location / {
    try_files $uri $uri/ /index.html;  # SPA fallback
    add_header Cache-Control "no-cache";
  }

  # 安全 Header
  add_header X-Frame-Options "SAMEORIGIN";
  add_header X-Content-Type-Options "nosniff";
  add_header Referrer-Policy "strict-origin-when-cross-origin";
}
```

---

## docker-compose.yml（本地開發）

```yaml
version: '3.9'

services:
  frontend:
    build:
      context: ./frontend
      target: builder         # 使用 builder 階段（熱重載）
    volumes:
      - ./frontend/src:/app/src  # 掛載源碼，支援熱重載
    ports:
      - "3000:3000"
    environment:
      - VITE_API_URL=http://backend:8080
    command: pnpm dev --host

  backend:
    image: node:20-alpine
    working_dir: /app
    volumes:
      - ./backend:/app
    ports:
      - "8080:8080"
    command: node server.js

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    depends_on: [frontend, backend]
    volumes:
      - ./nginx/local.conf:/etc/nginx/conf.d/default.conf
```

---

## 相關連結

- [[CI-CD]] — 自動化建置映像
- [[Git]] — 版本管理
