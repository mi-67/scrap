---
title: "[和訳] Nuxt3 公式サイト~Assets Directory"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Assets Directory](https://nuxt.com/docs/guide/directory-structure/assets) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/directory-structure/assets

# Assets Directory
`assets/` ディレクトリはビルドツール（webpack または Vite）が処理する全てのウェブサイトのアセットを追加するために使用されます。

このディレクトリには通常次のようなファイルが含まれています。
- スタイルシート（CSS、SASS など）
- フォント
- `public/` ディレクトリから提供されない画像

サーバーからアセットを提供する場合は `public/` ディレクトリを参照することをお勧めします。

詳細は下記ページを参照してください。
https://nuxt.com/docs/getting-started/assets

翻訳したものはこちら
https://zenn.dev/mm67/articles/nuxt3-assets