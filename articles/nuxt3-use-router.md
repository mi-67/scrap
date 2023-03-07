---
title: "[和訳] Nuxt3 公式サイト~useRouter"
emoji: "🎃"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト useRouter](https://nuxt.com/docs/api/composables/use-router) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-router

# `useRouter`
useRouter コンポーザブルはルーターインスタンスを返すので、セットアップ関数、プラグイン、ルートモジュールの中で呼び出す必要があります。

Vue コンポーネントのテンプレートの中では、代わりに `$route` を使用してルーターにアクセスできます。

`pages/` フォルダがある場合、`useRouter` は vue-router が提供するものと動作は同じです。各メソッドが何をするのかについてのより多くの情報は、ルーターのドキュメントを自由に読んでください。

詳細は下記ページを参照してください。
https://router.vuejs.org/api/#currentroute

## Basic Manipulation
- **addRoute**：新しいルートをルーターインスタンスに追加します。`parentName` は既存のルートの子として新しいルートを追加することで提供できます。
- **removeRoute**：既存のルートをその名前で削除します。
- **getRoutes**：全ルートレコードの完全なリスト取得します。
- **hasRoute**：与えられた名前のルートが存在するかを確認します。

## Based on History API
- **back**：可能であれば履歴を遡ります。`router.go(-1)` と同じです。
- **forward**：可能であれば履歴を先に進めます。`router.go(1)` と同じです。
- **go**：`router.back()` や `router.forward()` のような階層的な制限を受けずに履歴を前方や後方に移動することができます。
- **push**：履歴スタックのエントリーをプッシュすることで新しい URL にプログラム的にナビゲートします。**代わりに `navigateTo` を使用することをお勧めします**。
- **replace**：履歴スタックの現在のエントリーを置き換えることで新しい URL にプログラム的にナビゲートします。**代わりに `navigateTo` を使用することをお勧めします**。


> TIP： `router.addRoute()` はルートの配列にルートの詳細を追加し、Nuxt プラグインを構築するときに便利です。一方、`router.push()`　は新しいナビゲーションをすぐにトリガーし、Nuxt ページコンポーネント、Vue コンポーネント、コンポーザブルで便利です。

```js
const router = useRouter();
router.back();
router.forward();
router.go();
router.push({ path: "/home" })
router.replace({ hash: "#bio" })
```
詳細は下記ページを参照してください。
https://developer.mozilla.org/en-US/docs/Web/API/History

## Navigation Guards
`useRouter` コンポーザブルはナビゲーションガードとして機能する `afterEach`、`beforeEach`、`beforeResolve` ヘルパーメソッドを提供します。

しかし、Nuxt はルートミドルウェアというコンセプトがあり、ナビゲーションガードの実装を簡略化し、より良い開発者体験を提供します。

詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/middleware

## Promise and Error Handling
- **isReady**：ルーターが初期のナビゲーションが完了したときに解決する Promise を返します。
- **onError**：ナビゲーション中にキャッチされないエラーが発生するたびに毎回呼ばれるエラーハンドラを追加します。
- **resolve**：ルートロケーションの正規化されたバージョンを返します。また、既存の任意のベースを含む href プロパティを含みます。

詳細は下記ページを参照してください。
https://router.vuejs.org/api/#router-methods

## Universal Router Instance
`pages/` フォルダがない場合、`useRoute` は同様のヘルパーメソッドを持つユニバーサルルーターインスタンスを返しますが、全ての機能が `vue-router` と全く同じようにサポートされたり動作したりするわけではないことに注意してください。