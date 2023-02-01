---
title: "[和訳] Nuxt3 公式サイト~Testing"
emoji: "📌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxt3","nuxtjs"]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Testing](https://nuxt.com/docs/getting-started/testing) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/getting-started/testing
所々自分の理解のために参考になりそうな別サイトのリンクを貼ってあります。

# Testing
Nuxt アプリケーションのテスト方法

::: message
Test utils はまだ開発中であり、API や挙動が変更される可能性があります。現在、プレビューの段階ですが、まだ本番アプリをテストする準備ができていません。モジュール作成者の方は、モジュール作成者ガイドで具体的な情報を得ることができます。
:::

Nuxt3 では、`@nuxt/test-utils` を書き直したバージョンがあります。テストランナーとして、`Vitest` と `Jest` をサポートしています。

## Installation
```bash
> yarn add --dev @nuxt/test_utils vitest
```

## Setup
`nuxt/test_utils` ヘルパーメソッドを利用する各説明ブロックでは、開始前にテストコンテキストをセットアップする必要があります。
```ts
import { describe, test } from 'vitest'
import { setup, $fetch } from '@nuxt/test-utils'

describe('My test', async () => {
  await setup({
    // テストコンテキストのオプション
  })

  test('my test', () => {
    // ...
  })
})
```
裏側では、`beforeAll`、`beforeEach`、`afterEach`、`afterAll` の中で Nuxt のテスト環境を正しくセットアップするためのさまざまなタスクが `setup` によって実行されます。

## Options
### Nuxt Configuration
#### `rootDir`
テスト対象となる Nuxt アプリがあるディレクトリへのパス
- 型: string
- デフォルト値: `'.'`

#### `configFile`
設定ファイルの名前
- 型: string
- デフォルト値: `'nuxt.config'`

### Setup Timings
#### `setupTimeout`
`setupTest` が作業を完了するまでの時間（ミリ秒）（渡されたオプションに応じて、Nuxt アプリケーション用のファイルの構築または生成を含むことができる）
- 型: number
- デフォルト値: 60000

### Features to Enable
#### `server`
テストスイート内のリクエストに応答するサーバーを起動するかどうか
- 型: boolean
- デフォルト値: true

https://www.itmedia.co.jp/im/articles/1111/07/news187.html

#### `build`
別のビルドステップを実行するかどうか
- 型: boolean
- デフォルト値: true（`browser` か `server` が無効な場合は `false` ）

#### `browser`
Nuxt test utils は内部で `playwright` を使用してブラウザテストを行います。このオプションを設定すると、ブラウザが起動し、その後のテストスイート内で制御できるようになります。
- 型: boolean
- デフォルト値: false

#### `browserOptions`
- 型: 以下のオプションを持つオブジェクト
  - type: 起動するブラウザの種類 - `chromium`、`firefox`、`webkit` のいずれかを指定
  - launch: ブラウザの起動時に `playwright` に渡されるオプションのオブジェクトです。詳細は[こちら](https://playwright.dev/docs/api/class-browsertype#browser-type-launch)

#### `runner`
テストスイート用のランナーを指定します。現在、Vitest を推奨しています。
- 型: `'vitest' | 'jest'`
- デフォルト値: `'vitest'`

## APIs
### APIs for Rendering Testing
#### `$fetch(url)`
サーバーでレンダリングされたページの HTML を取得します。
```ts
import { $fetch } from '@nuxt/test-utils'

const html = await $fetch('/')
```

#### `fetch(url)`
サーバーレンダリングされたページのレスポンスを取得します。
```ts
import { fetch } from '@nuxt/test-utils'

const res = await fetch('/')
const { body, headers } = res
```

#### `url(path)`
指定されたページの完全な URL を取得します（テストサーバーが動作しているポートを含みます）。
```ts
import { url } from '@nuxt/test-utils'

const pageUrl = url('/page')
// 'http://localhost:6840/page'
```

## Testing in a Browser
:::message
現在執筆中、ご期待ください！
:::