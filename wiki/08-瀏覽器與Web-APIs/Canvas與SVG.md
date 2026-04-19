---
tags: [Canvas, SVG, 圖形, 動畫, Web API]
created: 2026-04-16
---

# Canvas 與 SVG

> Canvas 擅長像素級繪圖和動畫，SVG 擅長可縮放的向量圖形。各有所長，按需選用。

← [[前端工程師]] | 相關：[[CSS動畫]] | [[JavaScript]]

---

## Canvas vs SVG 比較

| | Canvas | SVG |
|--|--------|-----|
| 渲染方式 | 點陣（像素）| 向量（XML）|
| DOM | 無（整個畫布是一個元素）| 每個圖形都是 DOM 節點 |
| 縮放 | 模糊（除非 DPR 處理）| 清晰（矢量）|
| 效能 | 好（大量粒子/遊戲）| 差（大量元素時）|
| 互動 | 需手動計算座標 | 可直接綁定事件 |
| 適用 | 遊戲、圖表、圖片處理 | 圖示、圖表、動畫圖形 |

---

## Canvas 基礎

```html
<canvas id="myCanvas" width="800" height="400"></canvas>
```

```js
const canvas = document.getElementById('myCanvas');
const ctx = canvas.getContext('2d');

// 處理高 DPI 螢幕（Retina 顯示器）
function setupHiDPI(canvas) {
  const dpr = window.devicePixelRatio || 1;
  const rect = canvas.getBoundingClientRect();
  canvas.width  = rect.width  * dpr;
  canvas.height = rect.height * dpr;
  canvas.style.width  = rect.width  + 'px';
  canvas.style.height = rect.height + 'px';
  ctx.scale(dpr, dpr);
}
setupHiDPI(canvas);
```

### 基本繪圖

```js
// 矩形
ctx.fillStyle = '#3b82f6';
ctx.fillRect(10, 10, 100, 80);          // fillRect(x, y, width, height)

ctx.strokeStyle = '#1e40af';
ctx.lineWidth = 2;
ctx.strokeRect(120, 10, 100, 80);       // 只有邊框

ctx.clearRect(0, 0, canvas.width, canvas.height); // 清除區域

// 路徑（Path）
ctx.beginPath();
ctx.moveTo(250, 10);       // 起點
ctx.lineTo(350, 90);       // 畫線到
ctx.lineTo(150, 90);       // 繼續畫
ctx.closePath();           // 閉合路徑（連回起點）
ctx.fillStyle = '#10b981';
ctx.fill();
ctx.strokeStyle = '#059669';
ctx.stroke();

// 圓形（arc）
ctx.beginPath();
ctx.arc(
  400, 50,    // 圓心 x, y
  40,         // 半徑
  0,          // 開始角度（弧度）
  Math.PI * 2 // 結束角度（2π = 完整圓）
);
ctx.fillStyle = '#f59e0b';
ctx.fill();

// 半圓
ctx.beginPath();
ctx.arc(500, 50, 40, 0, Math.PI);  // 0 到 π = 下半圓
ctx.stroke();

// 圓弧 + 貝塞爾曲線
ctx.beginPath();
ctx.moveTo(600, 90);
ctx.bezierCurveTo(620, 10, 680, 10, 700, 90);
ctx.stroke();
```

### 文字

```js
ctx.font = 'bold 24px Inter, sans-serif';
ctx.fillStyle = '#1e293b';
ctx.textAlign = 'center';      // left / right / center / start / end
ctx.textBaseline = 'middle';   // top / middle / bottom / alphabetic
ctx.fillText('Hello Canvas!', 400, 50);
ctx.strokeText('Outline Text', 400, 90);  // 只有文字輪廓

// 測量文字寬度
const metrics = ctx.measureText('Hello');
console.log(metrics.width);
```

### 圖片

