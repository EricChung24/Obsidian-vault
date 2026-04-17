---
tags: [框架, Vue, Composition API]
created: 2026-04-16
---

# Vue

> Vue 是一個以元件為核心的前端框架，特色是上手快、模板直觀，Vue 3 之後以 Composition API 為主流寫法。

> [[前端工程師]]

---

## 核心概念

Vue 的重點可以先抓這幾個：

- 宣告式模板
- 元件化開發
- 響應式資料
- Composition API

---

## Composition API

```vue
<script setup lang="ts">
import { ref, reactive, computed, watch, onMounted } from 'vue';

const count = ref(0);
const state = reactive({
  users: [] as string[],
  loading: false,
});

const doubled = computed(() => count.value * 2);

watch(count, (newValue, oldValue) => {
  console.log(oldValue, newValue);
});

onMounted(() => {
  console.log('mounted');
});
</script>
```

### 什麼時候用 `ref`

- 基本型別
- 單一值
- 需要 `.value` 操作的情況

### 什麼時候用 `reactive`

- 物件或陣列
- 想直接用屬性操作

---

## 元件通訊

### props

父傳子最基本的方式。

### emit

子元件通知父元件。

### provide / inject

適合跨層級資料傳遞，但不要濫用。

---

## `computed` 與 `watch`

### `computed`

適合根據現有資料推導出新值。

### `watch`

適合監聽資料變化後做副作用，例如：

- 打 API
- 寫入 localStorage
- 同步路由參數

不要把可以用 `computed` 解決的事情寫成 `watch`。

---

## Vue 3 常見實務寫法

### `script setup`

現在 Vue 3 最常見的寫法就是 `script setup`，因為更簡潔，也更適合搭配 TypeScript。

### composables

如果有重複邏輯，可以抽成 composable，例如：

- `useFetchUsers`
- `useTheme`
- `usePagination`

---

## 什麼情況適合 Vue

- 想快速建立中小型前端專案
- 團隊偏好模板式語法
- 需要平滑導入現有頁面

---

## 相關連結

- [[Nuxt]]
- [[Pinia]]
- [[TypeScript]]
- [[表單處理]]
