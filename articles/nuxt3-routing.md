---
title: "[和訳] Nuxt3 公式サイト~Routing"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxt3","nuxtjs"]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Routing](https://nuxt.com/docs/getting-started/routing) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/getting-started/routing


# Routing
Nuxt の核となる機能の1つがファイルシステムルーターです。`pages/` ディレクトリにある全ての Vue ファイルは、その内容を表示するための URL（またはルート）を作成します。それぞれのページで動的インポートを使用することで、Nuxt は要求されたルートに対して最小限の JavaScript を出荷するためにコード分割をします。

## Pages
Nuxt のルーティングは vue-route をもとにしていて、`pages/` ディレクトリに作られた全てのコンポーネントからファイル名をに基づいてルートを生成します。

このファイルシステムルーティングは動的でネストされたルートを生成するために命名規則を使用しています。

```txt:pages/ ディレクトリの構成
pages
├ about
└ posts
    └[id].vue
```
```json: 生成された Router ファイル
{
  "routes": [
    {
      "path": "/about",
      "component": "pages/about.vue"
    },
    {
      "path": "/posts/:id",
      "component": "pages/posts/[id].vue"
    }
  ]
}
```

## Navigation
`<NuxtLink>` コンポーネントはページ間をリンクします。`href` 属性がページのルートに設定された `<a>` タグをレンダリングします。一度、アプリケーションをハイドレートしたあとは、ブラウザの URL を更新することで、JavaScript でページ遷移が行われます。これにより、全ページの更新を防ぎ、アニメーションによる遷移を可能にします。

クライアント側で `<NuxtLink>` が Viewport（表示表域）に入ると、Nuxt はリンク先のページのコンポーネントとペイロード（生成ページ）を自動的に先読みしてより高速なナビゲーションを実現します。

```Vue:pages/app.vue
<template>
    <header>
        <nav>
            <ul>
                <li><NuxtLink to="/about">About</NuxtLink></li>
                <li><NuxtLink to="/posts/1">Post 1</NuxtLink></li>
                <li><NuxtLink to="/posts/2">Post 2</NuxtLink></li>
            </ul>
        </nav>
    </header>
</template>
```
詳細は下記ページを参照してください。
https://nuxt.com/docs/api/components/nuxt-link

## Route Parameters
`useRoute()` コンポーザブルは 現在のルートの詳細にアクセスするために `<script setup>` ブロックの中あるいは Vue コンポーネントの `setup()` メソッドの中で使用できます。

```Vue:pages/post/[id].vue
<script setup>
const route = useRoute()

// /posts/route1にアクセスした時、route.params.id は 1 になる
console.log(route.params.id)
</script>
```
詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-route

## Route Middleware
Nuxt はアプリケーション全体で使用できる、カスタマイズ可能で特定のルートにナビゲートする前に実行したいコードを抽出するのに理想的なルートミドルウェアフレームワークを提供します。
:::message
ルートミドルウェアは Nuxt アプリの Vue 部分で実行されます。名前は似ていますが、Nitro サーバ部分で実行されるサーバミドルウェアとは全く違います。
:::
ルートミドルウェアには３種類あります。
1. 匿名（またはインライン）のルートミドルウェアで、使用するページで直接定義されるもの
2. 名前のついたルートミドルウェアで、 `middleware/` ディレクトリに置かれ、ページで使用される際に非同期インポートにより自動的に読み込まれるもの（注意：ミドルウェア名はケバブケースに正規化されるため、`someMiddleware` は `some-middleware` となる）
3. グローバルミドルウェアで、`middleware/` ディレクトリに末尾に `.global` がついた状態で置かれ、ルートが変わるたびに自動で実行されるもの

`/dashboard` ページを保護する認証（`auth`）ミドルウェアの例です
```ts:middleware/auth.ts
export default defineNuxtRouteMiddleware((to, from) => {
    // isAuthenticated() はユーザが認証されているかどうかを確認するメソッドの例です
    if(isAuthenticated() === false) {
        return navigateTo('/login')
    }
})
```
```Vue:pages/dashboard.vue
<script setup lang="ts">
definePageMeta({
    middleware: 'auth'
})
</script>

<template>
    <h1>Welcome to your dashboard</h1>
</template>
```
詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/middleware

## Route Validation
Nuxt は検証したい各ページの `definePageMeta` の `validate` プロパティを通してルートバリデーションを提供します。

`validate` プロパティは引数としてルートを受け取ります。このページでレンダリングするのに有効なルートかどうかを判断するために真偽値を返します。`false` を返した場合や他に一致するものが見つからない場合は、404 エラーになります。また、`statusCode` / `statusMessage` を含むオブジェクトを直接返すことで即座にエラーに対応することもできます（他に一致するかはチェックされません）。

もし、より複雑なユースケースがあるのなら、代わりに匿名ミドルウェアを使用することができます。

```Vue:pages/post/[id].vue
<script setup>
definePageMeta({
    validate: async(route) => {
        const nuxtApp = useNuxtApp()
        // id が数字で構成されているかをチェックする
        return /^\d+$/.test(route.params.id)
    }
})
```