---
title: "[和訳] Nuxt3 公式サイト~useError"
emoji: "👻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト useError](https://nuxt.com/docs/api/composables/use-error) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-error

# `useError`
`useError` コンポーザブルは処理中のグローバルな Nuxt エラーを返し、クライアントとサーバーで利用可能です。
```ts
const error = useError()
```
`useError` はステートにエラーを設定し、コンポーネント間でリアクティブかつ SSR フレンドリーなグローバル Nuxt エラーを生成します。Nuxt エラーは以下のプロパティを持ちます。

## Properties
- statusCode
  - 型：`Number`
  - HTTP レスポンスステータスコード
- statusMessage
  - 型：`String`
  - HTTP レスポンスステータスメッセージ
- message
  - 型：`String`
  - エラーメッセージ

詳細は下記ページを参照してください。
https://nuxt.com/docs/getting-started/error-handling