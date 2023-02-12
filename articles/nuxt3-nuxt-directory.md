---
title: "[和訳] Nuxt3 公式サイト~.nuxt Directory"
emoji: "🌟"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト .nuxt Directory](https://nuxt.com/docs/guide/directory-structure/nuxt) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/nuxt



# .nuxt Directory
Nuxt は開発時に `.nuxt/` ディレクトリを使い、Vue アプリケーションを生成します。

:::message
`nuxt dev` を実行するとディレクトリ全体が再生成されるので、このディレクトリの中のあらゆるファイルに触らないようにすべきです。
:::

このディレクトリは、Nuxt がディレクトリ構造に基づいて生成するファイルについてもっと知りたい場合に興味深いものです。

## Virtual File System
Nuxt はまた、テンプレートをディスクに書き込まず追加するためのモジュール用の仮想ファイルシステム（VFS）を提供します。生成されたファイルについて調べるためには、http://localhost:3000/_vfs に移動してください。