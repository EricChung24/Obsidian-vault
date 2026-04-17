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

## 常見能力

### 格線系統

```html
<div class="container">
  <div class="row">
    <div class="col-12 col-md-6">左側</div>
    <div class="col-12 col-md-6">右側</div>
  </div>
</div>
```

### 元件

常見元件有：

- Button
- Modal
- Card
- Navbar
- Form

### Utility class

例如：

- `mt-3`
- `d-flex`
- `text-center`

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
