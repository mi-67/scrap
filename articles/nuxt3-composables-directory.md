---
title: "[和訳] Nuxt3 公式サイト~Composables Directory"
emoji: "🌟"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Composables Directory](https://nuxt.com/docs/guide/directory-structure/composables) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/composables

# Composables Directory
Nuxt3 は `composables/` ディレクトリを使用し、自動インポートを使って、Vue コンポーザブルをアプリケーションに自動的にインポートします！

Nuxt の内部では、型を宣言するためのファイル `.nuxt/imports.d.ts` が自動生成されます。

Nuxt に型を生成させるために `nuxi prepare`、`nuxi dev`、`nuxi build` を実行しなければならないことに注意してください。開発サーバーが起動していない状態でコンポーザブルを作成した場合、	TypeScript が `Cannot find name 'useBar'.` のようなエラーを投げます。

## Usage
**方法１**：名前付きエクスポートの使用
```ts:composables/useFoo.ts
export const useFoo = () => {
  return useState('foo', () => 'bar')
}
```
**方法2**：デフォルトエクスポートの使用
```ts:composables/use-foo.ts or composables/useFoo.ts
// useFoo() として使用できるようになります(拡張子なしのキャメルケース)
export default function () {
  return useState('foo', () => 'bar')
}
```

**使用方法**：`.js`、`.ts`、`.vue` ファイルで自動インポートされたコンポーザブルが使用できます
```Vue:app.vue
<template>
  <div>
    {{ foo }}
  </div>
</template>
<script setup>
const foo = useFoo()
</script>
```

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/auto-imports/composables

## Examples
### Nested Composables
自動インポートを使用して、他のコンポーザブルの中であるコンポーザブルを使用できます。
```ts:composables/test.ts
export const useFoo = () => {
  const nuxtApp = useNuxtApp()
  const bar = useBar()
}
```

### Access plugin injections
コンポーザブルからプラグインインジェクションにアクセスできます。
```ts:composables/test.ts
export const useHello = () => {
  const nuxtApp = useNuxtApp()
  return nuxtApp.$hello
}
```

## How Files Are Scanned
Nuxt は `composables/` ディレクトリにあるトップレベルのファイルのみをスキャンします。例えば
```
components/
├ index.ts // スキャンされる
├ useFoo.ts // スキャンされる
└ nested
  └ utils.ts // スキャンされない
```

`components/index.ts` と `components/useFoo.ts` だけがインポート検索されます。

ネストしたモジュールの自動インポートを動作させるには、モジュールを再エクスポートするか（推奨）、ネストしたディレクトリを含むようにスキャナーを設定します。

**例**：`composables/index.ts` ファイルから必要なコンポーザブルを再エクスポートする
```ts:composables/index.ts
// このエクスポートの自動インポートを有効にする
export { utils } from './nested/utils.ts'
```

**例**：`composables/` フォルダ内のネストしたディレクトリをスキャンする
```ts:nuxt.config.ts
export default defineNuxtConfig({
  imports: {
    dirs: [
      // トップレベルモジュールのスキャン
      'composables',
      // ...または、特定の名前とファイル拡張子で１レベル深くネストされたモジュールをスキャン
      'composables/*/index.{ts,js,mjs,mts}',
      // ... または、与えられたディレクトリないの全てのモジュールをスキャン
      'composables/**'
    ]
  }
})
```