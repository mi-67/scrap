---
title: "[和訳] Nuxt3 公式サイト~$fetch"
emoji: "📌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は [Nuxt3 公式サイト $fetch](https://nuxt.com/docs/api/utils/dollarfetch) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/utils/dollarfetch

# `$fetch`
Nuxt は Vue アプリ内や API ルートで HTTP リクエストを作成する `$fetch` ヘルパーをグローバルに公開するために ofetch を使用します。

サーバーサイドレンダリング中に `$fetch` を呼び出して内部 API ルートをフェッチすると、関連する関数が直接呼び出されて（リクエストをエミュート）、**追加の API 呼び出しが節約されます。**

しかし、コンポーネントで `$fetch` を `useAsyncData` でラップせずに使用すると、最初にサーバーで、次にハイドレーション中にクライアントサイドでデータを2回フェッチします。これは `$fetch` が状態をサーバーからクライアントに転送しないためです。このように、クライアントがデータを再度取得する必要があるため、フェッチは両側で実行されます。

コンポーネントデータのフェッチ時に二重のデータフェッチングを防ぐために `useFetch` または `useAsyncData` + `$fetch` を使用することをお勧めします。

```Vue
<script setup>
// SSR 中はサーバーとクライアントで2回データを取得します。
const dataTwice = await $fetch("/api/item)

// SSR 中は、サーバー側でのみデータを取得し、クライアントに転送します。
const { data } = await useAsyncData('item', () => $fetch('/api/item'))

// useAsyncData + $fetch のショートカットとして useFetch を使用できます。
const { data } = await useFetch('/api/item')
</script>
```

詳細は下記ページを参照してください。
https://nuxt.com/docs/getting-started/data-fetching

クライアントサイドでのみ実行されるメソッドには `$fetch` を使用できます。

```Vue
<script setup>
function contactForm() {
  $fetch('/api/contact', {
    method: 'POST',
    body: { hello: 'world '}
  })
}
</script>

<template>
  <button @click="contactForm">Contact</button>
</template>
```

`$fetch` は Nuxt2 用に作成された @nuxt/http と @nuxtjs/axios の代わりに Nuxt で HTTP 呼び出しを行うのに好ましい方法です。