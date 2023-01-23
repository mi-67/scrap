---
title: "[和訳] Nuxt3 公式サイト~SEO and Meta"
emoji: "📌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxt3","nuxtjs"]
published: true
published_at: 2023-01-24 09:00
---
# この記事について
この記事は[Nuxt3 公式サイト SEO and Meta](https://nuxt.com/docs/getting-started/seo-meta) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/getting-started/seo-meta
所々自分の理解のために参考になりそうな別サイトのリンクを貼ってあります。

# SEO and Meta
強力な head の設定、コンポーザブル、コンポーネントを使用して、Nuxt アプリの SEO を向上させることができます。

## App Head
`nuxt.config.ts` の中で `app.head` プロパティを提供することで、アプリ全体の head をカスタマイズできます。

このメソッドはリアクティブなデータの提供を許可していません。もしグローバルなリアクティブデータが必要なら、`app.vue` の中で `useHead` を使うことができます。

設定を簡単に作成する `charset` と `viewport` というショートカットが利用できます。また、Types には Types のセクションで示すキーを指定することができます。

### Defaults
すぐに使えるように、Nuxt は適切なデフォルト値を提供しますが、必要に応じて上書きすることができます。
- `charset`: `utf-8`
- `viewport`: `width=device-width, initial-scale=1`

https://qiita.com/ryounagaoka/items/045b2808a5ed43f96607

### Example
```ts:nuxt.config.ts
export default definenuxtConfig({
    app: {
        head: {
            charset: 'utf-16',
            viewport: 'width=500, initial-scale=1',
            title: 'My App',
            meta: [
                // <meta name="description" content="My amazing site.">
                {name: 'description', content: 'My amazing site.'}
            ]
        }
    }
})
```
詳細は下記ページを参照してください。
https://nuxt.com/docs/api/configuration/nuxt-config/#head

## Composable: `useHead`
`useHead` コンポーザブル関数は @vueuse/head を使用して head タグをプログラム的かつリアクティブな方法で管理することができます。

他のコンポーザブルのように、コンポーネントのセットアップとライフサイクルフックでのみ使用できます。
https://nuxt.com/docs/guide/going-further/hooks

### Example
```Vue:app.vue
<script setup lang="ts">
useHead({
    title: 'My App',
    meta: [
        { name: 'description', content: 'My amazing site.'}
    ],
    bodyAttrs: {
        class: 'test'
    },
    script: [{children: 'console.log(\'Hello World\')'}]
})
</script>
```
詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-head

## Components
Nuxt は `<Title>`、 `<Base>`、`<NoScript>`、`<Style>`、`<Meta>`、`<Link>`、`<Body>`、`<Html>`、`<Head>` コンポーネントを提供し、コンポーネントのテンプレート内でメタデータと直接対話できるようにします。

これらのコンポーネント名はネイティブの HTML の要素と一致するため、テンプレート内では大文字であることがとても重要です。

美観上の理由から、`<Head>` と `<Body>` はネストしたメタタグを受け入れることができますが、最終的な HTML の中でネストしたメタタグがレンダリングされる場所へは影響は与えません。

### Example
```Vue:app.vue
<script setup>
const title = ref('Hello World')
</script>

<template>
  <div>
    <Head>
      <Title>{{ title }}</Title>
      <Meta name="description" :content="title" />
      <Style type="text/css" children="body { background-color: green;}" ></Style>
    </Head>

    <h1>{{ title }}</h1>
  </div>
</template>
```
## Types
下に示すのは `useHead`、`app.head`、コンポーネントで使用される非リアクティブ型です。
```ts
interface MetaObject {
    title?: string
    titleTemplate?: string | ((title?:string) => string)
    base?: Base
    link?: Link[]
    meta?: Meta[]
    style?: Style[]
    noscript?: Noscript[];
    htmlAttrs?: HtmlAttributes;
    bodyAttrs?: BodyAttributes;
}
```
詳細は下記ページを参照してください。
https://github.com/harlan-zw/zhead/tree/main/packages/schema/src


## Features
### Reactivity
reactivity は computed、computed getter ref、reactive として全てのプロパティでサポートされています。

`(() => {})` をよりも `computed(()=>{})` を使うことが推奨されています。

```Vue:useHead
<script setup lang="ts">
const desc = ref('My amazing site.')

useHead({
  meta: [
    { name: 'description', content: desc }
  ],
})
</script>
```
```Vue:Components
<script setup>
const desc = ref('My amazing site.')
</script>
<template>
<div>
  <Meta name="description" :content="desc" />
</div>
</template>
```

### Title Template
`titleTemplate` オプションを使用すると、サイトのタイトルをカスタマイズするための動的なテンプレートを提供することができます。たとえば、各ページのタイトルにサイト名を追加するなどです。

`titleTemplate` はタイトルを `%s` に置き換えた文字列か関数のどちらかを指定します。

完全な制御のために関数を使用したい場合、`nuxt.config` で設定することはできません。代わりに、サイトの全ページに適用される `app.vue` ファイル内で設定することが推奨されます。
```Vue:useHead
<script setup lang="ts">
  useHead({
    titleTemplate: (titleChunk) => {
      return titleChunk ? `${titleChunk} - Site Title` : 'Site Title';
    }
  })
</script>
```
これで、他のページで `useHead` を使ってタイトルを `My Page` に設定すると、ブラウザのタブにはタイトルが 'My Page - Site Title' と表示されるようになります。また、`null` を渡して、サイトタイトルをデフォルト値にすることもできます。

### Body Tag
`link` と `script` のメタタグに `body: true` オプションを設定すると、`<body>` タグの末尾に追加することができます。
```Vue
<script setup lang="ts">
useHead({
  script: [
    {
      src: 'https://third-party-script.com',
      body: true
    }
  ]
})
</script>
```

## Examples
### Usage with `definePageMeta`
`pages/` ディレクトリの中で、`definePageMeta` と `useHead` を一緒に使用することで、現在のルートに基づいたメタデータを設定することができます。

たとえば、まず現在のページタイトルを設定します（ビルド時にマクロによって抽出されるため、動的に設定することはできません）。
```Vue:pages/some-page.vue
<script setup>
definePageMeta({
  title: 'Some Page'
})
</script>
```
そして、レイアウトファイルでは、あらかじめ設定しておいたルートのメタデータを使うことができるでしょう。
```Vue:layouts/default.vue
<script setup>
const route = useRoute()

useHead({
  meta: [{ property: 'og:title', content: `App Name - ${route.meta.title}` }]
})
</script>
```
下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/composables/use-head

詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/pages/#page-metadata

### Add Dynamic Title
以下の例では、`titleTemplate` は %s プレースホルダーを含む文字列または関数として設定されていて、Nuxt アプリの各ルートにページタイトルを動的に設定する柔軟性を高めています。
```Vue:app.vue
<script setup>
  useHead({
    // 置き換えたい部分を %s にして、文字列として定義
    titleTemplate: '%s - Site Title',
    
    // あるいは関数として定義
    titleTemplate: (productCategory) => {
      return productCategory
        ? `${productCategory} - Site Title`
        : 'Site Title'
    }
  })
</script>
```
`nuxt.config` はページタイトルを設定する代替方法としても使用されます。しかし、`nuxt.config` はページタイトルを動的に設定することができません。そのため、動的にタイトルを追加するために `app.vue` ファイルで `titleTemplate` を使い、Nuxt アプリの全ルートに適用させることが推奨されています。

### Add External CSS
以下の例では、`useHead` コンポーザブルの `link` プロパティを使用して Google Fonts を挿入しています。
```Vue:useHead
<script setup lang="ts">
useHead({
  link: [
    {
      rel: 'preconnect',
      href: 'https://fonts.googleapis.com'
    },
    {
      rel: 'stylesheet',
      href: 'https://fonts.googleapis.com/css2?family=Roboto&display=swap',
      crossorigin: ''
    }
  ]
})
</script>
```
```Vue:Components
<template>
<div>
  <Link rel="preconnect" href="https://fonts.googleapis.com" />
  <Link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Roboto&display=swap" crossorigin="" />
</div>
</template>
```