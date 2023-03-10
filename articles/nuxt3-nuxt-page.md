---
title: "[和訳] Nuxt3 公式サイト~<NuxtPage>"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は [Nuxt3 公式サイト <NuxtPage>](https://nuxt.com/docs/api/components/nuxt-page) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/components/nuxt-page

# `<NuxtPage>`
`<NuxtPage>` は Nuxt に付属するビルドインコンポーネントです。`pages/` ディレクトリにあるトップレベルまたはネストされたページを表示するために　`NuxtPage` が必要です。

`NuxtPage` は Vue Router の `<RouterView>` コンポーネントをラップしたものです。`NuxtPage` コンポーネントは同じ `name` と `route` プロパティを受け取ることができます。

- **name**：型：`string`

`name` は `RouterView` が一致したルートレコードのコンポーネントオプションにある対応する名前のコンポーネントをレンダリングするのに役立ちます。

- **route**：型：`RouteLocationNormalized`

`route` はその構成要素の全てが解決されたルートロケーションです。
> Nuxt は `/pages` ディレクトリで見つかった全ての Vue コンポーネントファイルをスキャンし、レンダリングすることで `name` と `route` を自動的に解決します。

`name` と `route` とは別に、`NuxtPage` コンポーネントは `pageKey` プロパティも受け付けます。

- **pageKey**：型：`string` か `function`

`pageKey` は `NuxtPage` コンポーネントが再レンダリングされるタイミングをコントロールするのに役立ちます。

## Example
例えば、`static` キーを渡すと、`NuxtPage` コンポーネントはマウントされたときに1回だけレンダリングされます。

```Vue
<!-- static キー -->
<NuxtPage page-key="static" />
```

また、`pageKey` は `/pages` ディレクトリにある Vue コンポーネントの `<script>` セクションから `definePageMeta` を介して `key` 値として渡すことができます。

```Vue
<script setup>
definePageMeta({
  key: route => route.fullPath
})
</script>
```

## Custom Props
加えて、`NuxtPage` は渡すさらに下の階層に渡す必要があるカスタムプロパティも受け取ります。これらのカスタムプロパティは Nuxt アプリの `attr` を経由してアクセスできます。

```Vue
<NuxtPage :foobar="123" />
```

例えば、上の例では `foobar` の値は `attrs.foobar` を使用して利用可能です。

詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/app