```js
const img = new Image();
img.src = '/photo.jpg';
img.onload = () => {
  // drawImage(img, dx, dy)
  ctx.drawImage(img, 0, 0);

  // drawImage(img, dx, dy, dWidth, dHeight) — 縮放
  ctx.drawImage(img, 0, 0, 300, 200);

  // drawImage(img, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight) — 裁切
  ctx.drawImage(img, 100, 100, 200, 200, 0, 0, 100, 100);
};

// 儲存畫布為圖片
const dataURL = canvas.toDataURL('image/png');
const link = document.createElement('a');
link.href = dataURL;
link.download = 'canvas.png';
link.click();
```

---

## Canvas 動畫

```js
// 遊戲迴圈：用 requestAnimationFrame 實現流暢動畫
let x = 0;
let animationId;

function draw(timestamp) {
  // 清除上一幀
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // 更新狀態
  x = (x + 2) % canvas.width;

  // 繪製當前幀
  ctx.beginPath();
  ctx.arc(x, canvas.height / 2, 30, 0, Math.PI * 2);
  ctx.fillStyle = '#3b82f6';
  ctx.fill();

  // 請求下一幀
  animationId = requestAnimationFrame(draw);
}

// 開始動畫
animationId = requestAnimationFrame(draw);

// 停止動畫
function stop() {
  cancelAnimationFrame(animationId);
}
```

### 粒子系統範例

```js
class Particle {
  constructor(canvas) {
    this.canvas = canvas;
    this.reset();
  }
  reset() {
    this.x = Math.random() * this.canvas.width;
    this.y = Math.random() * this.canvas.height;
    this.vx = (Math.random() - 0.5) * 2;
    this.vy = (Math.random() - 0.5) * 2;
    this.size = Math.random() * 4 + 1;
    this.alpha = Math.random();
  }
  update() {
    this.x += this.vx;
    this.y += this.vy;
    this.alpha -= 0.005;
    if (this.alpha <= 0) this.reset();
  }
  draw(ctx) {
    ctx.save();
    ctx.globalAlpha = this.alpha;
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
    ctx.fillStyle = '#3b82f6';
    ctx.fill();
    ctx.restore();
  }
}

const particles = Array.from({ length: 100 }, () => new Particle(canvas));

function loop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  particles.forEach(p => { p.update(); p.draw(ctx); });
  requestAnimationFrame(loop);
}
loop();
```

---

## SVG 基礎

SVG 是 XML 格式，可以內嵌在 HTML 中，也可以作為外部檔案。

```html
<!-- 內嵌 SVG -->
<svg width="200" height="200" viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">

  <!-- 矩形 -->
  <rect x="10" y="10" width="80" height="60" fill="#3b82f6" rx="8"/>

  <!-- 圓形 -->
  <circle cx="150" cy="40" r="30" fill="#10b981" stroke="#059669" stroke-width="2"/>

  <!-- 橢圓 -->
  <ellipse cx="100" cy="150" rx="80" ry="40" fill="#f59e0b" opacity="0.8"/>

  <!-- 直線 -->
  <line x1="10" y1="180" x2="190" y2="180" stroke="#64748b" stroke-width="2"/>

  <!-- 折線 -->
  <polyline points="10,170 50,130 90,150 130,110 170,120"
            fill="none" stroke="#ef4444" stroke-width="2"/>

  <!-- 多邊形（閉合）-->
  <polygon points="100,10 130,80 70,80"
           fill="#8b5cf6" stroke="#7c3aed" stroke-width="1"/>

  <!-- 路徑（最強大）-->
  <!-- M=moveto L=lineto Q=quadratic-bezier C=cubic-bezier A=arc Z=closepath -->
  <path d="M 10 100 Q 100 20 190 100" fill="none" stroke="#3b82f6" stroke-width="3"/>

  <!-- 文字 -->
  <text x="100" y="195" text-anchor="middle" font-size="14" fill="#1e293b">
    SVG 文字
  </text>
</svg>
```

### SVG 圖示（最常見用法）

```html
<!-- 定義可重複使用的圖示 -->
<svg style="display:none">
  <symbol id="icon-star" viewBox="0 0 24 24">
    <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/>
  </symbol>
  <symbol id="icon-heart" viewBox="0 0 24 24">
    <path d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z"/>
  </symbol>
</svg>

<!-- 使用 -->
<svg width="24" height="24" fill="gold">
  <use href="#icon-star"/>
</svg>
<svg width="24" height="24" fill="red">
  <use href="#icon-heart"/>
</svg>
```

