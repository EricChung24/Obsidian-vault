---
tags: [a11y, WCAG, 無障礙, 可及性, 前端]
created: 2026-04-19
---

# WCAG 與 a11y 實踐

> WCAG 是規範「無障礙的定義」，a11y 實踐是「怎麼達到它」。面試常考，也是真實工程責任。

→ [[前端工程師]]

---

## Summary

WCAG（Web Content Accessibility Guidelines）是 W3C 制定的網頁無障礙國際標準。
前端工程師最常接觸的是 **WCAG 2.1 AA 等級**，這也是多數政府與企業的最低合規要求。

---

## Plain Explanation

想像你的網站有使用者是：
- 看不清楚螢幕的視覺障礙者（用螢幕閱讀器）
- 手無法精確操作滑鼠的運動障礙者（只用鍵盤）
- 色盲使用者（紅綠不分）
- 認知障礙者（需要清楚的語言與操作流程）

a11y 就是讓這些使用者也能正常使用你的網站。

---

## WCAG 三個等級

| 等級 | 意義 | 實務要求 |
|------|------|---------|
| A | 最基本 | 不得有嚴重阻礙，如圖片沒有 alt |
| AA | 業界標準 | 對比度、鍵盤操作、表單錯誤提示等 |
| AAA | 最嚴格 | 手語影片、極高對比度等，一般不強求全站 |

目標：達到 **AA 等級**。

---

## POUR 四大原則（WCAG 核心）

| 原則 | 英文 | 說明 |
|------|------|------|
| 可感知 | Perceivable | 內容要能被感知，如圖片有 alt、影片有字幕 |
| 可操作 | Operable | 所有功能都能用鍵盤完成 |
| 可理解 | Understandable | 語言清楚、錯誤訊息明確 |
| 健壯性 | Robust | 能被各種輔助技術（螢幕閱讀器）正確解析 |

---

## 實踐 Checklist（AA 等級）

### 語意與結構
- [ ] 使用語意化 HTML（`<nav>`, `<main>`, `<button>`, `<label>`）而非全 `<div>`
- [ ] 每頁只有一個 `<h1>`，標題層級不跳級
- [ ] 表單的 `<input>` 都有對應的 `<label>`（或 `aria-label`）
- [ ] 圖片有 `alt`；裝飾性圖片使用 `alt=""`

### 鍵盤操作
- [ ] 所有互動元素（按鈕、連結、表單）可用 Tab 鍵聚焦
- [ ] 聚焦狀態有明顯的視覺樣式（不要用 `outline: none` 把它消掉）
- [ ] Modal / Dropdown 開啟後，焦點移到對話框內；關閉後，焦點回到觸發元素

### 色彩對比
- [ ] 一般文字對比度 ≥ 4.5:1（可用 Chrome DevTools 或 axe 工具檢查）
- [ ] 大文字（18px 以上）對比度 ≥ 3:1
- [ ] 不能只靠顏色傳遞訊息（如：錯誤狀態不能只靠紅色，要加圖示或文字）

### ARIA 使用
- [ ] 使用原生 HTML 能達到效果就不要用 ARIA
- [ ] 自訂互動元件（如 Tab 列、手風琴）要加正確的 `role` 和 `aria-*` 屬性
- [ ] 動態內容更新要用 `aria-live` 通知螢幕閱讀器

---

## 常見錯誤

```html
<!-- 錯誤：沒有 label -->
<input type="text" placeholder="請輸入姓名" />

<!-- 正確 -->
<label for="name">姓名</label>
<input id="name" type="text" placeholder="請輸入姓名" />
```

```html
<!-- 錯誤：用 div 做按鈕，沒有語意 -->
<div onclick="submit()">送出</div>

<!-- 正確：用 button，自帶鍵盤焦點與 Enter 觸發 -->
<button type="submit">送出</button>
```

```css
/* 錯誤：移除聚焦樣式 */
:focus { outline: none; }

/* 正確：自訂但保留視覺提示 */
:focus-visible { outline: 2px solid #005fcc; outline-offset: 2px; }
```

---

## 測試工具

| 工具 | 用途 |
|------|------|
| Chrome DevTools Accessibility 面板 | 查看元素的 a11y tree |
| axe DevTools（Chrome 擴充套件） | 自動偵測 WCAG 違規 |
| WAVE | 網頁整體無障礙報告 |
| NVDA / VoiceOver | 實際用螢幕閱讀器測試 |
| Colour Contrast Analyser | 顏色對比度檢查 |

---

## Related Concepts
- [[a11y無障礙設計]]
- [[前端資安]]
- [[HTML]]
- [[CSS]]

## Open Questions
- React 的焦點管理（`useRef` + `focus()`）最佳實踐是什麼？
- 無障礙測試要如何納入 CI 流程？（axe-core + Jest / Playwright）
