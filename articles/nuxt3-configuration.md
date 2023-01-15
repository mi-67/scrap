---
title: "[和訳] Nuxt3 公式サイト~Configuration"
emoji: "✨"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxt3","nuxtjs"]
published: false
---
# この記事について
この記事は[Nuxt3 公式サイト Configuration](https://nuxt.com/docs/getting-started/configuration) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/getting-started/configuration
所々自分の理解のために参考になりそうな別サイトのリンクを貼ってあります。

# Configuration
デフォルトでは、Nuxt はほとんどのユースケースをカバーするように設定されています。`nuxt.config.ts` ファイルに書かれているデフォルトの設定は上書きしたり拡張したりできます。

## Nuxt Configuration
`nuxt.congif.ts` ファイルは Nuxt プロジェクトのルートディレクトリに置かれていて、アプリケーションの挙動の上書きや拡張ができます。

最低限の設定ファイルは設定が書かれたオブジェクトを含む `defineNuxtConfig` 関数をエクスポートします。`defineNuxtConfig` ヘルパーはインポートしなくてもグローバルに使用可能です。
```ts:nuxt.config.ts
export default defineNuxtConfig({
    // Nuxt の設定
})
```
このファイルはカスタムスクリプトの追加、モジュールの登録、レンダリングモードの変更など、ドキュメントでしばしば言及されます。

全ての設定オプションについての説明は下記ページを参照してください。
https://nuxt.com/docs/api/configuration/nuxt-config

:::message
Nuxt でアプリケーションを構築するために TypeScript を使用する必要はありません。しかし、`nuxt.config` ファイルの拡張子には `.ts` を使用することを強く推奨します。こうすることで設定を編集している間のタイポや間違えを避けるために IDE からヒントの恩恵を受けることができます。
:::

## Environment Variables and Private Tokens
`runtimeConfig` API は環境変数のような値を値をアプリケーションの他の部分に公開します。デフォルトでは、これらのキーはサーバーサイドでのみ使用可能です。`runtimeConfig.public` 内のキーはクライアントサイドでも利用可能です。

これらの値は `nuxt.config` で定義されるべきであり、環境変数を使って上書きすることができます。

```ts:nuxt.config.ts
export default defineNuxtConfig({
    runtimeConfig: {
        // サーバーサイドでのみ使用可能なキー
        apiSecret: '123',
        public: {
            // この中に書かれているものはクライアントサイドでも利用可能
            apiBase: '/api'
        }
    }
})
```
```env:.env
# apiSecret の値を上書きします
NUXT_API_SECRET=api_secret_token
```
これらの変数は `usingRuntimeConfig` コンポーザブルを使用してアプリケーションの他の部分に公開されます。

詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/going-further/runtime-config

## App Configuration
`app.config.ts` ファイルはソースディレクトリ（デフォルトではプロジェクトのルート）に配置されていて、ビルド時に決定できるパブリック変数を公開するために使用されます。`runtimeConfig` オプションとは逆に、環境変数を上書きすることはできません。

最低限の設定ファイルは設定が書かれたオブジェクトを含む `defineAppConfig` 関数をエクスポートします。`defineAppConfig` ヘルパーははインポートしなくてもグローバルに使用可能です。
```ts:app.config.ts
export default defineAppConfig({
    title: 'Hello Nuxt',
    theme: {
        dark: true,
        colors: {
            primary: '#ff0000'
        }
    }
})
```
これらの変数は `useAppConfig` コンポーザブルを使用してアプリケーションの他の部分に公開されます。
```Vue:pages/index.vue
<script setup>
const appConfig = useAppConfig()
</script>
```
詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/app-config

## `runtimeConfig` vs `app.config`
上記のように、`runtimeConfig` と `app.config` はどちらもアプリケーションの他の部分に変数を公開するために使われます。どちらを使用するべきかを判断するためにいくつかのガイドラインがあります。
- `runtimeConfig`: ビルド後に環境変数を使用して明示する必要があるプライベートトークンまたはパブリックトークン
- `app.config`: ビルド時に決定されるパブリックトークン、テーマバリアントやタイトルなどのweb サイト設定、機密性のないプロジェクト設定

|機能・特徴|`runtimeConfig`|`app.config`|
|:-|:-|:-|
|クライアントサイド|ハイドレート|バンドル|
|環境変数|:white_check_mark:|:x:|
|リアクティブ|:white_check_mark:|:white_check_mark:|
|型のサポート|:white_check_mark: 一部|:white_check_mark:|
|リクエストごとの設定|:x:|:white_check_mark:|
|Hot Module Replacement|:x:|:white_check_mark:|
|プリミティブでないJS型|:x:|:white_check_mark:|

> 参考記事
> - [hydrate（ハイドレート）とはどんな意味なのか？](https://zenn.dev/smallstall/articles/5531fd6647f713)
> - [Hot Module Replacementの設定と仕組みを理解する](https://qiita.com/haradakunihiko/items/40486ec2b6b9aea119bb)

## External Configuration Files
Nuxt は設定のための単一の信頼できるソースとして `nuxt.config.ts` を使用し、外部設定ファイルの読みこみをスキップします。プロジェクトを構築する過程でそれらを設定する必要が生じるかもしれません。下の表は、一般的な構成と、該当する場合は Nuxt でどのように構成することができるかを示したものです。
|名前|設定ファイル|設定方法|
|:-|:-|:-|
|Nitro| ~~nitro.config.ts~~ |`nitro` キーを `nuxt.config` の中で使用|
|PostCSS|~~postcss.config.ts~~|`postcss` キーを `nuxt.config` の中で使用|
|Vite|~~vite.config.ts~~|`vite` キーを `nuxt.config` の中で使用|
|webpack|~~webpack.config.ts~~|`webpack` キーを `nuxt.config` の中で使用|

その他の一般的な設定ファイルの一覧はこちらです。
|名前|設定ファイル|設定方法|
|:-|:-|:-|
|TypeScript|tsconfig.json|[詳細情報](https://nuxt.com/docs/guide/concepts/typescript#nuxttsconfigjson)|
|Eslint|.eslintrc.js|[詳細情報](https://eslint.org/docs/latest/use/configure/configuration-files)|
|Prettier|.prettierrc.json|[詳細情報](https://prettier.io/docs/en/configuration.html)|
|Stylelint|.stylelintrc.json|[詳細情報](https://stylelint.io/user-guide/configure)|
|TailwindCSS|.tailwind.config.js|[詳細情報](https://tailwindcss.nuxt.dev/tailwind/config/)|
|Vitest|.vitest.config.ts|[詳細情報](https://vitest.dev/config/)|