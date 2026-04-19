---
tags: [狀態管理, Pinia, Vue]
created: 2026-04-16
---

# Pinia

> Pinia 是 Vue 3 官方推薦的狀態管理方案。它比舊版 Vuex 更直覺，也更適合搭配 Composition API 與 TypeScript。

> [[前端工程師]]

---

## 為什麼用 Pinia

如果狀態只在單一元件內使用，直接用 `ref` 或 `reactive` 就夠了。

Pinia 比較適合：

- 多個元件共用狀態
- 使用者資訊、權限、主題這類全域資料
- 想把資料邏輯從元件抽出去

---

## 基本寫法

```ts
import { defineStore } from 'pinia';
import { computed, ref } from 'vue';

export const useUserStore = defineStore('user', () => {
  const users = ref<{ id: number; name: string; active: boolean }[]>([]);
  const loading = ref(false);

  const activeUsers = computed(() =>
    users.value.filter(user => user.active)
  );

  async function fetchUsers() {
    loading.value = true;
    users.value = await $fetch('/api/users');
    loading.value = false;
  }

  return {
    users,
    loading,
    activeUsers,
    fetchUsers,
  };
});
```

---

## 元件中使用

```vue
<script setup lang="ts">
import { onMounted } from 'vue';
import { storeToRefs } from 'pinia';
import { useUserStore } from '@/stores/userStore';

const userStore = useUserStore();
const { users, loading, activeUsers } = storeToRefs(userStore);

onMounted(() => {
  userStore.fetchUsers();
});
</script>
```

### 為什麼用 `storeToRefs`

如果你直接解構 store，響應式可能會丟掉。

所以：

- 狀態值用 `storeToRefs`
- action 直接從 store 拿

---

## Pinia 的優點

- API 簡單
- 跟 Vue 3 寫法一致
- TypeScript 友善
- 不像舊版 Vuex 那麼多 boilerplate

---

## 常見誤區

### 所有資料都丟進 Pinia

不對。局部 UI 狀態很多時候留在元件內更合理。

### Pinia 可以取代所有 API 資料管理

不完全。如果是 server state，還是要考慮你的資料快取策略。

---

## 什麼情況適合 Pinia

- Vue 3 專案
- 多元件共用狀態
- 想把邏輯從元件抽離

---

## 相關連結

- [[Vue]]
- [[Nuxt]]
- [[表單處理]]
