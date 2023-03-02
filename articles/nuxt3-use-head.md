---
title: "[和訳] Nuxt3 公式サイト~useHead"
emoji: "🍣"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
published_at: 2023-03-02 22:00
---
# この記事について
この記事は[Nuxt3 公式サイト useHead](https://nuxt.com/docs/api/composables/use-head) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-head

# `useHead`
Nuxt は Nuxt アプリの個々のページのヘッドプロパティを追加してカスタマイズするために `useHead` コンポーザブルを提供します。

`useHead` は @vueuse/head を利用しています。より詳細なドキュメントは[こちら](https://unhead.harlanzw.com)を参照してください。
https://unhead.harlanzw.com

詳細は下記ページを参照してください。
https://nuxt.com/docs/getting-started/seo-meta

## Type
```ts
useHead(meta: MaybeComputedRef<MetaObject>): void
```
以下は、`useHead` の非反応型である。より詳細な型は `zhead` を参照してください。

```ts
interface MetaObject {
  title?: string
  titleTemplate?: string | ((title?: string) => string)
  base?: Base
  link?: Link[]
  meta?: Meta[]
  style?: Style[]
  script?: Script[]
  noscript?: Noscript[]
  htmlAttrs?: HtmlAttributes[]
  bodyAttrs?: BodyAttributes[]
}
```
`useHead` のプロパティは動的で、`ref`、`computed`、`reactive` のプロパティを受け入れることができます。`meta` パラメータはオブジェクトを返す関数を受け取って、オブジェクト全体をリアクティブにすることもできます。

## Parameters
### `meta`
型：`MetaObject`

以下のヘッドメタデータを受け入れるオブジェクト
- `meta`
  
  **型**：`Array<Record<string, any>>`

  配列の各要素は新しく作成された `<meta>` タグに対応づけられ、オブジェクトプロパティは対応する属性に対応づけられます。
- `link`

  **型**：`Array<Record<string, any>>`

  配列の各要素は新しく作成された `<link>` タグに対応づけられ、オブジェクトプロパティは対応する属性に対応づけられます。
- `style`
  
  **型**：`Array<Record<string, any>>`

  配列の各要素は新しく作成された `<style>` タグに対応づけられ、オブジェクトプロパティは対応する属性に対応づけられます。
- `script`

  **型**：`Array<Record<string, any>>`

  配列の各要素は新しく作成された `<script>` タグに対応づけられ、オブジェクトプロパティは対応する属性に対応づけられます。
- `noscript`

  **型**：`Array<Record<string, any>>`

  配列の各要素は新しく作成された `<noscript>` タグに対応づけられ、オブジェクトプロパティは対応する属性に対応づけられます。
- `titleTemplate`

  **型**：`string` | `((title: string) => string)`

  個々のページでページタイトルをカスタマイズするための動的テンプレートを設定します。
- `title`
  
  **型**：`string`

  個々のページで静的ページのタイトルを設定します。
- `bodyAttrs`
  
  **型**：`Record<string, any>`

  `<body>` タグの属性を設定します。各オブジェクトプロパティは対応する属性に対応づけられます。
- `htmlAttrs`

  **型**：`Record<string, any>`

  `<body>` タグの属性を設定します。各オブジェクトプロパティは対応する属性に対応づけられます。
