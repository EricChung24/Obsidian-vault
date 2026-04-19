# Framer Motion

## Summary
Framer Motion 是 React 生態中最主流的動畫函式庫，提供宣告式 API，讓複雜動畫只需幾行程式碼即可實現，並內建無障礙支援與手勢互動。

## Plain Explanation
寫 CSS 動畫就像「告訴瀏覽器每一步怎麼做」，很囉嗦。

Framer Motion 讓你「說結果，它幫你算過程」：
- 你說「從透明度 0 到 1」
- 它自動幫你補間、緩動、處理效能

寫 React 動畫的首選工具，幾乎是業界標準。

## Technical Definition

核心 API 圍繞 `motion` 組件：
- 任何 HTML 元素前加 `motion.` 就能動畫化
- `animate`、`initial`、`exit`、`transition` props 控制動畫行為
- `variants` 管理多狀態動畫
- `AnimatePresence` 處理組件進出場動畫

## Example

### 安裝
```bash
npm install framer-motion
```

### 基本進場動畫
```tsx
import { motion } from 'framer-motion';

function FadeIn() {
  return (
    <motion.div
      initial={{ opacity: 0, y: 20 }}   // 初始狀態
      animate={{ opacity: 1, y: 0 }}     // 目標狀態
      transition={{ duration: 0.5 }}     // 過渡設定
    >
      Hello, World!
    </motion.div>
  );
}
```

### Hover 與 Tap 互動
```tsx
<motion.button
  whileHover={{ scale: 1.05 }}
  whileTap={{ scale: 0.95 }}
  transition={{ type: 'spring', stiffness: 400, damping: 17 }}
>
  Click Me
</motion.button>
```

### AnimatePresence — 進出場動畫
```tsx
import { motion, AnimatePresence } from 'framer-motion';
import { useState } from 'react';

function Modal() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <>
      <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>

      {/* AnimatePresence 讓條件渲染有退場動畫 */}
      <AnimatePresence>
        {isOpen && (
          <motion.div
            key="modal"
            initial={{ opacity: 0, scale: 0.9 }}
            animate={{ opacity: 1, scale: 1 }}
            exit={{ opacity: 0, scale: 0.9 }}   // exit 需要 AnimatePresence
            transition={{ duration: 0.2 }}
          >
            Modal Content
          </motion.div>
        )}
      </AnimatePresence>
    </>
  );
}
```

### Variants — 管理複雜多狀態動畫
```tsx
const containerVariants = {
  hidden: { opacity: 0 },
  visible: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1, // 子元素依序出現，間隔 0.1s
    },
  },
};

const itemVariants = {
  hidden: { opacity: 0, x: -20 },
  visible: { opacity: 1, x: 0 },
};

function List({ items }: { items: string[] }) {
  return (
    <motion.ul
      variants={containerVariants}
      initial="hidden"
      animate="visible"
    >
      {items.map((item) => (
        <motion.li key={item} variants={itemVariants}>
          {item}
        </motion.li>
      ))}
    </motion.ul>
  );
}
```

### useAnimation — 程式控制動畫
```tsx
import { motion, useAnimation } from 'framer-motion';

function ShakeOnError() {
  const controls = useAnimation();

  const handleError = async () => {
    await controls.start({
      x: [0, -10, 10, -10, 10, 0],
      transition: { duration: 0.4 },
    });
  };

  return (
    <motion.input
      animate={controls}
      onBlur={handleError}
    />
  );
}
```

### Layout Animation — 自動處理佈局變化
```tsx
// 當元素位置/大小改變時，自動補間動畫
<motion.div layout>
  {/* 內容改變時，位置會平滑過渡 */}
</motion.div>

// 共享 layoutId 讓兩個元素之間有連續動畫（Magic Motion）
{isExpanded ? (
  <motion.div layoutId="card" style={{ width: '100%' }} />
) : (
  <motion.div layoutId="card" style={{ width: '50px' }} />
)}
```

### Scroll 動畫
```tsx
import { motion, useScroll, useTransform } from 'framer-motion';

function ParallaxSection() {
  const { scrollY } = useScroll();
  // 將 scrollY 0~300 映射到 opacity 1~0
  const opacity = useTransform(scrollY, [0, 300], [1, 0]);

  return (
    <motion.div style={{ opacity }}>
      隨滾動淡出的區塊
    </motion.div>
  );
}
```

## Common Mistakes

### 1. 忘記加 `AnimatePresence` 就使用 `exit`
`exit` prop 只在被 `AnimatePresence` 包裹時才生效。

### 2. 動畫效能問題：避免 animate layout/width/height
```tsx
// 效能差：觸發 layout reflow
animate={{ width: '100%' }}

// 效能好：只觸發 GPU transform
animate={{ scaleX: 1 }}
```
優先使用 `transform`（scale, rotate, translate）和 `opacity`，它們走 GPU，不會 reflow。

### 3. `key` 不穩定導致動畫重複觸發
`AnimatePresence` 靠 `key` 識別組件，`key` 改變會觸發重新進場動畫。

### 4. 在 Server Component 中使用
Framer Motion 需要瀏覽器環境，使用的組件必須是 Client Component（加 `'use client'`）。

## Framer Motion vs CSS Animation vs GSAP

| 面向 | Framer Motion | CSS Animation | GSAP |
|------|--------------|---------------|------|
| 易用性 | 高（React 宣告式） | 中 | 低（命令式） |
| 複雜動畫 | 中 | 低 | 高 |
| Bundle 大小 | ~50KB | 0 | ~70KB |
| React 整合 | 原生 | 需要手動管理 | 需要 useEffect |
| 手勢支援 | 內建 | 無 | 需要額外設定 |

**結論**：React 專案首選 Framer Motion；超複雜時間軸動畫才考慮 GSAP。

## Related Concepts
- [[React.md]]
- [[CSS動畫]]
- [[React效能優化]]
- [[a11y無障礙設計]]

## Source Notes
- Framer Motion 官方文件：framer.com/motion
