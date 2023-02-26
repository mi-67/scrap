---
title: "[和訳] Nuxt3 公式サイト~App file"
emoji: "📘"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
published_at: 2023-02-26 00:00
---
# この記事について
この記事は[Nuxt3 公式サイト App file](https://nuxt.com/docs/guide/directory-structure/app) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/app

# App file
`app.vue` ファイルは Nuxt3 アプリケーションのメインコンポーネントです。

## Minimal usage
Nuxt3 では、`pages/` ディレクトリはオプションです。存在しない場合、Nuxt は vue-router の依存結果を含めません。ランディングページやルーティングを必要としないアプリケーションで作業するときに便利です。
```Vue:app.vue
<template>
  <h1>Hello world!</h1>
</template>
```

## Usage With Pages
`pages/` ディレクトリがある場合、現在のページを表示するために、`<NuxtPage>` コンポーネントを使用します。
```Vue:app.vue
<template>
  <div>
    <NuxtLayout>
      <NuxtPage/>
    </NuxtLayout>
  </div>
</template>
```

:::message alert
Nuxt3 は　`<NuxtPage>` の中に `<Suspense>` を使用しているため、ルート要素に設定することはできません。
:::

:::message
`app.vue` Nuxt アプリケーションのメインコンポーネントとして機能することを忘れないでください。これに追加したもの（JS と CSS）はすべてグローバルになり、すべてのページに含まれます。
:::

もし、ページ間のページ周りの構造をカスタマイズする可能性があるのなら、[`layouts/` ディレクトリ](https://nuxt.com/docs/guide/directory-structure/layouts)をチェックしてみてください。
https://nuxt.com/docs/guide/directory-structure/layouts