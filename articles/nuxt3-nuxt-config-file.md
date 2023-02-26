---
title: "[和訳] Nuxt3 公式サイト~Nuxt Config File"
emoji: "👋"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
published_at: 2023-02-26 09:00
---
# この記事について
この記事は[Nuxt3 公式サイト Nuxt Config File](https://nuxt.com/docs/guide/directory-structure/nuxt.config) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/nuxt.config

# Nuxt Config File
Nuxt は拡張子が `.js`、`.ts`、`.mjs` のいずれかの `nuxt.config` ファイル１つで簡単に設定できます。
```ts
export default defineNuxtConfig({
  // Nuxt の設定
})
```
`defineNuxtConfig` ヘルパーはインポートせずグローバルに利用可能です。

必要があれば、`nuxt/config` から `defineNuxtConfig` を明示的にインポートすることができます。

```ts
import { defineNuxtConfig } from 'nuxt/config'
export default defineNuxtConfig({
  // Nuxt の設定
})
```
設定が最新であることを保証するために、メイン設定ファイル、`.env`、`.nuxtignore`、`.nuxtrc` ドットファイルに変更を検出したときに Nuxt は完全な再起動を実行をします。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/configuration/nuxt-config