---
title: "[和訳] Nuxt3 公式サイト~useAsyncData"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト useAsyncData](https://nuxt.com/docs/api/composables/use-async-data) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-async-data

# `useAsyncData`
ページ、コンポーネント、プラグイン内で、useAsyncData を使用して、非同期に解決されるデータにアクセスすることができます。

## Type
```ts:Signature
function useAsyncData(
  handler: (nuxtApp?: NuxtApp) => Promise<DataT>,
  options?: AsyncDataOptions<DataT>
): AsyncData<DataT>

function useAsyncData(
  key: string,
  handler: (nuxtApp?: NuxtApp) => Promise<DataT>,
  options?: AsyncDataOptions<DataT>
): Promise<AsyncData<DataT>>

type AsyncDataOptions<DataT> = {
  server?: boolean
  lazy?: boolean
  default?: () => DataT | Ref<DataT> | null
  transform?: (input: DataT) => DataT
  pick?: string[]
  watch?: WatchScore[]
  immediate?: boolean
}

interface RefreshOptions {
  dedupe?: boolean
}

type AsyncData<DataT, ErrorT> = {
  data: Ref<DataT | null>
  pending: Ref<boolean>
  execute: () => Promise<void>
  refresh: (opts?: RefreshOptions) => Promise<void>
  error: Ref<ErrorT | null>
}
```

## Params
- **key**：リクエスト間でデータの取得が適切に重複しないようにする一意のキー。キーを与えなかった場合、`useAsyncData` のインスタンスのファイル名と行番号に対応する一意のキーが生成されます。
- **handler**：値を返す非同期関数です。
- **options**：
  - lazy：クライアントサイドのナビゲーションをブロックするの代わりに、ルートをロードしたあとに非同期関数を解決するかどうか（デフォルトは `false` ）
  - default：非同期関数を解決する前に、データのデフォルト値を設定するためのファクトリー関数　- `lazy: true` オプションで特に便利です。
  - server：サーバーでデータを取得するかどうか（デフォルトは `true` ）
  - transform：解決した後にハンドラ関数の結果を変更するために使用できる関数
  - pick：ハンドラ関数の結果からこの配列の指定されたキーだけを取り出します。
  - watch：リアクティブソースの自動更新を監視する
  - immediate：`false` に設定すると、リクエストが即座に実行するのを防ぎます（デフォルトは `true` ）。

内部で、`lazy: false` はデータが取得される前にルートの読み込みをブロックするために `<Suspense>` を使用します。より快活なユーザエクスペリアのために、`lazy: true` を使用し、代わりにローティング状態を実装することを検討してください。

## Return Values
- **data**：渡される非同期関数の結果
- **pending**：データがまだ取得されているかどうかを示す真偽値
- **refresh/execute**：`handler` 関数が返すデータを更新するために使用できる関数
- **error**：データ取得が失敗した場合のエラーオブジェクト

デフォルトでは、Nuxt は `refresh` が終わるまで、再度実行することはできません。

:::message
サーバーでデータを取得していない場合（例えば、`server: false`）、ハイドレーションが完了するまでデータは取得されません。つまり、クライアントサイドで `useAsyncData` を待っていても、`<script setup>` 内ではデータは `null` のままです。
:::

## Example
```ts
const { data, pending, error, refresh } = await useAsyncData(
  'mountains',
  () => $fetch('https://api.nuxtjs.dev/mountains')
)
```
:::message
`useAsyncData` はコンパイラにより変換される予約関数名なので、独自の関数に `useAsyncData` と名付けてははいけません。
:::

詳細は下記ページを参照してください。
https://nuxt.com/docs/getting-started/data-fetching