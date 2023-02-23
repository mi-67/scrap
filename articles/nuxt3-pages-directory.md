---
title: "[和訳] Nuxt3 公式サイト~Pages Directory"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Pages Directory](https://nuxt.com/docs/guide/directory-structure/pages) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/pages

# Pages Directory
Nuxt は内部で Vue Router を使用してウェブアプリケーション内でルートを作成するためのファイルに基づいたルーティングを提供します。

このディレクトリは**オプション**です。つまり、app.vue だけを使用する場合、vue-router は含まず（`nuxt.config` で `pages: true` を設定するか、`app/router.options.ts` があれば別）、アプリケーションのバンドルサイズを小さくすることが可能です。

## Usage
pages は Vue コンポーネントで拡張子に `.vue`、`.js`、`.jsx`、`.ts`、`.tsx` を持つことができます。
```Vue:pages/index.vue
<template>
  <h1>Index page</h1>
</template>
```
```ts:pages/index.ts
// https://vuejs.org/guide/extras/render-function.html
export default defineComponent({
  render () {
    return h('h1', 'Index page')
  }
})
```
```tsx:pages/index.tsx
// https://nuxt.com/docs/examples/advanced/jsx
// https://vuejs.org/guide/extras/render-function.html#jsx-tsx
export default defineComponent({
  render () {
    return <h1>Index page</h1>
  }
})
```

`pages/index.vue` ファイルは、アプリケーションの `/` ルートにマップされます。

app.vue を使用している場合、`<NuxtPage/>` コンポーネントを使用して現在のページを表示するようにしてください。
```Vue:app.vue
<template>
  <div>
    <!-- 全ページで共有されるマークアップ 例：NavBar -->
    <NuxtPage />
  </div>
</template>
```

ページ間のルート遷移を可能にするために、ページは**単一のルート要素を持つ必要があります**（HTML のコメントも要素とみなされます）。

つまり、ルートがサーバーサイドレンダリングされたとき、または静的に生成されたときは、そのコンテンツを正しく見ることができますが、クライアントサイドナビゲーションでそのルートへ移動すると、ルート間の移行が失敗し、ルードがレンダリングされないことがわかります。

ここでは、単一のルート要素を持つページがどのように見えるかを説明するためのいくつかの例を紹介します。
```Vue:pages/working.vue
<template>
  <div>
    <!-- このページは正しく1つのルート要素しか持っていません -->
    Page content
  </div>
</template>
```
```Vue:pages/bad-1.vue
<template>
  <!-- このコメントのせいで、クライアントサイドのナビゲーション中にルートが変更されるとこのページはレンダリングされません -->
  <div>Page content</div>
</template>
```
```Vue:pages/bad-2.vue
<template>
  <div>このページは</div>
  <div>1個以上のルート要素があります。</div>
  <div>クライアントサイドのナビゲーション中にルートが変更されるとこのページはレンダリングされません</div>
</template>
```

## Dynamic Routes
角括弧 `[]` の何かを入れると、動的ルートパラメータになります。ファイル名やディレクトリ内に複数のパラメータや非動的テキスト混在させることができます。

パラメータを省略可能にしたい場合は、二重の角括弧で囲む必要があります。例えば、`~/pages/[[slug]]/index.vue` や `~/pages/[[slug]].vue` は `/` と `/test` のどちらにもマッチします。

### Example
```
pages/
├ index.vue
└ users-[group]/ 
  └ [id].vue
```
上記の例では、`$route` オブジェクト経由でコンポーネント内の group/id にアクセスできます。
```Vue:pages/users-[group]/[id].vue
<template>
  <p>{{ $route.params.group }} - {{ $route.params.id }}</p>
</template>
```
`users-admins/123` に移動すると、次のようにレンダリングされます。
```Vue
<p>admins - 123</p>
```

Composition API を使用してルートにアクセスしたい場合、グローバルな `useRoute` 関数を使えば、Options API の `this.$route` のようにルートにアクセスできます。

```Vue
<script setup>
const route = useRoute()
if (route.params.group === 'admins' && !route.params.id) {
  console.log('Warning! Make sure user is authenticated!')
}
</script>
```

## Catch-all Route
キャッチオールルートが必要な場合、`[...slug].vue` のような名前のファイルを使用して作成します。これはそのパスの下の*全ての*ルートにマッチします。
```Vue:pages/[...slug].vue
<template>
  <p>{{ $route.params.slug }}</p>
</template>
```
`hello/world` に移動すると、次のようにレンダリングされます。
```Vue
<p>["hello", "world"]</p>
```

## Nested Routes
`<NuxtPage>` を使用してネストしたルートを表示できます。
例：
```
pages/
├ parent/
│ └ child.vue
└ parent.vue
```
このファイルツリーは、これらのファイルを生成します。
```ts
[
  {
    path: '/parent',
    component: '~/pages/parent.vue',
    name: 'parent',
    children: [
      {
        path: 'child',
        component: '~/pages/parent/child.vue',
        name: 'parent-child'
      }
    ]
  }
]
```
`child.vue` コンポーネントを表示するためには、`pages/parent.vue` 内に `<NuxtPage>`　コンポーネントを挿入する必要があります。

```Vue:pages/parent.vue
<template>
  <div>
    <h1>I am the parent view</h1>
    <NuxtPage :foobar="123" />
  </div>
</template>
```

### Child Route Keys
`<NuxtPage>` コンポーネントが再レンダリングされるタイミングをより制御したい場合（例えば、遷移のためなど）、`pageKey` プロパティを経由して文字列か関数を渡すか、`definePageMeta` を通じて `key` 値を定義することが可能です。

```Vue:pages/parent.vue
<template>
  <div>
    <h1>I am the parent view</h1>
    <NuxtPage :page-key="someKey" />
  </div>
</template>
```
あるいは、かわりに
```Vue:pages/child.vue
<script setup>
definePageMeta({
  key: route => route.fullPath
})
</script>
```
下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/routing/pages

## Page Metadata
アプリ内の各ルートに対してメタデータを定義したい場合があります。`definePageMeta` マクロを使用することででき、`<script>` と `<script setup>` の両方で動作します。

```Vue
<script setup>
definePageMeta({
  title: 'My home page'
})
</script>
```

このデータは、アプリの残りの部分で `route.meta` オブジェクトからアクセスできます。

```Vue
<script setup>
const route = useRoute()
console.log(route.meta.title) // My home page
</script>
```

ネストしたルートを使用している場合、これら全てのルートからのページメタデータは単一のオブジェクトに統合されます。ルートメタの詳細については、[vue-router のドキュメント](https://router.vuejs.org/guide/advanced/meta.html#route-meta-fields)を参照してください。
https://router.vuejs.org/guide/advanced/meta.html#route-meta-fields

`defineEmit` や `defineProps` （[Vue のドキュメント](https://vuejs.org/api/sfc-script-setup.html#defineprops-defineemits)を参照）と同様、`definePageMeta` は**コンパイラ・マクロ**です。コンパイルされるので、コンポーネント内で参照できません。その代わり、このマクロに渡されたメタデータはコンポーネントの外に出されます。そのため、ページメタオブジェクトはコンポーネント（または、コンポーネントで定義された値）を参照できません。しかし、インポートされたバインディングは参照することができます。
https://vuejs.org/api/sfc-script-setup.html#defineprops-defineemits

```Vue
<script setup>
import { someData } from '~/utils/example'
const title = ref('')
definePageMeta({
  title,  // これはエラーになります
  someData
})
</script>
```

### Special Metadata
もちろん、アプリ前提で使用するメタデータを定義することは歓迎されます。しかし、`definePageMeta` で定義されるメタデータの中には、特定の目的を持ったものがあります。

#### `alias`
ページエイリアスを定義することができます。同じページに異なるパスからアクセスできるようになります。[vue-router のドキュメント](https://router.vuejs.org/guide/essentials/redirect-and-alias.html#alias)で定義されているように文字列または文字列の配列のいずれかになります。
https://router.vuejs.org/guide/essentials/redirect-and-alias.html#alias

#### `keepalive`
`definePageMeta` で `keepalive: true` を設定すると、Nuxt は自動的に Vue の `<KeepAlive>` コンポーネントでページをラップします。これは例えば、動的な子ルートを持つ親ルートで、ルートの変更に渡ってページの状態を保持したい場合に便利でしょう。

親ルートの状態を保持することが目的の場合、次の構文： `<NuxtPage keepalive />` を使用します。`<KeepAlive>` に渡す props を設定することもできます（完全なリストは[こちら](https://vuejs.org/api/built-in-components.html#keepalive)）。
https://vuejs.org/api/built-in-components.html#keepalive

このプロパティのデフォルト値は `nuxt.config` で設定できます。

#### `key`
Child Route Keys のセクションを参照してください。

#### `layout`
ルートをレンダリングするために使用されるレイアウトを定義できます。これは、false（あらゆるレイアウトを無効にする）、文字列、何らかの方法でリアクティブにしたい場合は ref/computed のいずれかになります。
レイアウトの詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/layouts

#### `layoutTransition` and `pageTransition` 
ページとレイアウトをラップする `<transition>` コンポーネントの遷移のプロパティを定義したり、`false` を渡してそのルートの `<transition>` ラッパーを無効にしたりすることができます。
渡すことができるオプションのリストは下記ページを参照してください。
https://vuejs.org/api/built-in-components.html#transition
遷移の仕組みについては下記ページを参照してください。
https://vuejs.org/guide/built-ins/transition.html#transition

このプロパティのデフォルト値は `nuxt.config` で設定できます。

#### `middleware`
ページの読み込み前に適用するミドルウェアを定義できます。これは、一致する親子ルートで使用されている他の全てのミドルウェアと統合されます。文字列、関数（global before guard パターンに従う匿名/インラインミドルウェア関数）、または文字列/関数の配列のいずれかを指定します。
ミドルウェアの詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/middleware

#### `name`
ページのルートの名前を定義することができます。

#### `path`
ファイル名で表現できるよりも複雑なパターンがある場合、パスマッチャーを定義することができます。
詳細は下記ページを参照してください。
https://router.vuejs.org/guide/essentials/route-matching-syntax.html#custom-regex-in-params

### Typing Custom Metadata
ページのカスタムメタデータを追加する場合、タイプセーフな方法で行いたいと思うかもしれません。`definePageMeta` によって受け入れられるオブジェクトの型を拡張することが可能です。
```ts:index.d.ts
declare module '#app' {
  interface PageMeta {
    pageType?: string
  }
}
// 型を拡張する際には、常に何かをインポート/エクスポートすることが重要です。
export {}
```

## Navigation
アプリのページ間を移動するためには、`<NuxtLink>` コンポーネントを使用する必要があります。

このコンポーネントは Nuxt に含まれているため、他のコンポーネントのようにインポートする必要はありません。

`pages` フォルダにある `index.vue` へのシンプルなリンクです。

```Vue
<template>
  <NuxtLink to="/">Home page</NuxtLink>
</template>
```

`<NuxtLink>` の使用方法については下記ページを参照してください。
https://nuxt.com/docs/api/components/nuxt-link

## Router Options
vue-router オプションをカスタマイズすることが可能です。

### Using `app/router.options`
ルーターオプションを指定する場合に推奨されている方法です。

```ts:app/router.options.ts
import type { RouterConfig } from '@nuxt/schema'
// https://router.vuejs.org/api/interfaces/routeroptions.html
export default <RouterConfig> {
}
```

#### Custom Routes
スキャンされたルートを受け取り、カスタマイズされたルートを返す関数を使用して、オプションでルートを上書きすることができます。`null` または `undefined` を返す場合、Nuxt はデフォルトルートにフォールバックします（入力配列を修正にするのに便利です）。
```ts:app/router.options.ts
import type { RouterConfig } from '@nuxt/schema'
// https://router.vuejs.org/api/interfaces/routeroptions.html
export default <RouterConfig> {
  routes: (_routes) => [
    {
      name: 'home',
      path: '/',
      component: () => import('~/pages/home.vue')
    }
  ],
}
```
Nuxt は `routes` 関数から返すどんな新しいルートもあなたが提供するコンポーネントの `definePageMeta` で定義されたメタデータで補強しません。そうさせたい場合、ビルド時に呼び出される `pages: extend` フックを使うべきでしょう。

#### Custom History (advanced)
ベース URL を受け取り、ヒストリーモードを返す関数を使ってヒストリーモードをオプションで上書きすることができます。`null` や `undefined` を返す場合、Nuxt はデフォルトの履歴にフォールバックします。

```ts:app/router.options.ts
import type { RouterConfig } from '@nuxt/schema'
import { createMemoryHistory } from 'vue-router'
// https://router.vuejs.org/api/interfaces/routeroptions.html
export default <RouterConfig> {
  history: base => process.client ? createMemoryHistory(base) : null /* デフォルト */
}
```

### Using `nuxt.config`
**注意**：JSON シリアライズ可能なオプションのみ設定可能です。
- `linkActiveClass`
- `linkExactActiveClass`
- `end`
- `sensitive`
- `strict`
- `hashMode`

```ts:nuxt.config.ts
export default defineNuxtConfig({
  router: {
    // https://router.vuejs.org/api/interfaces/routeroptions.html
    options: {}
  }
})
```

### Hash Mode (SPA)
SPAモードでは、ハッシュ履歴を有効にすることが可能です。このモードでは、ルーターは内部で渡される実際の URL の前にハッシュ文字（#）を使用します。有効にすると、**URL はサーバーに送られず、SSR はサポートされません**。
```ts:nuxt.config.ts
export default defineNuxtConfig({
  ssr: false,
  router: {
    options: {
      hashMode: true
    }
  }
})
```

## Modifying or Removing Scanned Pages
Nuxt は `nuxt.config` で設定された `extensions` の一つで終わる全てのファイルのルートを作成します（デフォルトでは、これらは `.js`、`.jsx`、`.mjs`、`.ts`、`.tsx`、`.vue` です）。

`pages:extended` nuxt フックを使用してスキャンされたルートからページの変更や削除ができます。例えば、あらゆる `.ts` に対してルートを作成しないようにするためです。
```ts:nuxt.config.ts
export default defineNuxtConfig({
  hooks: {
    'pages:extend' (pages) {
      function removePagesMatching (pattern: RegExp, pages: NuxtPage[] = []) {
        const pagesToRemove = []
        for (const page of pages) {
          if (pattern.test(page.file)) {
            pagesToRemove.push(page)
          } else {
            removePagesMatching(pattern, page.children)
          }
        }
        for (const page of pagesToRemove) {
          pages.splice(pages.indexOf(page), 1)
        }
      }
      removePagesMatching(/\.ts$/, pages)
    }
  }
})
```

## Programmatic Navigation
Nuxt3 は `navigateTo()` ユーティリティメソッドによってプログラムによるナビゲーションが可能です。このユーティリティメソッドを使用して、アプリ内でユーザをプログラム的にナビゲートすることができます。これはユーザーからの入力を受け取り、アプリケーション全体で動的にナビゲートするのに適しています。この例では、ユーザーが検索フォームを送信した時に呼び出される`navigate()` と呼ばれるシンプルなメソッドを用意しています。

注意：`navigateTo()` は常に `await` にするか、関数からの返り値で結果を連鎖されてください。

```Vue
<script setup>
const router = useRouter();
const name = ref('');
const type = ref(1);
function navigate(){
  return navigateTo({
    path: '/search',
    query: {
      name: name.value,
      type: type.value
    }
  })
}
</script>
```