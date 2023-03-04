---
title: "[和訳] Nuxt3 公式サイト~useNuxtApp"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト useNuxtApp](https://nuxt.com/docs/api/composables/use-nuxt-app) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-nuxt-app

# `useNuxtApp`
`useNuxtApp` はクライアントサイドとサーバーサイドの両方で利用可能な Nuxt の共有ランタイムコンテキストにアクセスする方法を提供する組み込みコンポーザブルです。Vue アプリのインスタンス、ランタイムフック、ランタイム変数設定、`ssrContext` や `payload`などの内部状態へのアクセスを支援します。

`useNuxtApp()` はコンポーザブル、プラグイン、コンポーネント内で使用できます。

```Vue:app.vue
<script setup>
  const nuxtApp = useNuxtApp()
</script>
```

## Methods
### `provide(name, value)`
`nuxtApp` は Nuxt プラグインを使用して拡張できるランタイムコンテキストです。`provide` 関数を使用して Nuxt プラグインを作成し、全てのコンポーザブルとコンポーネントにわたって Nuxt アプリで値やヘルパーメソッドを利用できるようにします。

`provide` 関数は `name` と `value` パラメータを受け付けます。

**例**：
```ts
const nuxtApp = useNuxtApp()
nuxtApp.provide('hello', (name) => `Hello ${name}!`)

// "Hello name!" を印字
console.log(nuxtApp.$hello('name'))
```

上の例でわかるように、`$hello` は `nuxtApp` コンテキストの新しいカスタム部分になり、`nuxtApp` がアクセスできる全ての場所で利用可能になりました。

### `hook(name, cb)`
`nuxtApp` で利用可能なフックでは、Nuxt アプリケーションのランタイム面をカスタマイズできます。レンダリングライフサイクルにフックするために Vue.js のコンポーザブルと Nuxt プラグインでランタイムフックを使用できます。

`hook` 関数は特定のポイントでレンダリングライフサイクルにフックすることによってカスタムロジックを追加するのに便利です。`hook` 関数は主に Nuxt プラグインを作成する際に使用されます。

Nuxt が呼び出す利用可能なランタイムフックについては[ランタイムフック](https://nuxt.com/docs/api/advanced/hooks#app-hooks-runtime)を参照してください。
https://nuxt.com/docs/api/advanced/hooks#app-hooks-runtime

```ts:plugins/test.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.hook('page:start', () => {
    /* コードはここに書かれています */
  })
  nuxtApp.hook('vue:error',(..._args) => {
    console.log('vue:error')
    // if (process.client) {
    //   console.log(..._args)
    // }
  })
})
```

### `callhook(name, ...args)`
`callhook` は既存のフックのいずれかで呼ばれたときに Promise を返します。

```ts
await nuxtApp.callHook('my-plugin:init')
```

## Properties
`useNuxtApp()` はアプリの拡張やカスタマイズ、状態、データ、変数の共有をするために使用できる以下のプロパティを公開します。

### `vueApp`
`vueApp` は `nuxtApp` を通してアクセスできるグローバルな Vue.js アプリケーションインスタンスです。便利なメソッドをいくつか紹介します。
- **component()** - 名前の文字列とコンポーネント定義を両方渡した場合、グローバルコンポーネントを登録し、名前のみを渡した場合、既に登録されているものを取得します。
- **directive()** - 名前の文字列とディレクティブ定義を両方渡した場合、グローバルなカスタムディレクティブ登録し、名前のみを渡した場合、既に登録されているものを取得します（[例](https://nuxt.com/docs/guide/directory-structure/plugins#vue-directives)）。
- **use()** - Vue.js プラグインをインストールします（[例](https://nuxt.com/docs/guide/directory-structure/plugins#vue-plugins)）。

詳細は下記ページを参照してください。
https://vuejs.org/api/application.html#createapp

### `ssrContext`
`ssrContext` はサーバーサイドレンダリング時に生成され、サーバーサイドでのみ利用可能です。Nuxt は `ssrContext` を通じて以下のプロパティを公開します。
- **url** (string) - 現在のリクエスト URL です。
- **event** (unjs/h3 リクエストイベント) - 現在のリクエストの `req` オブジェクトと `res` オブジェクトにアクセスします。
- **payload** (object) - NuxtApp の ペイロードオブジェクトです。

### `payload`
`payload` はサーバーサイドからクライアントサイドにデータと状態変数を公開し、ブラウザからアクセエスできる `window._NUXT_` オブジェクトで利用可能にします。

`payload` は以下のキーを文字列化し、サーバーサイドから渡された後にクライアントサイドで公開します。

- **serverRendered** (boolean) - レスポンスがサーバーサイドレンダリングかどうかを示します。

- **data** (object) - `useFetch` または `useAsyncData` を使用して API エンドポイントからデータを取得した場合、結果のペイロードは `payload.data` からアクセスすることができます。このデータはキャッシュされ、同一のリクエストが複数回行われた場合に、同じデータを取得するのを防ぐのに役立ちます。

```Vue:app.vue
export default defineComponent({
  async setup() {
    const { data } = await useAsyncData('count', () => $fetch('/api/count'))
  }
})
```

上記の例で、`useAsyncData` を使用して `count` の値を取得した後、`payload.data` にアクセスすると `{count: 1}` が記録されています。`count` の値は、ページカウントが増えるたびに更新されます。

ssrcontext から同じ `payload.data` にアクセスする場合、サーバーサイドでも同じ値にアクセスすることができます。

- **state** (object) - Nuxt で `useState` コンポーザブルを使用して共有状態を設定すると、この状態は `payload.state.[name-of-your-state]` を通じてアクセスされます。

```ts:plugins/my-plugin.ts
export const useColor = () => useState<string>('color', () => 'pink')

export default defineNuxtPlugin((nuxtApp) => {
  if(process.server) {
    const color = useColor()
  }
})
```

### `isHydrating`
Nuxt アプリがクライアントサイドでハイドレートしているかどうかをチェックするために `nuxtApp.isHydrating` (boolean) を使用します。

**例**：
```ts:components/nuxt-error-boundary.md
export default defineComponent({
  setup(_props, { slots, emit }) {
    const nuxtApp = useNuxtApp()
    onErrorCaptured((err) => {
      if(process.client && !nuxtApp.isHydrating) {
        // ...
      }
    })
  }
})
```