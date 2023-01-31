---
title: "[和訳] Nuxt3 公式サイト~Error Handling"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxt3","nuxtjs"]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Error Handling](https://nuxt.com/docs/getting-started/error-handling) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/getting-started/error-handling

# Error Handling
ライフサイクルに応じたエラーキャッチ方法を学びます。

## Handling Errors
Nuxt3 はフルスタックフレームワークであり、それはさまざまな文脈で起こりうる、防止できないユーザー実行時エラーの原因がいくつかあることを意味します。
1. Vue のレンダリングライフサイクル（SSR + SPA）中のエラー
2. API または Nitro サーバのライフサイクル中のエラー
3. サーバおよびクライアントの起動エラー（SSR + SPA）

### Error During the Vue Rendering Lifecycle (SSR + SPA)
Vue のエラーは `onErrorCaptured` を使用してフックすることができます。

加えて、Nuxt は `vue:error` フックを提供し、何らかのエラーがトップレベルまで伝播した際に呼び出せるようになっています。

エラー報告フレームワークを使用している場合、`vueApp.config.errorHandler` を通じてグローバルハンドラを提供することができます。これは、たとえ処理されたとしても全ての Vue エラーを受け取ります。

#### Example With Global Error Reporting Framework
```ts
export default defineNuxtPlugin((nuxtApp) => {
    nuxtApp.vueApp.config.errorHandler = (error, context) => {
        // ...
    }
})
```
### Server and Client Startup Errors (SSR + SPA)
Nuxt は Nuxt のアプリケーション起動時に何らかのエラーが発生した場合、`app:error` フックを呼び出します。

これには以下が含まれます。
- Nuxt プラグインの実行
- `app:create` と `app:beforeMount` フックの処理
- アプリのマウント（クライアント側）
  - ただし、この場合は `onCreateCaptured` または `vue:error` で処理する必要があります
- `app:mounted` フックの処理

### Errors During API or Nitro Server Lifecycle
現在、これらのエラーに対するサーバーサイドの処理を定義できませんが、エラーページをレンダリングすることは可能です（次のセクションを参照してください）。

## Rendering an Error Page
Nuxt が致命的なエラーに遭遇した場合、サーバーのライフサイクル中であれ、Vue アプリケーション（SSR と SPA の両方）のレンダリング中であれ、JSON レスポンス（`Accept:application/json` ヘッダーでリクエストした場合）または HTML のエラーページのいずれかをレンダリングします。

このエラーページはアプリケーションのソースディレクトリに`app.vue` と一緒に `~/error.vue` を加えることでカスタマイズできます。このページはあなたが処理するエラーを含む単一のプロパティ `error` を持っています。

エラーページを削除する準備ができたら、オプションでリダイレクト先（例えば「安全」なページに移動させたい、など）のパスを受け取る `clearError` ヘルパー関数呼ぶことができます。

:::message
Nuxt のプラグインに依存するもの、例えば `$route` や `useRouter` はプラグインがエラーを吐いたときにそのエラーが解消されるまで再実行されないので、使う前に必ず確認してください。
:::

### Example
```Vue:error.vue
<template>
  <button @click="handleError">Clear errors</button>
</template>

<script setup>
const props = defineProps({
  error: Object
})

const handleError = () => clearError({ redirect: '/' })
</script>
```

## Error Helper Methods
### `useError`
```ts
function useError(): Ref<Error | { url, statusCode, statusMessage, message, description, data }>
```
この関数は処理中のグローバルな Nuxt エラーを返します。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-error

### `createError`
```ts
function createError (err: { cause, data, message, name, stack, statusCode, statusMessage, fatal }): Error
```
メタデータを持つエラーオブジェクトを作成するためにこの関数を使うことができます。アプリの Vue と Nitro の両方で使用でき、スローされることを意味しています。

`createError` で作成したエラーを投げるケース
- サーバーサイドで、`clearError` で削除できるフルスクリーンのエラーページをトリガーするとき。
- クライアントサイドで、自分で処理する必要のある致命的ではないエラーを投げるとき。フルスクリーンエラーページを表示させたいときは、`fatal: true` を設定すれば可能です。

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

### `showError`
```ts
function showError (err: string | Error | {statusCode, statusMessage}): Error
```
この関数はクライアントサイドの任意の場所で呼ぶことができますし、（サーバーサイドでは）ミドルウェア、プラグイン、`setup()` 関数の中で直接呼ぶこともできます。この関数は `clearError` で削除できるフルスクリーンのエラーページをトリガーします。

代わりに `throw createError()` を使用することが推奨されます。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/utils/show-error

### `clearError`
```ts
function clearError (options?: { redirect?: string }): Promise<void> 
```
この関数は現在処理されている Nuxt のエラーを削除します。また、オプションでリダイレクト先のパス（例えば「安全」なページに移動させたい、など）を受け取ります。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/utils/clear-error

## Rendering Errors Within Your App
また、Nuxt はサイト全体をエラーページで置き換えることなくクライアントサイドのエラーをアプリ内で処理できる `<NuxtEErrorBoundary>` コンポーネントを提供します。

このコンポーネントはデフォルトのスロット内で起きたエラーを処理する責任があります。クライアントサイドでは、エラーがトップレベルまで伝播するのを防ぎ、代わりに `#error` スロットをレンダリングします。

`#error` スロットは `error` をプロパティとして受け取ります（`error = null` とした場合、デフォルトのスロットを再レンダリングすることになります。エラーが完全に解消していないと、エラースロットが2回レンダリングされるだけです）。

別のルートに移動すると、エラーは自動的に解消されます。

### Example
```Vue:pages/index.vue
<template>
  <!-- 何らかのコンテンツ -->
  <NuxtErrorBoundary @error="someErrorLogger">
    <!-- コンテンツをレンダリングするために、デフォルトスロットを使用します -->
    <template #error="{ error }">
      You can display the error locally here.
      <button @click="error = null">
        This will clear the error.
      </button>
    </template>
  </NuxtErrorBoundary>
</template>
```

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/app/error-handling
