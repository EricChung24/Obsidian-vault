---
tags: [CSS, 動畫, Animation, Transition]
created: 2026-04-16
---

# CSS 動畫

> CSS Transition 和 @keyframes Animation，讓介面富有生命感。

← [[前端工程師]] | 相關：[[切版技巧]] | [[CSS]]

---

## Transition（過渡）

元素在狀態變化時（hover/focus/active）的平滑效果。

```css
/* 語法：transition: property duration timing-function delay */

.btn {
  background: #3b82f6;
  /* 單一屬性 */
  transition: background 0.3s ease;

  /* 多個屬性 */
  transition:
    background   0.3s ease,
    transform    0.2s ease,
    box-shadow   0.3s ease;

  /* 全部屬性（謹慎使用，影響效能）*/
  transition: all 0.3s ease;
}

.btn:hover {
  background: #2563eb;
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(37, 99, 235, 0.4);
}
```

### Timing Function（緩動函數）

```css
transition-timing-function: linear;          /* 線性，勻速 */
transition-timing-function: ease;            /* 先快後慢（預設）*/
transition-timing-function: ease-in;         /* 慢→快 */
transition-timing-function: ease-out;        /* 快→慢 */
transition-timing-function: ease-in-out;     /* 慢→快→慢 */
transition-timing-function: cubic-bezier(0.34, 1.56, 0.64, 1); /* 自訂 */
transition-timing-function: steps(4, end);   /* 分段跳躍 */
```

> **工具**：[cubic-bezier.com](https://cubic-bezier.com) 可視化調整緩動曲線

---

## @keyframes Animation（關鍵影格動畫）

```css
/* 定義動畫 */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50%       { transform: scale(1.05); }
}

@keyframes spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}

/* 套用動畫 */
.card {
  animation:
    fadeInUp           /* 動畫名稱 */
    0.5s               /* 持續時間 */
    ease-out           /* 緩動函數 */
    0.1s               /* 延遲 */
    1                  /* 次數（infinite = 無限）*/
    normal             /* 方向（alternate = 來回）*/
    forwards;          /* 結束狀態（保持最後影格）*/
}

.loading-icon {
  animation: spin 1s linear infinite;
}
```

---

## 常用動畫效果

### 淡入系列

```css
@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}

@keyframes fadeInDown {
  from { opacity: 0; transform: translateY(-20px); }
  to   { opacity: 1; transform: translateY(0); }
}

@keyframes fadeInLeft {
  from { opacity: 0; transform: translateX(-20px); }
  to   { opacity: 1; transform: translateX(0); }
}
```

### Skeleton Loading（骨架屏）

```css
@keyframes shimmer {
  0%   { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}

.skeleton {
  border-radius: 4px;
  background: linear-gradient(
    90deg,
    #e2e8f0 25%,
    #f1f5f9 50%,
    #e2e8f0 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite linear;
}

/* 使用 */
.skeleton-text   { height: 1rem; margin-bottom: 0.5rem; }
.skeleton-title  { height: 1.5rem; width: 60%; }
.skeleton-image  { height: 200px; border-radius: 8px; }
```

### Bounce（彈跳）

```css
@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
    animation-timing-function: cubic-bezier(0, 0, 0.2, 1);
  }
  50% {
    transform: translateY(-25%);
    animation-timing-function: cubic-bezier(0.8, 0, 1, 1);
  }
}

.icon:hover {
  animation: bounce 0.8s ease infinite;
}
```

### Shake（錯誤抖動）

```css
@keyframes shake {
  0%, 100% { transform: translateX(0); }
  10%, 50%, 90% { transform: translateX(-6px); }
  30%, 70% { transform: translateX(6px); }
}

.input--error {
  animation: shake 0.5s ease-in-out;
  border-color: #ef4444;
}
```

### 打字機效果

```css
@keyframes typing {
  from { width: 0; }
  to   { width: 100%; }
}

@keyframes blink {
  0%, 100% { border-color: transparent; }
  50%       { border-color: currentColor; }
}

.typewriter {
  display: inline-block;
  overflow: hidden;
  white-space: nowrap;
  border-right: 2px solid currentColor;
  width: 0;
  animation:
    typing 2s steps(30, end) forwards,
    blink  0.75s step-end infinite;
}
```

---

## CSS 變數動態動畫

```css
/* 用 CSS 變數讓動畫更彈性 */
.card {
  --delay: 0s;
  animation: fadeInUp 0.5s ease-out var(--delay) both;
}

/* HTML 中設定不同延遲 */
/* <div class="card" style="--delay: 0s">
   <div class="card" style="--delay: 0.1s">
   <div class="card" style="--delay: 0.2s"> */
```

---

## 進場動畫搭配 Intersection Observer

```js
// 進入視口才觸發動畫，節省效能
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('animate');
        observer.unobserve(entry.target); // 只觸發一次
      }
    });
  },
  { threshold: 0.1 }
);

document.querySelectorAll('.animate-on-scroll').forEach(el => {
  observer.observe(el);
});
```

```css
.animate-on-scroll {
  opacity: 0;
  transform: translateY(30px);
  transition: opacity 0.6s ease, transform 0.6s ease;
}

.animate-on-scroll.animate {
  opacity: 1;
  transform: translateY(0);
}
```

---

## 效能最佳實踐

```css
/* ✅ 只動這兩個屬性（GPU 合成層，不觸發 Layout/Paint）*/
.good {
  transform: translateX(100px) scale(1.2) rotate(45deg);
  opacity: 0.5;
}

/* ❌ 會觸發 Layout 重排，效能差 */
.bad {
  width: 200px;   /* 觸發 Reflow */
  top: 100px;     /* 觸發 Reflow */
  margin: 20px;   /* 觸發 Reflow */
}

/* will-change：提示瀏覽器預先建立合成層 */
.animated-element {
  will-change: transform, opacity;
}
/* 動畫結束後移除，避免佔用記憶體 */
.animated-element.done {
  will-change: auto;
}
```

### 無障礙：尊重用戶設定

```css
/* 用戶開啟「減少動態效果」時停用動畫 */
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## JavaScript 動畫 API

```js
// Web Animations API（現代，可程式化控制）
const el = document.querySelector('.box');

const animation = el.animate(
  [
    { transform: 'translateX(0px)', opacity: 1 },
    { transform: 'translateX(300px)', opacity: 0 },
  ],
  {
    duration: 1000,
    easing: 'ease-in-out',
    fill: 'forwards',
  }
);

// 控制播放
animation.pause();
animation.play();
animation.cancel();
animation.reverse();

// 等待結束
await animation.finished;
```

---

## 相關連結

- [[切版技巧]] — Layout 基礎
- [[渲染優化]] — GPU 加速、will-change
- [[a11y無障礙設計]] — prefers-reduced-motion
