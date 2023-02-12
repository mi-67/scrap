---
title: "[和訳] Nuxt3 公式サイト~Output Directory"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Output Directory](https://nuxt.com/docs/guide/directory-structure/output) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/output

# Output Directory
Nuxt はアプリケーションを本番用にビルドする際に `.output/` ディレクトリを作成します。

:::message
`nuxt build` を実行するとディレクトリ全体が再生成されるので、このディレクトリの中のあらゆるファイルに触らないようにすべきです。
:::

このディレクトリはアプリケーションを本番環境にデプロイするために使用します。詳しくは[デプロイメントのセクション](https://nuxt.com/docs/getting-started/deployment)をご覧ください。
https://nuxt.com/docs/getting-started/deployment