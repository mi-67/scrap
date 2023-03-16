---
title: "[和訳] Nuxt3 公式サイト~createError"
emoji: "😸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は [Nuxt3 公式サイト createError](https://nuxt.com/docs/api/utils/create-error) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/utils/create-error

# `createError`
追加のメタデータを持つエラーオブジェクトを作成するためにこの関数を使用できます。アプリの Vue と Nitro の両方の部分で使用可能で、エラーが投げられることを意味しています。

**Parameters**：
- err: {cause, data, message, name, stack, statusCode, statusMessage, fatal}

## Throwing Errors in Your Vue App
`createError` で作成されたエラーを投げる場合
- サーバーサイドでは、全画面のエラーページをトリガーし、`clearError` で消すことができます。
- クライアントサイドでは、致命的ではないエラーを投げ、処理することができます。全画面のエラーページをトリガーする必要がある場合、`fatal: true` を設定することで対応できます。

### Example
```Vue:pages/movies/[slug].vue
<script setup>
const route = useRoute()
const { data } = await useFetch(`/api/movies/${route.params.slug}`)
if (!data.value) {
  throw createError({ statusCode: 404, statusMessage: 'Page Not Found' })
}
</script>
```

## Throwing Errors in API Routes
サーバー API ルートでエラー処理をトリガーするために `createError` を使用します。

### Example
```ts
export default eventHandler(() => {
  throw createError({
    statusCode: 404,
    statusMessage: 'Page Not Found'
  })
})
```
詳細は下記ページを参照してください。
https://nuxt.com/docs/getting-started/error-handling