---
title: "[和訳] Nuxt3 公式サイト~useCookie"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [nuxtjs,nuxt3]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト useCookie](https://nuxt.com/docs/api/composables/use-cookie) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/api/composables/use-cookie

# `useCookie`
ページ、コンポーネント、プラグイン内で、cookie を読み書きするための SSR フレンドリーなコンポーザブルである `useCookie` を使用できます。
```ts
const cookie = useCookie(name, options)
```
`useCookie` は `setup` または `Lifecycle Hooks` の間だけで動作します。

`useCookie` ref は cookie の値を自動的に JSON にシリアライズ、デシリアライズします。

## Example
以下の例では、`counter` という cookie を生成しています。この cookie が存在しない場合、最初にランダムな値が設定されます。`counter` 変数を更新するたびに、それに応じて cookie も更新されます。
```Vue
<template>
  <div>
    <h1>Counter: {{ counter || '-' }}</h1>
    <button @click="counter = null">reset</button>
    <button @click="counter--">-</button>
    <button @click="counter++">+</button>
  </div>
</template>
<script setup>
const counter = useCookie('counter')
counter.value = counter.value || Math.round(Math.random() * 1000)
</script>
```

## Options
cookie コンポーザブルは cookie の挙動を変更するためのいくつかのオプションを受け付けます。

ほとんどのオプションは cookie パッケージに直接渡されます。

### `maxAge` / `expires`
`maxAge`：`Max-Age` `Set-Cookie` 属性の値となる数値（秒）を指定します。与えられた数値は切り捨てることで整数に変換されます。デフォルトでは、最大エイジは設定されていません。

`expires`：`Expires` `Set-Cookie` 属性の値となる `Date` オブジェクトを指定します。デフォルトでは、有効期限は設定されていません。ほとんどのクライアントはこれを「非永続的な cookie」とみなし、ウェブブラウザアプリケーションを終了するなどの条件でこれを削除します。

注意：cookie ストレージモデルの仕様では、`expires` と `maxAge` の両方が設定されている場合、`maxAge` が優先されますが、全てのクライアントがこれに従うとは限らないので、両方が設定されている場合、同じ日時を指す必要があります！

`expires` と `maxAge` のどちらも設定されていない場合、cookie はセッション専用となり、ユーザがブラウザを閉じた時に削除されます。

### `httpOnly`
`HttpOnly` `Set-Cookie` 属性の値となる真偽値を指定します。真値の場合は、`HttpOnly` 属性が設定され、そうでなければ設定されません。デフォルトでは、`HttpOnly` 属性は設定されていません。

注意：コンプライアントクライアントでは、クライアントサイドの JavaScript が `document.cookie` にある cookie をみることができないので、これを `true` に設定する場合は注意してください。

### `secure`
`Secure` `Set-Cookie` 属性の値となる真偽値を指定します。真値の場合は、`Secure` 属性が設定され、そうでなければ設定されません。デフォルトでは、`Secure` 属性は設定されていません。

注意：これを `true` に設定したとき、ブラウザは HTTPS 接続でない場合、コンプライアントクライアントは今後 cookie をサーバーに送り返さないので注意してください。これにより、ハイドレーションエラーが発生する可能性があります。

### `domain`
`Domain` `Set-Cookie` 属性となる値を指定します。デフォルトでは、ドメインは設定されておらず、ほとんどのクライアントは現在のドメインにのみ cookie を適用することを考慮します。

### `path`
`Path` `Set-Cookie` 属性となる値を指定します。デフォルトでは、パスは "[default path](https://www.rfc-editor.org/rfc/rfc6265#section-5.1.4)" とみなされます。
https://www.rfc-editor.org/rfc/rfc6265#section-5.1.4

### `sameSite`
`SameSite` `Set-Cookie` 属性となる `boolean` または `string` の値を指定します。
- `true` を設定すると、`SameSite` 属性が `Strict` に設定され、同じサイトを厳格に強制するようになります。
- `false` を設定すると、`SameSite` 属性は設定されません。
- `'lax'` を設定すると、`SameSite` 属性を `Lax` にし、緩やかな同サイトの施行を行います。
- `'none'` を設定すると、明示的なクロスサイト cookie に対して `SameSite` 属性を `None` にします。
- `'strict'` を設定すると、`SameSite` 属性を `Strict` にし、同じサイトを厳格に強制するようになります。

各実施レベルの詳細については、[仕様書](https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-rfc6265bis-03#section-4.1.2.7)に記載されています。
https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-rfc6265bis-03#section-4.1.2.7

### `encode`
cookie の値をエンコードするために使用される関数を指定します。cookie の値は文字数が限られている（単純な文字列でなければいけない）ため、この関数は値を cookie の値に適した文字列にエンコードするために使用することができます。

デフォルトのエンコーダーは `JSON.stringify` + `encodeURIComponent` です。

### `decode`
cookie の値をデコードするために使用される関数を指定します。cookie の値は文字数が限られている（単純な文字列でなければいけない）ため、この関数は以前にエンコードした cookie の値を JavaScript 文字列や他のオブジェクトにデコードするために使用することができます。

デフォルトのエンコーダーは `decodeURIComponent` + [destr](https://github.com/unjs/destr) です。
https://github.com/unjs/destr

注意：関数からエラーが投げられた場合、デコードされていないオリジナルの cookie 値が cookie 値として返されます。

### `default`
cookie のデフォルト値を返す関数を指定します。この関数は `Ref` を返すこともできます。

### `watch`
監視 cookie ref データの `boolean` または `string` の値を指定します。
- `true` - cookie ref データの変更とそのネストされたプロパティを監視します（デフォルト）。
- `shallow` - トップレベルのプロパティについて、cookie ref データの変更を監視します。
- `false` - cookie ref データの変更を監視しません。

#### Example 1:
```Vue
<template>
  <div>User score: {{ user?.score }}</div>
</template>
<script setup>
const user = useCookie(
  'userInfo',
  {
    default: () => ({ score: -1 }),
    watch: false
  }
)
if (user.value && user.value !== null) {
  user.value.score++; // userInfo cookie はこの変更で更新されません
}
</script>
```

#### Example 2:
```Vue
<template>
  <div>
    <h1>List</h1>
    <pre>{{ list }}</pre>
    <button @click="add">Add</button>
    <button @click="save">Save</button>
  </div>
</template>
<script setup>
const list = useCookie(
  'list',
  {
    default: () => [],
    watch: 'shallow'
  }
)
function add() {
  list.value?.push(Math.round(Math.random() * 1000))
  // list cookie はこの変更で更新されません
}
function save() {
  if (list.value && list.value !== null) {
    list.value = [...list.value]
    // list cookie はこの変更で更新されます
  }
}
</script>
```

## Handling Cookies in API Routes
サーバー API ルートに cookie を設定するために、h3 パッケージの `getCookie` と `setCookie` を使用できます。

**例**：
```ts
export default defineEventHandler(event => {
  // counter cookie を読み込む
  let counter = getCookie(event, 'counter') || 0
  // counter cookie を 1 増やす
  setCookie(event, 'counter', ++counter)
  // JSON レスポンスを返す
  return { counter }
})
```

下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/composables/use-cookie

