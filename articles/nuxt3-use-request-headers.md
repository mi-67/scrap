---
title: "[和訳] Nuxt3 公式サイト~useRequestHeaders"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト useRequestHeaders](https://nuxt.com/docs/api/composables/use-request-headers) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-request-headers

# `useRequestHeaders`
組み込みの `useRequestHeaders` コンポーザブル使用すると、ページ、コンポーネント、プラグイン内で受信するリクエストヘッダーにアクセスすることができます。

```ts
// 全てのリクエストヘッダーを取得する
const headers = useRequestHeaders()

// cookie のリクエストヘッダのみを取得する
const headers = useRequestHeaders(['cookie'])
```
ブラウザでは、`useRequestHeaders` は空のオブジェクトを返します。

## Example
`useRequestHeaders` を使用し、SSR 中の最初のリクエストの `authorization` ヘッダーにアクセスし、今後の内部リクエストにプロキシすることができます。

下の例は、`authorization` リクエストヘッダーを同型の `$fetch` 呼び出しに追加します。

```Vue:pages/some-page.vue
<script setup>
const { data } = await useFetch('/api/confidential', {
  headers: useRequestHeaders(['authorization'])
})
</script>
```

[別の例](https://nuxt.com/docs/getting-started/data-fetching#example-pass-client-headers-to-the-api)では、最初のリクエストから別の API ルートに cookie を渡す方法を示しています。
https://nuxt.com/docs/getting-started/data-fetching#example-pass-client-headers-to-the-api