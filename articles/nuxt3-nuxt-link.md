---
title: "[和訳] Nuxt3 公式サイト~<NuxtLink>"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は [Nuxt3 公式サイト <NuxtLink>](https://nuxt.com/docs/api/components/nuxt-link) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/components/nuxt-link

# `<NuxtLink>`
Nuxt はアプリケーション内のあらゆる種類のリンクを処理するための `<NuxtLink>` コンポーネントを提供します。

`<NuxtLink>` は Vue Router の `<RouterLink>` コンポーネントの HTML の `<a>` タグの両方を置き換えるドロップダウン型のものです。リンクが *内部* か *外部* かどうかを知的に判断し、利用可能な最適化機能（プリフェッチ、デフォルト属性など）で適宜レンダリングします。

## Example
### Basic Usage
この例では、`<NuxtLink>` コンポーネントを使用して、ウェブサイトへのリンクを貼っています。
```Vue:app.vue
<template>
  <NuxtLink to="https://nuxtjs.org">
    Nuxt Website
  </NuxtLink>
  <!-- <a href="http://nuxtjs.org" rel="noopener noreferrer">...</a> -->
</template>
```

### Internal Routing
この例では、`<NuxtLink>` コンポーネントを使用して、アプリケーションの他のページへのリンクを貼っています。
```Vue:pages/index.vue
<template>
  <NuxtLink to="/about">
    About Page
  </NuxtLink>
  <!-- <a href="/about">...</a> (+Vue Router & prefetching) -->
</template>
```

### `target` and `rel` Attributes
この例では、`<NuxtLink>` は `target`、`rel`、`noRel` とともに使用される。
```Vue:app.vue
<template>
  <NuxtLink to="https://twitter.com/nuxt_js" target="_blank">
    Nuxt Twitter
  </NuxtLink>
  <!-- <a href="https://twitter.com/nuxt_js" target="_black" rel="noopener noreferrer">...</a> -->

  <NuxtLink to="https://discord.nuxtjs.org" target="_blank" rel="noopener">
    Nuxt Discord
  </NuxtLink>
  <!-- <a href="https://discord.nuxtjs.org" target="_blank" rel="noopener">...</a> -->
  
  <NuxtLink to="https://github.com/nuxt" no-rel>
    Nuxt GitHub
  </NuxtLink>
  <!-- <a href="https://github.com/nuxt">...</a> -->

  <NuxtLink to="/contact" target="_blank">
    Contact page opens in another tab
  </NuxtLink>
  <!-- <a href="/contact" target="_blank" rel="noopener noreferrer">...</a> -->
</template>
```

## Props
- **to**：あらゆる URL または Vue Router のルートロケーションオブジェクト
- **href**：`to` のエイリアス。`to` と一緒に使うと `href` は無視されます。
- **target**：リンクに適用するための `target` 属性の値
- **rel**：リンクに適用するための `rel` 属性の値。外部リンクのデフォルトは `"noopener noreferrer"` です。
- **noRel**：`true` を指定した場合、リンクに `rel` 属性は付加されません。
- **activeClass**：アクティブリンクに適用するためのクラス。Vue Router の `active-class` プロパティと同じように内部リンクで動作します。デフォルトでは、Vue Router のデフォルト（`"router-link-active"`）になります。
- **exactActiveClass**：正確なアクティブリンクに適用するためのクラス。Vue Router の `exact-active-class` プロパティと同じように内部リンクで動作します。デフォルトでは、 Vue Router のデフォルト（`"router-link-exact-active"`）になります。
- **replace**：Vue Router の `replace` プロパティと同じように内部リンクで動作します。
- **ariaCurrentValue**：正確なアクティブなリンクに適用するための `aria-current` 属性値。Vue Router の `aria-current-value` プロパティと同じように内部リンクで動作します。
- **external**：リンクを外部（`true`）または内部（`false`）とみなすように強制します。これはエッジケースを処理するのに便利です。
- **prefetch** と **noPrefetch**：ビューポートに入るリンクをアセットをプリフェッチすることを有効にするかどうか
- **prefetchedClass**：プリフェッチされたリンクに適用されるクラス。
- **custom**：`<NuxtLink>` がそのコンテンツを `<a>` 要素でラップするかどうかを指定します。リンクがどのようにレンダリングされ、クリックされた時にどのようにナビゲーションが動作するのか完全に制御することができます。Vue Router のカスタムプロップと同じ働きをします。

デフォルトは上書きできますので、変更したい場合は、[デフォルトの上書き](https://nuxt.com/docs/api/components/nuxt-link#overwriting-defaults)を参照してください。

## Overwriting Defaults
`defaultNuxtLink` を使用して独自のリンクコンポーネントを作成することにより、`<NuxtLink>` のデフォルトを上書きすることができます。

```ts:components/MyNuxtLink.ts
export default defineNuxtLink({
  componentName: 'MyNuxtLink',
  /** 詳しくは下のシグネチャをご覧ください **/
})
```
その後、`<MyNuxtLink />` コンポーネントを新しいデフォルトで通常通り使用できます。

### `defineNuxtLink` Signature
```ts
defineNuxtLink({
  componentName?: string;
  externalRelAttribute?: string;
  activeClass?: string;
  exactActiveClass?: string;
  prefetchedClass?: string;
  trailingSlash?: 'append' | 'remove'
}) => Component
```
- **componentName**：定義された `<NuxtLink>` コンポーネントの名前
- **externalRelAttribute**：外部リンクに適用されるデフォルトの `rel` 属性の値。デフォルトは `"noopener noreferrer"` です。無効にする場合は `""` を指定します。
- **activeClass**：アクティブリンクに適用されるデフォルトクラス。Vue Router の `linkActiveClass` オプションと同じように動作します。デフォルトは、Vue Router のデフォルト（`"router-link-active"`）です。
- **exactActiveClass**：正確なアクティブリンクに適用されるデフォルトクラス。Vue Router の `linkExactActiveClass` オプションと同じように動作します。デフォルトは、Vue Router のデフォルト（`"router-link-exact-active"`）です。
- **prefetchedClass**：プリフェッチされたリンクに適用されるデフォルトクラス。
- **trailingSlash**：`href` の末尾のスラッシュを追加または削除するオプション。未設定または有効な値 `append` か `remove` に一致しない場合は無視されます。

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/routing/nuxt-link