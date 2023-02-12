---
title: "[和訳] Nuxt3 公式サイト~Server Engine"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxt3,nuxtjs]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Server Engine](https://nuxt.com/docs/guide/concepts/server-engine) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/guide/concepts/server-engine


# Server Engine
Nuxr3 は新しいサーバーエンジンである Nitro を搭載しています。
✅ Node.js、ブラウザ、サービスワーカーなど、クロスプラットフォームに対応
✅ サーバーレスを設定なしでサポート
✅ API ルート対応
✅ 自動コード分割と非同期ロードチャンク
✅ 静的サイト + サーバーレスサイトのハイブリッドモード
✅ ホットモジュールローディングが可能な開発サーバー

## API Layer
サーバー API エンドポイントとミドルウェアは内部で [h3](https://github.com/unjs/h3) を使用している Nitro によって追加されます。
https://github.com/unjs/h3

主な特徴は以下の通りです。
- ハンドラは、自動的に処理される JSON レスポンスに対して、オブジェクト / 配列を直接返すことができます。
- ハンドラは、待ち受けとなるプロミスを返すことができます（`res.end()` 、`next()` もサポートされています）。
- ボディ解析、Cookie 処理、リダイレクト、ヘッダーなどのヘルパー関数があります。

詳しくは [h3 のドキュメント](https://github.com/unjs/h3)をご覧ください。
https://github.com/unjs/h3

詳細は下記ページを参照してください。
https://nuxt.com/docs/guide/directory-structure/server

## Direct API Calls
Nitro ではグローバルに使用可能な $fetch ヘルパー経由でルートを「直接」呼び出すことできます。これはブラウザ上で実行されている場合はサーバーへの API 呼び出しを行いますが、サーバー上で実行されている場合は、関連する関数を直接呼び出すので、追加の API 呼び出しを節約することができます。

`$fetch` API は ofetch を使用しており、主な機能は以下の通りです。
- JSON レスポンスの自動解析（必要に応じて生レスポンスへのアクセスも可能）
- 正しい `Content-Type` ヘッダーをつけたリクエストボディとパラメータの自動的な処理

`$fetch` の特徴についての詳細な情報は、[ofetch](https://github.com/unjs/ofetch) をご覧ください。
https://github.com/unjs/ofetch

## Typed API Routes
API ルート（またはミドルウェア）を使用する時、Nitro はレスポンスを送るために `res.end()` 使う代わりに値を返す限りこれらのルートの型を生成します。

`$fetch()` や `useFetch()` を使用するとこれらの型にアクセスできます。

## Standalone Server
Nitro は `node_modules` に依存しないスタンドアローンなサーバー配布物を生成します。

Nuxt2 のサーバーはスタンドアローンではなく、`nuxt start` の実行（`nuxt-start` や `nuxt` ディストリビューションを使用）やカスタムプログラム的な使用によって Nuxt コアの一部が関与する必要があり、壊れやすく、サーバーレスやサービスワーカーの環境には向いていません。

Nuxt3 では `.output` ディレクトリで `nuxt build` を実行することでこの配布物を生成します。

出力にはあらゆる環境（実験的なブラウザサービスワーカーを含む！）で Nuxt サーバーを実行し、静的ファイルを提供するためのランタイムコードが含まれおり、JAMstack のための真のハイブリッドフレームワークとなってます。加えて、Nuxt はネイティブストラテジーレイヤーを実装しており、マルチソースドライバーとローカルアセットをサポートしています。

Nitro エンジンについて GitHub チェックしてください：[unjs/nitro](https://github.com/unjs/nitro)
https://github.com/unjs/nitro