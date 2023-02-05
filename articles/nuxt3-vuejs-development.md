---
title: "[和訳] Nuxt3 公式サイト~Vue.js Development"
emoji: "🐕"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxt3,nuxtjs]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Vue.js Development](https://nuxt.com/docs/guide/concepts/vuejs-development) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/concepts/vuejs-development
所々自分の理解のために参考になりそうな別サイトのリンクを貼ってあります。

# Vue.js Development
Nuxt は Vue をフロントエンドフレームワークとして使用し、コンポーネントの自動インポートやファイルベースのルーティングなどの機能を追加しています。Nuxt3 は Vue の新しいメジャーリリースである Vue3 を統合し、Nuxt ユーザのための新しいパターンを可能にします。
> Nuxt を使うために Vue の深い知識は必要としませんが、ドキュメントを読んで [vuejs.org](https://vuejs.org/) にあるいくつかの例を見てみることをおすすめします。

https://vuejs.org/

Nuxt は常に Vue をフロントエンドフレームワークとして使用してきました。Nuxt を Vue の上に構築することを選択したのは以下のような理由からです。
- Vue のリアクティビティモデルで、データの変化が自動的にインタフェースの変化を引き起こすため。
- コンポーネントベースのテンプレートは、HTML をウェブの共通言語として保持し、直感的なパターンを可能にし、インタフェースの一貫性を保ちながら強力なパフォーマンスを発揮するため。
- 小さなプロジェクトから大規模なウェブアプリケーションまで、Vue はスケーラブルに優れたパフォーマンスをし続け、アプリケーションがユーザに価値を提供し続けることを保証するため。
 
## Vue with Nuxt
### Single File Components
Vue のシングルファイルコンポーネント（SFC または `*.vue` ファイル）は Vue コンポーネントのマークアップ( `<template>` )、ロジック（ `<script>` ）、スタイル（ `<style>` ）をカプセル化したものです。Nuxt はシームレスな開発者体験を提供する Hot Module Replacement により SFC のゼロコンフィグレーション体験を提供します。

### Components Auto-imports
Nuxt プロジェクトの `components/` ディレクトリに作成された全ての Vue コンポーネントは、インポートしなくてもプロジェクト内で使用可能です。コンポーネントがどこにも使用されていない場合は、プロダクションコードにそれは含まれません。

### Vue Router
ほとんどのアプリケーションは複数のページとそれらの間を移動する方法が必要です。これはルーティングと呼ばれます。Nuxt は `pages/` ディレクトリと命名規則を使用し、公式の Vue Router ライブラリを使ってファイルにマッピングされたルートを直接作成します。

### Example
[StackBlitz](https://stackblitz.com/edit/github-9hzuns?file=app.vue) で開く
https://stackblitz.com/edit/github-9hzuns?file=app.vue

[CodeSandbox](https://codesandbox.io/s/nuxt-3-components-auto-import-2xq9z?file=/app.vue) で開く
https://codesandbox.io/s/nuxt-3-components-auto-import-2xq9z?file=/app.vue

`app.vue` ファイルはエントリポイントになり、ブラウザウィンドウに表示されるページを表現します。

コンポーネントの `<template>` の中では、`components/` ディレクトリに作成された `<Welcome>` コンポーネントをインポートすることなく使用しています。

`<template>` の中身を独自のウェルカムメッセージに置き換えてみてください。右にあるブラウザウィンドウはリロードしなくても変更を自動的に再描画します。

:::message
Vue に慣れている人なら、Vue アプリのインスタンスを作成する `main.js` を探しているかもしれません。Nuxt はこれを舞台裏で自動的に処理しています。
:::

Nuxt2 または Vue2 の以前のユーザであれば、Vue2 と Vue3 の違いおよび Nuxt がこれらの進化をどのように統合しているかを知るために読み続けてください。

そうでなければ、次の章に進んで Nuxt のもう一つの鍵となる機能：レンダリングモードを学んでください。

## Differences with Nuxt2 / Vue2
Nuxt3 は Vue3 に基づいています。新しい Vue のメジャーバージョンには　Nuxt が活用できるいくつかの変更が導入されています。
- より良いパフォーマンス
- Composition API
- TypeScript のサポート

### Faster Rendering
Vue Virtual DOM (VDOM)　は最初から書き直され、より良いレンダリングパフォーマンスを可能にしました。その上、コンパイルされたシングルファイルコンポーネント（SFC）を扱う場合、Vue コンパイラは静的マークアップと動的マークアップを分離することでビルド時にさらに最適化できます。

その結果、最初のレンダリング（コンポーネント作成）と更新が高速化され、メモリ使用量も少なくなりました。Nuxt3 では、サーバーサイドレンダリングの高速化も可能です。

### Smaller Bundle
Vue3 と Nuxt3 では、バンドルサイズの縮小に重点が置かれています。バージョン3では、テンプレートディレクティブや組み込みコンポーネントなど Vue の機能のほとんどがツリーシェイク可能になっています。それらを使用しない場合にはプロダクションバンドルには含まれません。

この方法で、最小限の Vue3 アプリケーションを gzip で 12KB に減らすことができます。

https://developer.mozilla.org/ja/docs/Glossary/Tree_shaking


### Composition API
Vue2 でコンポーネントにデータとロジックを提供する唯一の方法は、Options API を使用することでした。Options API は `data` や `method` などあらかじめ定義されたプロパティをテンプレートに返すことができます。

```Vue
<script>
export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    increment() {
      this.count++
    }
  }
}
</script>
```

Vue3 で導入された Composition API は Options API を置き換えるものではないですが、アプリケーション全体でより良いロジックの再利用を可能にし、複雑なコンポーネントで関心事項ごとにコードをグループ化する自然な方法となります。

`<script>` 定義の `setup` キーワードと一緒に使い、Composition API と Nuxt3 の自動インポートされた Reactivity API で上記のコンポーネントを書き直したものがこちらです。
```Vue
<script setup>
  const count = ref(0)
  const increment = () => count.value++
</script>
```
Nuxt3 のゴールは Composition API を中心とした素晴らしい開発者体験を提供することです。
- Vue と Nuxt3 の組み込みコンポーザブルから自動インポートされたリアクティビティ関数を使います
- `composables/` ディレクトリに自動インポートされる再利用可能な関数を書いてください。

### TypeScript Support
Vue3 と Nuxt3 はどちらも TypeScript で書かれています。完全に片付けされたコードベースは間違いを防ぎ、APIの使い方をドキュメント化します。これはアプリケーションを TypeScript で書く必要があるということではありません。Nuxt3 では、ファイル名を `.js` から `.ts` に変えたり、コンポーネント内に `<script lang="ts">` を追加することでオプトインできます。

詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/concepts/typescript