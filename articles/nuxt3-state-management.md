---
title: "[和訳] Nuxt3 公式サイト~State Management"
emoji: "😸"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxt3","nuxtjs"]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト State Management](https://nuxt.com/docs/getting-started/state-management) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/getting-started/state-management

# State Management
Nuxt はコンポーネント間でリアクティブで SSR に適した共有状態を作成するために、`useState` コンポーザブルを提供します。

`useState` は SSR に適した `ref` の置き換えです。その値はサーバーサイドレンダリングの後（クライアント再度のハイドレーションの間）保存され、一意のキーを使って全てのコンポーネント間で共有されます。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-state

:::message
`useState` は `setup` の時あるいは `Lifecycle Hooks` 時だけで機能します。
:::
:::message
`useState` 内のデータは JSON にシリアライズされるため、クラス、関数、シンボルなど、シリアライズできないものを含まないことが重要です。
:::

## Best Practice
:::message alert
絶対に `<script setup>` や `setup()` 関数の外側で `const state = ref()` を定義しないでください。  
このような状態は、ウェブサイトを訪れる全てのユーザーで共有され、メモリリークにつながる可能性があります。
:::
代わりに `const useX = () => useState('x')` を使用してください。

## Examples
### Basic Usage
この例では、コンポーネントローカルのカウンタステートを使用しています。`useState('counter')` を使用する他のコンポーネントは同じリアクティブステートを共有します。
```Vue:app.vue
<script setup>
const counter = useState('counter', () => Math.round(Math.random() * 1000))
</script>
<template>
  <div>
    Counter: {{ counter }}
    <button @click="counter++">
      +
    </button>
    <button @click="counter--">
      -
    </button>
  </div>
</template>
```

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/composables/use-state

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-state

### Advanced
この例では、HTTP リクエストヘッダからユーザのデフォルトロケールを見つけ、それをロケール状態に保持するコンポーザブルを使用します。

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/other/locale

## Shared State
自動インポートされるコンポーザブルを使うことで、グローバルに型安全な状態を定義し、アプリ全体でインポートすることができます。
```ts:composables/states.ts
export const useCounter = () => useState<number>('counter', () => 0)
export const useColor = () => useState<string>('color', () => 'pink')
```
```Vue:app.vue
<script setup>
const color = useColor() // Same as useState('color')
</script>

<template>
  <p>Current color: {{ color }}</p>
</template>
```