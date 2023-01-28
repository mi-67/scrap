---
title: "[和訳] Nuxt3 公式サイト~Data Fetching"
emoji: "🐙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxt3","nuxtjs"]
published: true
---
# この記事について
この記事は[Nuxt3 公式サイト Data Fetching](https://nuxt.com/docs/getting-started/data-fetching) を和訳したものになります（日本語が不自然になってしまっている箇所があるのはごめんなさい）。
https://nuxt.com/docs/getting-started/data-fetching

# Data Fetching
Nuxt はアプリケーション内でデータの取得を処理するために `useFetch`、`useLazyFetch`、`useAsyncData`、`useAsyncLazyData` を提供します。

:::message
`useFetch`、`useLazyFetch`、`useAsyncData`、`useAsyncLazyData` は `setup` の時または `Lifecycle Hooks` の時のみ機能します。
:::

## `useFetch`
ページ内、コンポーネント内、プラグイン内で、あらゆる URL から広くデータを取得するために `useFetch` を使うことができます。

このコンポーザブルは `useAsyncData` と `$fetch` の便利なラッパーを提供します。URL とフェッチオプションに基づいてキーを自動的に生成し、サーバールートに基づいてリクエスト URL の型のヒントを提供し、API のレスポンス型を推論します。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-fetch

### Example
```Vue:app.vue
<script setup>
const {data: count} = await useFetch('/api/count')
</script>

<template>
    Page visits: {{ count }}
</template>
```
下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/composables/use-fetch

## `useLazyFetch`
このコンポーザブルは `lazy:true` オプションが設定された `useFetch` と同じように振る舞います。言い換えれば、非同期関数はナビゲーションをブロックしません。これはデータが null（またはカスタムのデフォルトファクトリで提供した値）である場合の処理をする必要があることを意味します。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-lazy-fetch

### Example
```Vue
<template>
  <!-- 読み込み状態を処理する必要があります -->
  <div v-if="pending">
    Loading ...
  </div>
  <div v-else>
    <div v-for="post in posts">
      <!-- 何らかの処理 -->
    </div>
  </div>
</template>

<script setup>
const { pending, data: posts } = useLazyFetch('/api/posts')
watch(posts, (newPosts) => {
  // posts は最初 null なのですぐにその中身のアクセスすることはできませんが、ウォッチすることはできます。
})
</script>
```

## `useAsyncData`
ページ、コンポーネント、プラグインの中で、`useAsyncData` を使用して非同期に解決されるデータにアクセスすることができます。

:::message
`useFetch` と `useAsyncData` の違いは何ですか？簡単にいうと、`useFetch` は URL を受け取って、そのデータを取得しますが、`useAsyncData` はより複雑なロジックを持つ可能性があります。`useFetch(url)` は `useAsyncData(url,()=>$fetch(url))` とほぼ同義で、最も一般的な使用例に対する開発者の糖衣経験のようなものです。
:::

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-async-data

### Example
```ts:server/api/count.ts
let counter = 0
export default () => {
  counter++
  return JSON.stringify(counter)
}
```
```Vue:app.vue
<script setup>
const { data } = await useAsyncData('count', () => $fetch('/api/count'))
</script>
<template>
  Page visits: {{ data }}
</template>
```
下記ページで実際の例を見たり編集したりできます。
https://nuxt.com/docs/examples/composables/use-async-data

## `useLazyAsyncData`
このコンポーザブルは `lazy:true` オプションが設定された `useAsyncData` と同じように振る舞います。言い換えれば、非同期関数はナビゲーションをブロックしません。これはデータが null（またはカスタムのデフォルトファクトリで提供した値）である場合の処理をする必要があることを意味します。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/composables/use-lazy-async-data

### Example
```Vue
<template>
  <div>
    {{ pending ? 'Loading' : count }}
  </div>
</template>
<script setup>
const { pending, data: count } = useLazyAsyncData('count', () => $fetch('/api/count'))
watch(count, (newCount) => {
  // count は最初 null なのですぐにその中身のアクセスすることはできませんが、ウォッチすることはできます。
})
</script>
```
## Refreshing Data
ページを訪問する過程で、API から読み込んだデータを更新する必要がある場合があります。これはページ送り、結果のフィルタリング、検索などを選択した場合に発生する可能性があります。

`useFetch()` コンポーザブルから返される `refresh()` メソッドを利用して、異なるクエリパラメータでリフレッシュすることが可能です。

```Vue
<script setup>
const page = ref(1);
const { data: users, pending, refresh, error } = await useFetch(() => `users?page=${page.value}&take=6`, { baseURL: config.API_BASE_URL }
);
function previous() {
  page.value--;
  refresh();
}
function next() {
  page.value++;
  refresh();
}
</script>
```
これを実現する鍵は、クエリパラメータが変わったときに `useFetch()` コンポーザブルから返される `refresh()` メソッドを呼ぶことです。

デフォルトでは、`refresh()` は保留中のあらゆるリクエストをキャンセルし、その結果データや保留中の状態は更新されません。この新しいリクエストが解決されるまで、以前に待機していたプロミスは解決しません。この挙動は `dedupe` オプションを設定することで防ぐことができます。`dedupe` オプションは現在実行中のリクエストに対するプロミスがある場合に、代わりにそれを返します。

```ts
refresh({ dedupe: true })
```
### `refreshNuxtData`
`useAsyncData`、`useLazyAsyncData`、`useFetch`、`useLazyFetch` のキャッシュを無効化し、リフェッチをトリガーします。

このメソッドは現在のページの全てのデータ取得をリフレッシュしたいときに便利です。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/utils/refresh-nuxt-data

#### Example
```Vue
<template>
  <div>
    {{ pending ? 'Loading' : count }}
  </div>
  <button @click="refresh">Refresh</button>
</template>
<script setup>
const { pending, data: count } = useLazyAsyncData('count', () => $fetch('/api/count'))
const refresh = () => refreshNuxtData('count')
</script>
```

### `clearNuxtData`
`useAsyncData` と `useFetch` のキャッシュされたデータ、エラーステータス、保留中のプロミスを削除します。

このメソッドは別ページのデータ取得を無効にしたい場合に便利です。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/utils/clear-nuxt-data

## Options API support
Nuxt3 は Options API 内で `asyncData` を実行する方法を提供します。これを動作させるためには、`defineNuxtComponent` でコンポーネント定義をラップする必要があります。
```Vue
<script>
export default defineNuxtComponent({
  fetchKey: 'hello',
  async asyncData () {
    return {
      hello: await $fetch('/api/hello')
    }
  }
})
</script>
```
:::message
Nuxt3 で Vue コンポーネントを宣言するには `<script setup lang="ts">` を使用することが推奨されます。
:::

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/utils/define-nuxt-component

## Isomorphic `fetch` and `$fetch`
ブラウザで `fetch` を呼び出すと、cookie のようなユーザヘッダが直接 API へ送られます。しかしサーバサイドレンダリングでは、フェッチリクエストはサーバーの「内部」で行われるのでブラウザの cookie は含まれませんし、フェッチレスポンスから cookie が渡されるわけではありません。

詳細は下記ページを参照してください。
https://nuxt.com/docs/api/utils/dollarfetch

### Example: Pass Client Headers to the API
`useRequestHeaders` を使用し、サーバサイドから API にアクセス、cookie をプロキシすることができます。

下の例では、同型の `$fetch` 呼び出しにリクエストへッダを追加し、API エンドポイントがユーザによって最初に送信されたのと同じクッキーヘッダにアクセスできるようにしています。
```Vue
<script setup>
const headers = useRequestHeaders(['cookie'])
const { data } = await useFetch('/api/me', { headers })
</script>
```
:::message
外部の API にヘッダをプロキシする前に、必要なヘッダだけを含めるよう十分に注意してください。全てのヘッダが安全に渡されるとは限らないですし、望ましくない挙動を引き起こすかもしれません。以下は、プロキシしてはいけない一般的なヘッダのリストです。
- host
- accept
- content-length
- content-md5
- content-type
- x-forwarded-host
- x-forwarded-port
- x-forwarded-proto
- cf-connecting-ip
- cf-ray
:::

### Example: Pass Cookies From Server-side API Calls on SSR Response
もし、内部リクエストからクライアントに戻って、クッキーを渡したりプロキシしたりしたい場合は自分で処理する必要があります。
```ts:composables/fetch.ts
export const fetchWithCookie = async (event: H3Event, url: string) => {
  const res = await $fetch.raw(url)
  const cookies = (res.headers.get('set-cookie') || '').split(',')
  for (const cookie of cookies) {
    appendHeader(event, 'set-cookie', cookie)
  }
  return res._data
}
```
```Vue
<script setup lang="ts">
// このコンポーザブルは自動的にクライアントに cookie を渡します
const event = useRequestEvent()
const result = await fetchWithCookie(event, '/api/with-cookie')
onMounted(() => console.log(document.cookie))
</script>
```

## Best Practice
これらのコンポーザブルによって返されるデータはページのペイロード内に保存されます。つまり、返されたキーのうち、コンポーネントで使用していないものは全てペイロードに追加されることになります。
:::message
コンポーネントで使用するキーだけを選ぶことを強く推奨します。
:::

`api/mountains/everest` が次のようなオブジェクトを返すと想像してください。
```json
{
  "title": "Mount Everest",
  "description": "Mount Everest is Earth's highest mountain above sea level, located in the Mahalangur Himal sub-range of the Himalayas. The China–Nepal border runs across its summit point",
  "height": "8,848 m",
  "countries": [
    "China",
    "Nepal"
  ],
  "continent": "Asia",
  "image": "https://upload.wikimedia.org/wikipedia/commons/thumb/f/f6/Everest_kalapatthar.jpg/600px-Everest_kalapatthar.jpg"
}
```
もし、コンポーネントで `title` と `description` だけを使用するなら、`$fetch` か `pick` の結果を連鎖させてキーを選択することができます。
```Vue
<script setup>
const { data: mountain } = await useFetch('/api/mountains/everest', { pick: ['title', 'description'] })
</script>
<template>
  <h1>{{ mountain.title }}</h1>
  <p>{{ mountain.description }}</p>
</template>
```

## Using Async Setup
`async setup()` を使用している場合、最初の `await` の後に現在のコンポーネントインスタンスは失われます（これは Vue3 の制限です）。`useFetch` を複数回呼び出すなど、複数の非同期処理を使いたい場合は、`<script setup>`　を使うかセットアップの最後にまとめて `await` する必要があります。

:::message
トップレベルの `await` を使う制約がなくなるので、`<setup script>` を使うことを推奨します。詳細は[こちら](https://vuejs.org/api/sfc-script-setup.html#top-level-await)
:::
```Vue
<script>
export default defineComponent({
  async setup() {
    const [{ data: organization }, { data: repos }] = await Promise.all([
      useFetch(`https://api.github.com/orgs/nuxt`),
      useFetch(`https://api.github.com/orgs/nuxt/repos`)
    ])
    return {
      organization,
      repos
    }
  }
})
</script>
<template>
  <header>
    <h1>{{ organization.login }}</h1>
    <p>{{ organization.description }}</p>
  </header>
</template>
```

## Direct Calling an API Endpoint
API を直接呼び出す必要がある場合もあります。Nuxt3 では Fetch API と同じ API でunjs/ofetch を使って（`fetch` に加え）グローバルに使用できる `$fetch` メソッドを提供します。 

`$fetch` を使用すると以下のようなメリットがあります。

サーバ上で動作している場合は、直接 API を呼びだし、クライアントサイドで動作している場合はクライアントサイドから API を呼び出す右ことを「スマート」に処理します（サードパーティの API を呼び出すことも可能です）。

加えて、レスポンスの自動解析やデータの文字列化などの便利な機能も含まれています。