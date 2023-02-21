---
title: "[和訳] Nuxt3 公式サイト~Layouts Directory"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Layouts Directory](https://nuxt.com/docs/guide/directory-structure/layouts) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/layouts

# Layouts Directory
Nuxt はアプリケーション全体で使用できるカスタマイズできるレイアウトフレームワークを提供し、共通の UI やコードパターンを再利用可能なレイアウトコンポーネントを抽出するのに理想的です。

レイアウトは `layouts/` ディレクトリに置かれ、使用時に非同期インポートで経由で自動的に読み込まれます。レイアウトは `app.vue` に `<NuxtLayout>` 加え、ページのメタデータの一部としてレイアウトプロパティを設定するか（`~/pages` インテグレーションを使用している場合）、手動で `<NuxtLayout>` の prop として指定することで使用されます（注意：レイアウト名はケバブケースに正規化されるので、`someLayout` は `some-layout` になります）。

アプリケーションにレイアウトが1つしかない場合、代わりに app.vue を使用することをお勧めします。

:::message
他のコンポーネントとは異なり、Nuxt がレイアウト変更間の遷移を適用できるようにレイアウトは単一のルート要素を持つ必要があります - そして、このルート要素は `<slot />` にはできません。
:::

## Enabling the Default Layout
`~/layouts/default.vue` を追加します。
```Vue:layouts/default.vue
<template>
  <div>
    Some default layout shared across all pages
    <slot />
  </div>
</template>
```
レイアウトファイルでは、レイアウトコンテンツは特別なコンポーネントを使用するのではなく、`<slot />` に読み込まれます。

`app.vue` を使用する場合、`<NuxLayout>` も追加する必要があります。

```Vue:app.vue
<template>
  <NuxtLayout>
    some page content
  </NuxtLayout>
</template>
```

## Setting Another Layout
```
layouts/
├ default.vue
└ custom.vue
```
このようにデフォルトレイアウトを直接上書きできます。

```Vue:app.vue
<template>
  <NuxtLayout :name="layout">
    <NuxtPage />
  </NuxtLayout>
</template>

<script setup>
// API コールやログイン状態に応じて選択することができます
const layout = "custom";
</script>
```

また、このようにページごとにデフォルトレイアウトを上書きできます。
```Vue:pages/index.vue
<script>
// `<script setup>` と `<script>` の両方で動きます
definePageMeta({
  layout: "custom",
});
</script>
```

```Vue:app.vue
<template>
  <NuxtLayout>
    <NuxtPage />
  </NuxtLayout>
</template>
```

```Vue:layouts/custom.vue
<template>
  <div>
    Some *custom* layout
    <slot />
  </div>
</template>
```

```Vue:layouts/default.vue
<template>
  <div>
    A *default* layout
    <slot />
  </div>
</template>
```

詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/pages#page-metadata

## Changing the Layout Dynamically
レイアウトに ref や computed プロパティを使用することも可能です。

```Vue
<template>
  <div>
    <button @click="enableCustomLayout">Update layout</button>
  </div>
</template>
<script setup>
function enableCustomLayout () {
  setPageLayout('custom')
}
definePageMeta({
  layout: false,
});
</script>
```

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/routing/layouts

## Overriding a Layout on a Per-page Basis
`~/pages` インテグレーションを使用している場合、`layout:false` を設定し、ページ内で `<NuxtLayout>` コンポーネントを使用することで完全に制御することができます。

```Vue:pages/index.vue
<template>
  <div>
    <NuxtLayout name="custom">
      <template #header> Some header template content. </template>
      The rest of the page
    </NuxtLayout>
  </div>
</template>
<script setup>
definePageMeta({
  layout: false,
});
</script>
```
```Vue:layouts/custom.vue
<template>
  <div>
    <header>
      <slot name="header">
        Default header content
      </slot>
    </header>
    <main>
      <slot />
    </main>
  </div>
</template>
```

:::message
ページ内で `<NuxtLayout>` を使用している場合、それがルート要素ではないことを確認してください（または、レイアウト/ページ遷移を無効にしてください）。
:::