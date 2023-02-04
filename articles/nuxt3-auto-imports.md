---
title: "[和訳] Nuxt3 公式サイト~Auto Imports"
emoji: "👻"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxt3,nuxtjs]
published: true
published_at: 2023-02-05
---
# この記事について
この記事は[Nuxt3 公式サイト Auto Imports](https://nuxt.com/docs/guide/concepts/auto-imports) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/concepts/auto-imports

# Auto Imports
Nuxt はヘルパー関数、コンポーザブル、Vue API を明示的にインポートしなくてもアプリケーション全体で使用できるように自動インポートします。ディレクトリ構造をもとにして、全ての Nuxt アプリケーションは独自コンポーネント、コンポーザブル、プラグインに自動インポートを使用できます。コンポーネント、コンポーザブル、プラグインはこれらの関数を使用することができます。

古典的なグローバル宣言とは異なり、Nuxt は型と IDE の補完とヒントを保ち、プロダクションコードで実際に使用されるものだけを含めます。

ドキュメントでは、明示的にインポートされていない全ての関数は Nuxt によって自動インポートされ、コード内でそのまま使用できます。自動インポートされたコンポーザブルとユーティリティのリファレンスは API セクションで見つけることができます。

:::message
現在、自動インポートはサーバディレクトリの中では動きません。
:::

## Nuxt Auto-imports
Nuxt は、データの取得実行、アプリコンテキストやランタイム設定へのアクセス、状態管理、コンポーネントとプラグインの定義を行うための関数とコンポーザブルを自動インポートします。

```Vue
<script setup>
// useAsyncData() と $fetch() は自動インポートされています
const {data, refresh, pending} = await useAsyncData('/api/hello', () => $fetch('/api/hello'))
</script>
```

## Vue Auto-imports
Vue3 は `ref` や `computed` のような Reactivity API および Nuxt が自動インポートしたライフサイクルフックやヘルパーを公開します。
```Vue
<script setup>
// ref() と computed() は自動インポートされています
const count = ref(1)
const double = computed(() => count.value * 2)
</script>
```

## Directory-based Auto-imports
Nuxt は下に定義されたディレクトリに作成されたファイルを直接自動インポートします。
- `components/` を Vue コンポーネントとしてインポート
- `composables/` を Vue コンポーザブルとしてインポート
- `utils/` をヘルパー関数とその他のユーティリティとしてインポート

## Explicit Imports
Nuxt は全ての自動インポートを `#imports` というエイリアスで公開しており、必要に応じてこれを使用してインポートを明示することができます。
```Vue
<script setup>
import { ref, computed } from '#imports'

const count = ref(1)
count double = computed(() => count.value * 2)
</script>
```

## Disable Auto-imports
自動インポートを無効にしたい場合は、`imports.autoImport` を `false` に設定できます。
```ts:nuxt.config.ts
export default defineNuxtConfig({
  imports: {
    autoImport: false
  }
})
```
これにより暗黙の自動インポートは完全に無効になりますが、明示的なインポート（Explicit Import）を使用することは可能なままです。
