---
title: "[和訳] Nuxt3 公式サイト~Transitions"
emoji: "🗂"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxt3","nuxtjs"]
published: true
published_at: 2023-01-25 09:00
---
# この記事について
この記事は[Nuxt3 公式サイト Transitions](https://nuxt.com/docs/getting-started/transitions) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/getting-started/transitions
所々自分の理解のために参考になりそうな別サイトのリンクを貼ってあります。

# Transitions
Nuxt は Vue の `<Transition>` コンポーネントを利用してページとレイアウト間の遷移を適用します。

## Page transitions
ページの遷移を有効にすると、全てのページで自動遷移を適用できます。
```ts:nuxt.config.ts
export default defineNuxtConfig({
  app: {
    pageTransition: { name: 'page', mode: 'out-in' }
  },
})
```
:::message
ページだけでなくレイアウトも変更する場合、ここで設定したページ遷移は実行されません。代わりに、レイアウト遷移を設定する必要があります。
:::

ページ間の遷移を追加するには、`app.vue` に次の CSS を追加します。
```Vue:app.vue
<template>
  <NuxtPage />
</template>
<style>
.page-enter-active,
.page-leave-active {
  transition: all 0.4s;
}
.page-enter-from,
.page-leave-to {
  opacity: 0;
  filter: blur(1rem);
}
</style>
```
```Vue:pages/index.vue
<template>
  <div>
    <h1>Home page</h1>
    <NuxtLink to="/about">About page</NuxtLink>
  </div>
</template>
```
```Vue:pages/about.vue
<template>
  <div>
    <h1>About page</h1>
    <NuxtLink to="/">Home page</NuxtLink>
  </div>
</template>
```
※公式サイトでは、上記設定を適用させた際の遷移の様子が動画で見られます。

---

ページごとに異なる遷移を設定するには、ページの `definePageMeta` に `pageTransition` キーを設定します。
```Vue:pages/about.vue
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'rotate'
  }
})
</script>
```
```Vue:app.vue
<template>
  <NuxtPage />
</template>

<style>
/* ... */
.rotate-enter-active,
.rotate-leave-active {
  transition: all 0.4s;
}
.rotate-enter-from,
.rotate-leave-to {
  opacity: 0;
  transform: rotate3d(1, 1, 1, 15deg);
}
</style>
```
※公式サイトでは、上記設定を適用させた際の遷移の様子が動画で見られます。


## Layout transitions
レイアウトトランジションを有効にすると、全てのレイアウトに自動トランジションが適用されます。
```ts:nuxt.config.ts
export default defineNuxtConfig({
  app: {
    layoutTransition: { name: 'layout', mode: 'out-in' }
  },
})
```
ページとレイアウト間の遷移を追加するには、以下の CSS を `app.vue` に追加してください。
```Vue:app.vue
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>

<style>
.layout-enter-active,
.layout-leave-active {
  transition: all 0.4s;
}
.layout-enter-from,
.layout-leave-to {
  filter: grayscale(1);
}
</style>
```
```Vue:layouts/default.vue
<template>
  <div>
    <pre>default layout</pre>
    <slot />
  </div>
</template>

<style scoped>
div {
  background-color: lightgreen;
}
</style>
```
```Vue:layouts/orange.vue
<template>
  <div>
    <pre>orange layout</pre>
    <slot />
  </div>
</template>

<style scoped>
div {
  background-color: #eebb90;
  padding: 20px;
  height: 100vh;
}
</style>
```
```Vue:pages/index.vue
<template>
  <div>
    <h1>Home page</h1>
    <NuxtLink to="/about">About page</NuxtLink>
  </div>
</template>
```
```Vue:pages/about.vue
<script setup lang="ts">
definePageMeta({
  layout: 'orange'
})
</script>

<template>
  <div>
    <h1>About page</h1>
    <NuxtLink to="/">Home page</NuxtLink>
  </div>
</template>
```
※公式サイトでは、上記設定を適用させた際の遷移の様子が動画で見られます。

---

`pageTransition` と同様に、`definePageMeta` を使用して、カスタムした `layoutTransition` をページコンポーネントに適用することができます。
```Vue:pages/about.vue
<script setup lang="ts">
definePageMeta({
  layout: 'orange',
  layoutTransition: {
    name: 'slide-in'
  }
})
</script>
```

## Global settings
これらのデフォルトのトランジション名は、`nuxt.config` を使用してグローバルにカスタマイズすることができます。

`pageTransition` と `layoutTransition` の両キーは、カスタム CSS トランジションの名前、モード、その他の有効なトランジションプロパティを渡す `TransitionProps` をシリアライズ可能な JSON の値として受け取ります。
```ts:nuxt.config.ts
export default defineNuxtConfig({
  app: {
    pageTransition: {
      name: 'fade',
      mode: 'out-in' // default
    },
    layoutTransition: {
      name: 'slide',
      mode: 'out-in' // default
    }
  }
})
```
`name` プロパティを変更した場合、それに応じて CSS のクラス名も変更する必要があります。

グローバルに設定した遷移プロパティを上書きするには、`defineMetaPage` を使用して単一の Nuxt のページまたはレイアウトを定義し、`nuxt.config` ファイルでグローバルに定義されているページまたはレイアウト遷移を全て上書きしてください。
```Vue:pages/some-page.vue
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'bounce',
    mode: 'out-in' // default
  }
})
</script>
```

## Disable transitions
`pageTransition` と `layoutTransition` は特定のルートに対して無効にすることができます。
```Vue:pages/some-page.vue
<script setup lang="ts">
definePageMeta({
  pageTransition: false
  layoutTransition: false
})
</script>
```
グローバルに無効にしたいときは下のようにします。
```ts:nuxt.config.ts
defineNuxtConfig({
  app: {
    pageTransition: false,
    layoutTransition: false
  }
})
```

## JavaScript Hooks
発展的な使用例として、JavaScript hook を使用して高度に動的なカスタムトランジションを Nuxt ページに作成することができます。

この方法は GSAP や Tween.js のようなJavaScript アニメーションライブラリの完璧な使用方法を提供します。
https://greensock.com/gsap/
https://github.com/tweenjs/tween.js/

```Vue:pages/some-page.vue
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'custom-flip',
    mode: 'out-in',
    onBeforeEnter: (el) => {
      console.log('Before enter...')
    },
    onEnter: (el, done) => {},
    onAfterEnter: (el) => {}
  }
})
</script>
```
詳細は下記ページを参照してください。
https://vuejs.org/guide/built-ins/transition.html#javascript-hooks

## Dynamic transitions
条件付きのロジックで動的な遷移を適用するために、インラインミドルウェアを活用して、`to.meta.pageTransition` に異なる遷移名を割り当てることができます。
```Vue:pages/[id].vue
<script setup lang="ts">
definePageMeta({
  pageTransition: {
    name: 'slide-right',
    mode: 'out-in'
  },
  middleware (to, from) {
    to.meta.pageTransition.name = +to.params.id > +from.params.id ? 'slide-left' : 'slide-right'
  }
})
</script>

<template>
  <h1>#{{ $route.params.id }}</h1>
</template>

<style>
.slide-left-enter-active,
.slide-left-leave-active,
.slide-right-enter-active,
.slide-right-leave-active {
  transition: all 0.2s;
}
.slide-left-enter-from {
  opacity: 0;
  transform: translate(50px, 0);
}
.slide-left-leave-to {
  opacity: 0;
  transform: translate(-50px, 0);
}
.slide-right-enter-from {
  opacity: 0;
  transform: translate(-50px, 0);
}
.slide-right-leave-to {
  opacity: 0;
  transform: translate(50px, 0);
}
</style>
```
```Vue:layouts/default.vue
<script setup lang="ts">
const route = useRoute()
const id = computed(() => Number(route.params.id || 1))
const prev = computed(() => '/' + (id.value - 1))
const next = computed(() => '/' + (id.value + 1))
</script>

<template>
  <div>
    <slot />
    <div v-if="$route.params.id">
      <NuxtLink :to="prev">⬅️</NuxtLink> |
      <NuxtLink :to="next">➡️</NuxtLink>
    </div>
  </div>
</template>
```
前の id に移動するときには `slide-left`、次の id に遷移するときには `slide-right` という遷移が適用されるようになります。
※公式サイトでは、上記設定を適用させた際の遷移の様子が動画で見られます。

## Transition with NuxtPage
`app.vue` で `<NuxtPage />` を使用する場合、遷移に関するプロパティをとしてコンポーネントのプロパティとして直接渡すことで、グローバルな遷移を有効にできます。
```Vue:app.vue
<template>
  <div>
    <NuxtLayout>
      <NuxtPage :transition="{
        name: 'bounce',
        mode: 'out-in'
      }" />
    </NuxtLayout>
  </div>
</template>
```
:::message
このページ遷移は、個々のページの `definePageMeta` で上書きできないことに注意してください。
:::