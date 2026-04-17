---
tags: [框架, Nuxt, Vue, SSR]
created: 2026-04-16
---

# Nuxt

> Nuxt 是建立在 [[Vue]] 之上的全端框架，定位上很像 Vue 生態中的 Next.js，提供路由、SSR、SSG、API 與約定式結構。

> [[前端工程師]]

---

## 為什麼要用 Nuxt

如果你只用 Vue，通常還要自己組：

- 路由
- SSR / SSG
- API
- 專案結構

Nuxt 把這些整合起來，讓專案可以更快進入實作。

---

## 常見結構

```txt
nuxt-app/
  app.vue
  nuxt.config.ts
  pages/
    index.vue
    about.vue
    blog/
      [slug].vue
  components/
  composables/
  layouts/
  middleware/
  server/
    api/
  plugins/
```

---

## 常用資料取得方式

### `useFetch`

```vue
<script setup>
const { data, pending, error } = await useFetch('/api/posts');
</script>
```

### `useAsyncData`

```vue
<script setup>
const { data: user } = await useAsyncData('user', () =>
  $fetch('/api/users/1')
);
</script>
```

### `$fetch`

```vue
<script setup>
await $fetch('/api/submit', {
  method: 'POST',
  body: { name: 'Eric' },
});
</script>
```

---

## Server API

Nuxt 也支援在 `server/api` 內直接寫 API。

```ts
export default defineEventHandler(async () => {
  return { ok: true };
});
```

---

## 什麼情況適合 Nuxt

- 你本來就用 Vue
- 需要 SSR / SSG
- 需要更完整的專案約定
- 希望前後端整合更順

---

## 相關連結

- [[Vue]]
- [[Pinia]]
- [[HTTP協議]]
- [[SEO優化]]