### SVG 動畫

```html
<!-- SMIL 動畫（純 SVG）-->
<circle cx="50" cy="50" r="30" fill="#3b82f6">
  <animate attributeName="r" from="30" to="50" dur="1s" repeatCount="indefinite"/>
  <animate attributeName="opacity" from="1" to="0" dur="1s" repeatCount="indefinite"/>
</circle>

<!-- CSS 動畫（推薦）-->
<style>
.svg-spin {
  animation: spin 2s linear infinite;
  transform-origin: center;
}
@keyframes spin {
  from { transform: rotate(0deg); }
  to   { transform: rotate(360deg); }
}
</style>

<svg viewBox="0 0 50 50" width="50" height="50">
  <circle class="svg-spin" cx="25" cy="25" r="20"
          fill="none" stroke="#3b82f6" stroke-width="4"
          stroke-dasharray="100" stroke-dashoffset="80"/>
</svg>
```

```css
/* Loading 圓圈（常見 SVG 動畫技巧）*/
.spinner {
  stroke-dasharray: 126;       /* 圓周長 = 2πr = 2 * 3.14 * 20 ≈ 126 */
  stroke-dashoffset: 100;      /* 隱藏多少 */
  animation: dash 1.5s ease-in-out infinite;
  transform-origin: center;
}
@keyframes dash {
  0%   { stroke-dashoffset: 126; transform: rotate(0deg); }
  50%  { stroke-dashoffset: 30; }
  100% { stroke-dashoffset: 126; transform: rotate(360deg); }
}
```

### React 中使用 SVG

```tsx
// 方法一：直接內嵌（可用 CSS 控制）
function StarIcon({ className }) {
  return (
    <svg className={className} viewBox="0 0 24 24" fill="currentColor">
      <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/>
    </svg>
  );
}

// 使用
<StarIcon className="w-6 h-6 text-yellow-400" />  // Tailwind 控制顏色和尺寸

// 方法二：Vite 直接 import SVG 為 React 元件
// vite.config.ts 需加 svgr 插件
import { ReactComponent as Logo } from './logo.svg';
<Logo width={100} height={100} />
```

---

## 實用案例：折線圖

```js
function drawLineChart(canvas, data) {
  const ctx = canvas.getContext('2d');
  const { width, height } = canvas;
  const padding = 40;

  const maxVal = Math.max(...data);
  const scaleX = (width  - padding * 2) / (data.length - 1);
  const scaleY = (height - padding * 2) / maxVal;

  // 繪製網格線
  ctx.strokeStyle = '#e2e8f0';
  ctx.lineWidth = 1;
  for (let i = 0; i <= 5; i++) {
    const y = padding + (height - padding * 2) / 5 * i;
    ctx.beginPath();
    ctx.moveTo(padding, y);
    ctx.lineTo(width - padding, y);
    ctx.stroke();
  }

  // 繪製折線
  ctx.strokeStyle = '#3b82f6';
  ctx.lineWidth = 2;
  ctx.lineJoin = 'round';
  ctx.beginPath();
  data.forEach((val, i) => {
    const x = padding + i * scaleX;
    const y = height - padding - val * scaleY;
    i === 0 ? ctx.moveTo(x, y) : ctx.lineTo(x, y);
  });
  ctx.stroke();

  // 繪製數據點
  ctx.fillStyle = '#3b82f6';
  data.forEach((val, i) => {
    const x = padding + i * scaleX;
    const y = height - padding - val * scaleY;
    ctx.beginPath();
    ctx.arc(x, y, 5, 0, Math.PI * 2);
    ctx.fill();
  });
}

drawLineChart(document.getElementById('chart'), [10, 45, 30, 70, 55, 90, 65]);
```

---

## 相關連結

- [[CSS動畫]] — CSS 動畫（transition/@keyframes）
- [[渲染優化]] — GPU 合成層、效能優化
- [[Web Worker與效能]] — 複雜計算移到 Worker
