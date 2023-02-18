---
title: "[和訳] Nuxt3 公式サイト~Components Directory"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Components Directory](https://nuxt.com/docs/guide/directory-structure/components) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/components

# Components Directory
`components/` ディレクトリは、ページや他のコンポーネントの中に取り込むことができる全ての Vue コンポーネントを置くところです（詳細は[こちら](https://vuejs.org/guide/essentials/component-basics.html#components-basics)）。
https://vuejs.org/guide/essentials/component-basics.html#components-basics

Nuxt は自動的に`components/` ディレクトリにあるあらゆるコンポーネントを取り込みます（使用しているモジュールによって登録されているコンポーネントも同様）。

```
components/
├ TheHeader.vue
└ TheFooter.vue
```
```Vue:layouts/default.vue
<template>
<div>
  <TheHeader />
  <slot />
  <TheFooter />
</div>
</template>
```

## Custom directories
デフォルトでは、`~/components` ディレクトリのみがスキャンされます。他のディレクトリを追加したい場合、またはこのディレクトリのサブフォルダ内でコンポーネントをスキャンする方法を変更したい場合は、設定に追加のディレクトリを加えることができます。

```ts
export default defineNuxtConfig({
  components: [
    { '~/components/special-components', prefix: 'Special' },
    '~/components',
  ]
})
```

あらゆるネストされたディレクトリは、順番にスキャンされるため最初に追加する必要があります。

## Component extensions
デフォルトでは、`nuxt.config.ts` の extension キー指定された拡張子を持つあらゆるファイルはコンポーネントとして扱われます。コンポーネントとして登録されるファイル拡張子を限定する必要がある場合は、コンポーネントディレクトリ宣言とその `extension` キーの拡張形式を使用します。
```diff ts 
export default defineNuxtModule({
  components: [
    {
      path: '~/components',
+     extensions: ['.vue']
    }
  ]
})
```

## Component Names
下のようにネストしたディレクトリにコンポーネントがある場合
```
components/
└ base/
  └ foo/
    └ Button.vue
```
このとき、コンポーネントの名前はそれ自身のパスディレクトリとファイル名を基にして、重複するセグメントは削除されます。よって、コンポーネント名は以下のようになります。
```Vue
<BaseFooButton />
```
わかりやすくするために、コンポーネントのファイル名をコンポーネント名と一致させることを推奨します（つまり、上記の例では、`Button.vue` を `BaseFooButton.vue` と改名すれば良いでしょう）。

もし、パスではなく名前だけに基づいて自動インポートしたい場合は、設定オブジェクトの拡張フォームを使用して `pathPrefix` オプションを `false` に設定する必要があります。
```diff ts
export default defineNuxtConfig({
  components: [
    {
      path: '~/components/',
+     pathPrefix: false,
    },
  ],
})
```
これは Nuxt2 で使用されていたのと同じストラテジーを使用してコンポーネントを登録します。たとえば、`~/components/Some/MyComponent.vue` は `<SomeMyComponent>` ではなく `<MyComponent>` として使用できるようになります。

## Dynamic Components
Vue の `<component :is="someComputedComponent">` 構文を使いたい場合は、Vue が提供する `resolveComponent` ヘルパーを使う必要があります。

例：
```Vue
<template>
  <component :is="clickable ? MyButton : 'div'" />
</template>
<script setup>
const MyButton = resolveComponent('MyButton')
</script>
```
:::message
もし動的コンポーネントを処理するために `resolveComponent` を使用しているなら、コンポーネントの名前（変数ではなく文字列でなければいけません）以外を挿入しないように注意してください。
:::

また、推奨しませんが、全てのコンポーネントをグローバルに登録できます。全てのコンポーネントに非同期チャンクが作成され、アプリケーション全体で利用できるようになります。

```diff ts
  export default defineNuxtConfig({
    components: {
+     global: true,
+     dirs: ['~/components']
    },
  })
```
また、一部のコンポーネントを `~/components/global` ディレクトリに配置することで選択的にグローバルに登録できます。

`global` オプションはコンポーネントディレクトリごとに設定することも可能です。

## Dynamic Imports
コンポーネントを動的にインポートする（コンポーネントのレイジーローディングとも呼ばれる）ために必要なことはコンポーネント名に `Lazy` というプレフィックスを追加することです。

```Vue:layouts/default.vue
<template>
  <div>
    <TheHeader />
    <slot />
    <LazyTheFooter />
  </div>
</template>
```
これは、コンポーネントが常に必要ではない場合に特に有用です。`Lazy` プレフィックスを使うことで、適切なタイミングまでコンポーネントコードの読み込みを遅延でき、JavaScript のバンドルサイズを最適化するのに便利です。

```Vue:pages/index.vue
<template>
  <div>
    <h1>Mountains</h1>
    <LazyMountainsList v-if="show" />
    <button v-if="!show" @click="show = true">Show List</button>
  </div>
</template>
<script>
export default {
  data() {
    return {
      show: false
    }
  }
}
</script>
```

## Direct Imports
Nuxt の自動インポート機能を回避したい場合や回避が必要な場合は、`#components` から明示的にインポートこともできます。
```Vue:pages/index.vue
<template>
  <div>
    <h1>Mountains</h1>
    <LazyMountainsList v-if="show" />
    <button v-if="!show" @click="show = true">Show List</button>
    <NuxtLink to="/">Home</NuxtLink>
  </div>
</template>
<script setup>
  import { NuxtLink, LazyMountainsList } from '#components'
  const show = ref(false)
</script>
```

## `<ClientOnly>` Component
Nuxt は意図的にクライアント側のみでコンポーネントをレンダリングするために `<ClientOnly>` コンポーネントを提供します。クライアントにのみコンポーネントをインポートするためには、クライアントサイドのみのプラグインにコンポーネントを登録します。

```Vue:pages/example.vue
<template>
  <div>
    <Sidebar />
    <ClientOnly>
      <!-- このコンポーネントはクライアントサイドのみでレンダリングされます -->
      <Comments />
    </ClientOnly>
  </div>
</template>
```
クライアント側で `<ClientOnly>` がマウントされるまでのフォールバックとしてスロットを使用します。

```Vue:pages/example.vue
<template>
  <div>
    <Sidebar />
    <!-- これはサーバーサイドで "span" 要素をレンダリングするものです -->
    <ClientOnly fallbackTag="span">
      <!-- このコンポーネントはクライアントサイドのみでレンダリングされます -->
      <Comments />
      <template #fallback>
        <!-- これはサーバーサイドでレンダリングされます -->
        <p>Loading comments...</p>
      </template>
    </ClientOnly>
  </div>
</template>
```

## .client Components
コンポーネントがクライアント側だけでレンダリングされることを意図している場合、コンポーネントの接尾辞 `.client` を追加することができます。

```
components/
└ Comments.client.vue
```
```Vue:pages/example.vue
<template>
  <div>
    <!-- このコンポーネントはクライアント側のみでレンダリングされます -->
    <Comments />
  </div>
</template>
```

:::message
この機能は、Nuxt の自動インポートおよび `#components` インポートでのみ動作します。これらのコンポーネントを実際のパスから意図的にインポートしても、クライアント専用のコンポーネントには変換されません。
:::

:::message
`.client` コンポーネントはマウントされた後のみに描画されます。`onMounted()` を使用してレンダリングされたテンプレートにアクセスするには、`onMounted()` フックのコールバックに `await nextTick()` を追加します。
:::

## .server Components
`.server` コンポーネントは単独で使用することもできますし、`.client` コンポーネントと組み合わせて使用することもできます。

### Standalone server components
スタンドアローンサーバーコンポーネントは常にサーバ上でレンダリングされます。それらの props が更新されると、レンダリングされた HTML をインプレースで更新するネットワークリクエストが発生します。

サーバーコンポーネントは現在実験的なもので、それらを使うためには `nuxt.connfig` で「componentIslands」機能を有効にする必要があります。
```ts:nuxt.config.ts
export default defineNuxtConfig({
  experimental: {
    componentIslands: true
  }
})
```
これにより、サーバー専用のコンポーネントを接尾辞 `.server` で登録でき、アプリケーションの任意の場所で自動停に使用できます。

```
components/
└ HighlightedMarkdown.server.vue
```
```Vue:pages/example.vue
<template>
  <div>
    <!--
      これは自動的にサーバ上でレンダリングされます。つまり、マークダウンのパースとハイライトを行うライブラリはクライアントのバンドルに含まれません。
     -->
    <HighlightedMarkdown markdown="# Headline" />
  </div>
</template>
```

:::message
スロットは現在の開発状況ではサーバーコンポーネントでサポートされていません。
:::

非同期コードをサーバーコンポーネントで使用する場合、既知の問題があることに注意してください。それは、最初のレンダリングでハイドレーションミスマッチエラーを引き起こすということです。詳細は [#18500](https://github.com/nuxt/nuxt/issues/18500#issuecomment-1403528142) を参照してください。回避策があるまで、サーバーコンポーネントは同期でなければいけません。

### Paired with a `.client` component
この場合、`.server` + `.client` コンポーネントはコンポーネントの2「等分」であり、サーバー側とクライアント側にコンポーネントを別々に実装する発展的なユースケースで使用することができます。

```
components/
├ Comments.client.vue
└ Comments.server.vue
```
```Vue:pages/example.vue
<template>
  <div>
    <!-- このコンポーネントはサーバーサイドで　Comments.server をレンダリングし、クライアントサイドにマウントされると Comments.client をレンダリングします。 -->
    <Comments />
  </div>
</template>
```
:::message
コンポーネントのクライアント側では、コンポーネントがサーバーでレンダリングされた HTML を「ハイドレート」できることが重要です。つまり、最初の読み込み時に同じ HTML を描画する必要があり、さもなければ、ハイドレーションのミスマッチが発生します。
:::

## `<DevOnly>` Component
Nuxt は開発中のみコンポーネントを描画するために `<DevOnly>` コンポーネントを提供します。

このコンテンツは製品版のビルドやツリーシェイクには含まれません。

```Vue:pages/example.vue
<template>
  <div>
    <Sidebar />
    <DevOnly>
      <!-- このコンポーネントは開発中のみ描画されます -->
      <LazyDebugBar />
    </DevOnly>
  </div>
</template>
```

## Library Authors
自動ツリーシェイクとコンポーネント登録ができる Vue コンポーネントライブラリの作成がとても簡単✨

`components:dirs` フックを使用すると Nuxt モジュールでのユーザの設定を必要とせずにディレクトリリストを拡張できます。

このようなディレクトリ構造を想像してください。
```
node_modules/
├ awesome-ui/
│ ├ components/
│ │ ├ Alert.vue
│ │ └ Button.vue
│ └ nuxt.js
├ pages/
│ └ index.vue
└ nuxt.config.js
```
そして、`awesome-ui/nuxt.js` で `components:dirs` フックを使用することができます。
```ts
import { defineNuxtModule } from '@nuxt/kit'
import { fileURLToPath } from 'node:url'
export default defineNuxtModule({
  hooks: {
    'components:dirs'(dirs) {
      // ./components ディレクトリをリストに追加します。
      dirs.push({
        path: fileURLToPath(new URL('./components', import.meta.url)),
        prefix: 'awesome'
      })
    }
  }
})
```
これで完了です！これで、プロジェクトでは、`nuxt.config` ファイルで UI ライブラリを Nuxt モジュールとしてインポートできるようになりました。

```ts:nuxt.config
export default {
  modules: ['awesome-ui/nuxt']
}
```

...そして、モジュールコンポーネント（接頭辞は `awesome-` ）を直接 `pages/index.vue` で使用します。

```Vue:pages/index.vue
<template>
  <div>
    My <AwesomeButton>UI button</AwesomeButton>!
    <awesome-alert>Here's an alert!</awesome-alert>
  </div>
</template>
```

使用する場合のみコンポーネントを自動的にインポートし、`node_modules/awesome-ui/components` にあるコンポーネントを更新する際にも HMR をサポートします。

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/auto-imports/components