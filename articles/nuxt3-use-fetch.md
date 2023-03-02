---
title: "[和訳] Nuxt3 公式サイト~useFetch"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
published_at: 2023-03-02 20:00
---
# この記事について
この記事は[Nuxt3 公式サイト useFetch](https://nuxt.com/docs/api/composables/use-fetch) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-fetch

# `useFetch`
このコンポーザブルは `useAsyncData` と `$fetch` まわりの便利なラッパー提供します。

URL と fetch オプションをもとにしてキーを自動的に生成し、サーバールートに基づいてリクエスト URL の型ヒントを提供し、API レスポンスの型を推論します。

## Type
```ts:Signature
function useFetch(
  url: string | Reequest | Ref<string | Request> | () => string | Request, options?: UseFetchOptions<DataT>
): Promise<AsyncData<DataT>>

type UseFetchOptions = {
  key?: string
  method?: string
  query?: SearchParams
  params?: SearchParams
  body?: RequestInit['body'] | Record<string, any>
  headers?: {key: string, value: string}[]
  baseURL?: string
  server?: boolean
  lazy?: boolean
  immediate?: boolean
  default?: () => DataT
  transform?: (input: DataT) => DataT
  pick?: string[]
  watch?: WatchScore[]
}

type AsyncData<DataT> = {
  data: Ref<DataT>
  pending: Ref<boolean>
  refresh: (opts?: {dedupe?: boolean}) => Promise<void>
  execute: () => Promise<void>
  error: Ref<Error | boolean>
}
```

## Params
- **URL**：取得する URL
- **Options ([unjs/ofetch](https://github.com/unjs/ofetch) と [AsyncDataOptions](https://nuxt.com/docs/api/composables/use-async-data#params) を拡張します)**
  - `method`：リクエストメソッド
  - `query`：[ufo](https://github.com/unjs/ufo) を使用して URL にクエリ検索パラメータを追加します
  - `params`：`query` のエイリアス
  - `body`：リクエストボディ - 自動的に文字列化されます（オブジェクトが渡された場合）
  - `headers`：リクエストヘッダ
  - `baseURL`：リクエストのベース URL 

全てのフェッチオプションは `computed` と `ref` 値を与えることができます。これらは監視され、更新されると、新しい値で自動的に新しいリクエストが作成されます。
- **Options (`useAsyncData` から)**
  - `key`：リクエスト間でデータ取得がきちんと重複しないようにするための一意のキーで、提供されない場合、`useAsyncData` が使用されている静的コードの場所に基づいて生成されます。
  - `server`：サーバーにデータを取り込むかどうか（デフォルトは `true`）
  - `default`：非同期関数を解決する前にデータのデフォルト値を設定するファクトリー関数 - `lazy: true` オプションで特に便利です。
  - `pick`：ハンドラ関数の結果からこの配列で指定されたキーだけをピックアップします。
  - `watch`：リアクティブソースの自動更新を監視します。
  - `transform`：解決後にハンドラ関数の結果を変更するために使用できる関数
  - `immediate`：`false` に設定すると、リクエストが即座に実行されるのを防ぎます（デフォルトは `true`）。

:::message
URL パラメータに関数や ref を提供したり、オプションパラメータの引数に関数を提供したりする場合、オプションが同じに見えても `useFetch` の呼び出しはコードベースの他の場所にあるほかの `useFetch` 呼び出しと一致しません。強制的にマッチさせたい場合は、オプションで独自のキーを指定することができます。
:::

## Return Values
- **data**：渡された非同期関数の結果
- **pending**：データがまだ取り込まれているかどうかを示す真偽値
- **refresh/execute**：ハンドラ関数が返すデータをリフレッシュするために使用できる関数
- **error**：データの取得が失敗した場合のエラーオブジェクト

デフォルトでは、Nuxt は再度実行できるようになる前に `refresh` が完了するまで待ちます。

:::message
サーバーでデータを取得していない場合（例えば、`server: false` の場合）、ハイドレーションが完了するまでデータ取得 *されません* 。つまり、`useFetch` を待っても、`<script setup>` の中ではデータは null のままです。
:::

## Example
```ts
const {data, pending, error, refresh} = await useFetch('https://api.nuxtjs.dev/mountains', {
  pick: ['title']
})
```
クエリ検索パラメータの追加：

`query` オプションを使用すると、クエリに検索パラメータを追加できます。このオプションは`unjs/ofetch` から拡張されたもので、URL を作成するために `unjs/ufo` を使用しています。オブジェクトは自動的に文字列化されます。

```ts
const param1 = ref('value1')
const { data, pending, error, refresh } = await useFetch('https://api.nuxtjs.dev/mountains', {
  query: { param1, param2: 'value2'}
})
```
結果は `https://api.nuxtjs.dev/mountains?param1=value1&param2=value2`

インターセプターの使用：
```ts
const { data, pending, error, refresh } = await useFetch('/api/auth/login', {
  onRequest({ request, options }) {
    // リクエストヘッダを設定する
    options.headers = options.headers || {}
    options.headers.authorization = '...'
  },
  onRequestError({ request, options, error }) {
    // リクエストエラーの処理
  },
  onResponse({ request, response, options }) {
    // 応答データの処理
    return response._data
  },
  onResponseError({ request, response, options }) {
    // レスポンスエラーの処理
  }
})
```
:::message
`useFetch` はコンパイラによって変換される予約関数名なので、自分で作った関数に `useFetch` と名付けるべきではありません。
:::

詳細は下記ページを参照してください。
https://nuxt.com/docs/getting-started/data-fetching

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/composables/use-fetch