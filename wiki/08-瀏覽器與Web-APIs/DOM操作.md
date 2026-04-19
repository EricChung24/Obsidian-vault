---
tags: [瀏覽器, DOM, 事件]
created: 2026-04-16
---

# DOM 操作

> Document Object Model，JavaScript 操作網頁結構的介面。

← [[前端工程師]]

---

## 選取元素

```js
// 現代方法（推薦）
document.querySelector('.card')          // 第一個符合的元素
document.querySelectorAll('.card')       // NodeList（所有符合）

// 語意化選取
document.getElementById('app')
document.getElementsByClassName('btn')  // HTMLCollection（即時更新）
document.getElementsByTagName('input')

// 關聯選取
element.closest('.container')           // 往上找最近的祖先
element.parentElement
element.children                         // 子元素
element.firstElementChild
element.lastElementChild
element.nextElementSibling
element.previousElementSibling
```

---

## 操作元素

```js
// 內容
element.textContent = '純文字';        // 安全，不解析 HTML
element.innerHTML = '<b>粗體</b>';     // 解析 HTML，注意 XSS
element.innerText = '可見文字';        // 考慮 CSS 顯示狀態

// 屬性
element.setAttribute('data-id', '123');
element.getAttribute('data-id');        // '123'
element.removeAttribute('disabled');
element.hasAttribute('required');

// dataset
element.dataset.userId = '42';          // <div data-user-id="42">
const id = element.dataset.userId;

// class
element.classList.add('active', 'highlight');
element.classList.remove('inactive');
element.classList.toggle('open');
element.classList.contains('active');   // boolean
element.classList.replace('old', 'new');

// 樣式
element.style.color = 'red';
element.style.cssText = 'color:red; font-size:16px';
getComputedStyle(element).color;        // 取得實際計算後的樣式
```

---

## 建立與插入元素

```js
// 建立
const div = document.createElement('div');
div.className = 'card';
div.textContent = '新卡片';

// 插入
parent.appendChild(div);                // 加到末尾
parent.prepend(div);                    // 加到開頭
parent.insertBefore(div, referenceNode);
element.before(div);                    // 在元素前
element.after(div);                     // 在元素後

// 複製
const clone = element.cloneNode(true);  // true = 深複製

// 移除
element.remove();
parent.removeChild(element);

// DocumentFragment — 批次操作，減少重排
const fragment = document.createDocumentFragment();
items.forEach(item => {
  const li = document.createElement('li');
  li.textContent = item.name;
  fragment.appendChild(li);
});
list.appendChild(fragment);  // 只觸發一次 DOM 更新
```

---

## 事件處理

```js
// 添加/移除事件
const handler = (e) => console.log(e.target);
element.addEventListener('click', handler);
element.removeEventListener('click', handler);  // 需要同一個函式參照

// 事件物件
element.addEventListener('click', (e) => {
  e.preventDefault();     // 阻止預設行為（表單提交、連結跳轉）
  e.stopPropagation();    // 阻止冒泡
  e.target;               // 觸發事件的元素
  e.currentTarget;        // 綁定事件的元素（= this）
  e.clientX, e.clientY;  // 滑鼠座標
});

// 鍵盤事件
document.addEventListener('keydown', (e) => {
  if (e.key === 'Escape') closeModal();
  if (e.ctrlKey && e.key === 's') save();
});
```

---

## 事件委派（Event Delegation）

```js
// 代替為每個子元素綁定事件
// 動態新增的子元素也能響應

document.querySelector('.list').addEventListener('click', (e) => {
  const item = e.target.closest('.list-item');
  if (!item) return;

  const id = item.dataset.id;
  handleItemClick(id);
});
```

---

## IntersectionObserver（懶載入）

```js
const observer = new IntersectionObserver(
  (entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.classList.add('visible');
        observer.unobserve(entry.target);  // 只觸發一次
      }
    });
  },
  { threshold: 0.1 }  // 10% 進入視口才觸發
);

document.querySelectorAll('.lazy').forEach(el => observer.observe(el));
```

---

## MutationObserver

```js
const observer = new MutationObserver((mutations) => {
  mutations.forEach(mutation => {
    console.log('DOM 變更:', mutation.type);
  });
});

observer.observe(document.body, {
  childList: true,    // 子節點增刪
  subtree: true,      // 觀察整個子樹
  attributes: true,   // 屬性變更
});
```

---

## 相關連結

- [[JavaScript]] — 基礎知識
- [[前端資安]] — XSS 與 innerHTML
- [[渲染優化]] — 減少 DOM 操作
