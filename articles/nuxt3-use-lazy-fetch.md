---
title: "[和訳] Nuxt3 公式サイト~useLazyFetch"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト useLazyFetch](https://nuxt.com/docs/api/composables/use-lazy-fetch) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-lazy-fetch

# `useLazyFetch`
`useLazyFetch` は `lazy` オプションを `true` に設定することでハンドラが解決される前にナビゲーションをトリガーする `useFetch` 周りのラッパーを提供します。

## Description
デフォルトでは、`useFetch` は非同期関数が解決されるまでナビゲーションをブロックします。

> `useLazyFetch` は `useFetch` と同じシグネチャを持ちます。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-fetch

## Example
```Vue
<template>
  <div v-if="pending">
    Loading ...
  </div>
  <div v-else>
    <div v-for="post in posts">
      <!-- 何かする -->
    </div>
  </div>
</template>
<script setup>
/* ナビゲーションはフェッチが完了する前に発生します。
   保留状態やエラー状態をコンポーネントのテンプレート内で直接処理します。
*/
const { pending, data: posts } = useLazyFetch('/api/posts')
watch(posts, (newPosts) => {
  // ポストは null で始まるので、すぐにその内容にアクセスすることはできませんが、
  // 見ることはできます。
})
</script>
```
:::message
`useLazyFetch` はコンパイラによって変換される予約関数名なので、自分で作った関数に `useLazyFetch` と名付けるべきではありません。
:::

詳細は下記ページを参照してください。
https://nuxt.com/docs/getting-started/data-fetching#uselazyfetch