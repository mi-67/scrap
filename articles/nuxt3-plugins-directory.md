---
title: "[和訳] Nuxt3 公式サイト~Plugins Directory"
emoji: "😸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は [Nuxt3 公式サイト Plugins Directory](https://nuxt.com/docs/guide/directory-structure/plugins) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/plugins

# Plugins Directory
Nuxt は `plugins` ディレクトリにあるファイルを自動的に読み込み、Vue アプリケーションの作成時にロードします。プラグインをサーバーサイドまたはクライアントサイドのみでロードするために、ファイル名に `.server` または `.client` というサフィックスが使用できます。

:::message
`plugins/` ディレクトリにある全てのプラグインは自動登録させるので、別途、`nuxt.config` に追加する必要はありません。
:::

## Which Files Are Registered
`plugins/` ディレクトリのトップレベルにあるファイル（またはサブディレクトリ内のインデックスファイル）だけがプラグインとして登録されます。
例：
```
plugins/
├ myPlugin.ts
└ myOtherPlugin/ 
  ├ supportingFile.ts
  ├ componentToRegister.vue
  └ index.ts 
```
`myPlugin.ts` と `myOtherPlugin/index.ts` だけが登録されます。

## Creating Plugins
プラグインに渡される唯一の引数は `nuxtApp` です。
```ts
export default defineNuxtPlugin(nuxtApp => {
  // nuxtApp で何かする
})
```

## Plugin Registration Order
ファイル名の前に番号をつけることで、プラグインの登録順を制御できます。

例：
```
plugins/
├ 1.myPlugin.ts
└ 2.myOtherPlugin.ts 
```
この例では、`2.myOtherPlugin.ts` は `1.myPlugin.ts` によって注入されたものにアクセスできます。

他のプラグインに依存するプラグインがある場合に便利です。

## Using Composables Within Plugin
Nuxt プラグインの中でコンポーザブルを使用できます。

```ts
export default defineNuxtPlugin((NuxtApp) => {
  const foo = useFoo()
})
```
しかし、いくつかの制限と違いがあることに留意してください。

**コンポーザブルが後から登録した他のプラグインに依存している場合、動作しないことがあります。**
**理由**：プラグインは順次、他のものよりも先に呼ばれます。まだ呼び出されていない他のプラグインに依存するコンポーザブルを使用することもあるでしょう。

**コンポーザブルが Vue.js のライフサイクルに依存している場合、動作しません。**
**理由**：通常、Vue.js のコンポーザブルは現在のコンポーネントインスタンスにバインドされるのに対し、プラグインは `nuxtApp` インスタンスにのみバインドされます。

## Automatically Providing Helpers
`NuxtApp` インスタンスにヘルパーを提供したい場合、プラグインからヘルパーを`provide` キーで返します。例えば、
```ts
export default defineNuxtPlugin(() => {
  return {
    provide: {
      hello: (msg: string) => `Hello ${msg}!`
    }
  }
})
```
他のファイルでこれを使用することができます。

```Vue
<template>
  <div>
    {{ $hello('world') }}
  </div>
</template>
<script setup lang="ts">
// または、こちらを使用できます。
const { $hello } = useNuxtApp()
</script>
```

## Typing Plugins
プラグインからヘルパーを返す場合、自動的に型付けされます。`useNuxtApp()` の戻り値やテンプレート内で型付けされているのがわかると思います。

提供されたヘルパーを他のプラグイン *内* で使用する必要がある場合、型付けされたバージョンを取得するために `useNuxtApp()` を呼び出すことができます。しかし、一般的には、プラグインの順番が確かではない限り避けるべきです。

### Advanced
発展的なユースケースとして、次のように注入されたプロパティの型を定義できます。
```ts:index.d.ts
declare module '#app' {
  interface NuxtApp {
    $hello (msg: string): string
  }
}

declare module 'vue' {
  interface ComponentCustomProperties {
    $hello (msg: string): string
  }
}

export { }
```

## Vue Plugins
Google Analytics のタグを追加する vue-gtag のように、Vue プラグインを使用したい場合、Nuxt のプラグインもそのように使用することができます。

> これにはもっと簡単にするための Open RFC があります。詳細は[こちら](https://github.com/nuxt/nuxt/discussions/17143)

https://github.com/nuxt/nuxt/discussions/17143

まず、欲しいプラグインをインストールします。
```bash
> yarn add --dev vue-gtag-next
```
そして、プラグインファイル `plugins/vue-gtag.client.js` を作成します。

```ts:plugins/vue-gtag.client.js
import VueGtag, { trackRouter } from 'vue-gtag-next'
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.use(VueGtag, {
    property: {
      id: 'GA_MEASUREMENT_ID'
    }
  })
  trackRouter(useRouter())
})
```

## Vue Directories
同様に、カスタム Vue ディレクティブをプラグインに登録できます。例えば、`plugins/directive.ts` で

```ts:plugins/directive.ts
export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.directive('focus', {
    mounted (el) {
      el.focus()
    },
    getSSRProps (binding, vnode) {
      // SSR 専用の props はここで用意できます。
      return {}
    }
  })
})
```

詳細は下記ページを参照してください。
https://vuejs.org/guide/reusability/custom-directives.html

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/app/plugins