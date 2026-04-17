---
tags: [CSS, Bootstrap, UI框架, RWD]
created: 2026-04-16
---

# Bootstrap 5

> Bootstrap 是經典的 CSS UI 框架，提供現成元件、格線系統與大量 utility class，適合快速做出可用介面。

> [[前端工程師]]

---

## 它適合什麼情況

- 需要快速交付後台或企業系統
- 團隊想用成熟的 UI 規則
- 不想從零設計太多基礎元件

---

## 基本安裝

```bash
pnpm add bootstrap
```

```ts
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap/dist/js/bootstrap.bundle.min.js';
```

---

## 格線系統

12 欄格線，搭配 `col-*` 控制各 breakpoint 的欄寬：

```html
<div class="container">
  <div class="row g-3">
    <!-- 手機全寬，平板以上各占一半 -->
    <div class="col-12 col-md-6">左側</div>
    <div class="col-12 col-md-6">右側</div>
  </div>

  <div class="row">
    <!-- 手機全寬，平板 4/8，桌面 3/9 -->
    <div class="col-12 col-md-4 col-lg-3">側邊欄</div>
    <div class="col-12 col-md-8 col-lg-9">主內容</div>
  </div>
</div>
```

---

## 常用元件範例

### Button

```html
<button class="btn btn-primary">主要</button>
<button class="btn btn-secondary">次要</button>
<button class="btn btn-danger">危險</button>
<button class="btn btn-outline-primary">外框版</button>
<button class="btn btn-sm btn-primary">小版本</button>
```

### Card

```html
<div class="card" style="width: 18rem;">
  <img src="/photo.jpg" class="card-img-top" alt="...">
  <div class="card-body">
    <h5 class="card-title">標題</h5>
    <p class="card-text">描述文字</p>
    <a href="#" class="btn btn-primary">前往</a>
  </div>
</div>
```

### Modal（用 JS 控制開關）

```html
<!-- 觸發按鈕 -->
<button data-bs-toggle="modal" data-bs-target="#confirmModal">
  開啟確認視窗
</button>

<!-- Modal 本體 -->
<div class="modal fade" id="confirmModal" tabindex="-1">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">確認刪除</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
      </div>
      <div class="modal-body">確定要刪除嗎？</div>
      <div class="modal-footer">
        <button class="btn btn-secondary" data-bs-dismiss="modal">取消</button>
        <button class="btn btn-danger">確認刪除</button>
      </div>
    </div>
  </div>
</div>
```

### 常用 Utility class

```html
<!-- spacing：m = margin，p = padding，t/b/s/e/x/y = 方向，0–5 = 大小 -->
<div class="mt-3 mb-2 px-4">...</div>

<!-- flex -->
<div class="d-flex justify-content-between align-items-center gap-2">...</div>

<!-- text -->
<p class="text-center text-muted fw-bold">...</p>

<!-- 顯示/隱藏（響應式） -->
<div class="d-none d-md-block">只在 md 以上顯示</div>
```

---

## 優點

- 生態成熟
- 上手快
- RWD 支援完整

---

## 缺點

- 容易長得很像 Bootstrap
- 客製化設計感有限
- 如果設計系統要求高，常常還是得再包一層

---

## 相關連結

- [[CSS]]
- [[Tailwind CSS]]
- [[Sass]]
