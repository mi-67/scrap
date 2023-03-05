---
title: "[和訳] Nuxt3 公式サイト~useRoute"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト useRoute](https://nuxt.com/docs/api/composables/use-route) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-route

# `useRoute`
`useRoute` コンポーザブルは現在のルートを返すので、セットアップ関数、プラグイン、ルートミドルウェアの中で呼び出す必要があります。

Vue コンポーネントのテンプレート内では、`$route` を使用してルートにアクセスできます。

## Example
以下の例では、URL の一部として動的ページパラメータである `slug` を使用して `useFetch` 経由で API を呼びます。

```Vue:~/pages/[slug].vue
<script setup>
const route = useRoute()
const { data: mountain } = await useFetch(`https://api.nuxtjs.dev/mouuntains/${route.params.slug}`)
</script>

<template>
  <div>
    <h1>{{ mountain.title }}</h1>
    <p>{{ mountain.description }}</p>
  </div>
</template>
```

ルートクエリパラメータにアクセスする必要がある場合（例えば、パス `/test?example=true` の `example`）、`useRoute().params` の代わりに `useRoute().query`を使用できます。

動的パラメータとクエリパラメータとは別に、`useRoute()` は現在のルートに関連する以下のコンピューティッドリファレンスも提供します。
- **fullPath**：パス、クエリ、ハッシュを含む現在のルートに関連するエンコードされた URL
- **hash**：# で始まる URL のハッシュ部分をデコードしたもの
- **matched**：現在のルートロケーションが正規化されたマッチングルートの配列
- **meta**：レコードに添付されるカスタムデータ
- **name**：ルートレコードの一意な名前
- **path**：URL のエンコードされたパス名部分
- **redirectedFrom**：現在のルートロケーションに到達する前にアクセスしようとしたルートロケーション

詳細は下記ページを参照してください。
https://router.vuejs.org/api/interfaces/RouteLocationNormalizedLoaded.html