---
title: "[和訳] Nuxt3 公式サイト~App Config File"
emoji: "👻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト App Config File](https://nuxt.com/docs/guide/directory-structure/app-config) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/app-config

# App Config File
Nuxt3 はアプリケーション内にリアクティブな設定を公開するための `app.config` という設定ファイルを提供し、ライフサイクル内の実行時に更新する機能や nuxt プラグインを使用して HMR (hot-module-replacement) で編集する機能があります。

`app.config.ts` ファイルを使用することで実行時のアプリの設定を簡単に提供できます。拡張子は `.ts`、`.js`、`.mjs` のいずれかになります。

```ts:app.config.ts
export default defineAppConfig({
  foo: 'bar'
})
```
:::message
`app.config` 内にはシークレット値を置かないでください。ユーザークライアントバンドルに公開されます。
:::

## Defining App Config
設定と環境変数の値をアプリの残りの部分に公開するために、`app.config` ファイルで設定を定義する必要があります。

例：
```ts:app.config.ts
export default defineAppConfig({
  theme: {
    primaryColor: '#ababab'
  }
})
```
`app.config` に `theme` を追加する場合、Nuxt はコードをバンドルするために Vite やウェブパックを使用します。ページをサーバーレンダリングするときも、ブラウザで `useAppConfig` コンポーザブルを使うときも `theme` に普遍的にアクセスできます。

Nitro の `useAppConfig()` とサーバディレクトへのアクセスは近日中に対応予定です。

```ts
const appConfig = useAppConfig()
console.log(appConfig.theme)
```

## Manually Typing App Config
Nuxt は提供されたアプリ設定から TypeScript インタフェースの自動生成を試します。

アプリ設定を手動で入力することも可能です。入力する内容として以下の2つが考えられます。

### Typing App Config Input
`AppConfigInput` はアプリの設定をする時に有効な *入力* オプションが何であるかを宣言しているモジュール作成者によって使用されるかもしれません。これは `useeAppConfig()` の型に影響を与えません。

```ts:index.d.ts
declare module 'nuxt/schema' {
  interface AppConfigInput {
    /** テーマ設定 */
    theme?: {
      /** アプリのメインカラー */
      primaryColor?: string
    }
  }
}
// 型を拡張する際には、常に何かをインポート/エクスポートすることが重要です。
export {}
```

### Typing App Config Output
`useAppConfig()` を呼び出した結果に型付けしたい場合、`AppConfig` を拡張する必要があります。

:::message
`AppConfig` を型付けすると、Nuxt が実際に定義したアプリの設定から推論した型を上書きしてしまうことに注意してください。
:::

```ts:index.d.ts
declare module 'nuxt/schema' {
  interface AppConfig {
    // これは既存の `theme` プロパティを完全に置き換えるものです。
    theme: {
      // Nuxt の推論よりも具体的な値を追加するためにこの値を入力すると良いでしょう
      // 文字列リテラル型など
      primaryColor?: 'red' | 'blue'
    }
  }
}
// 型を拡張する際には、常に何かをインポート/エクスポートすることが重要です。
export {}
```