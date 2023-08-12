---
title: "Nuxt3 useFetch 2回目以降のリクエストで前回と同じレスポンスが返ってきてしまう時の対処法"
emoji: "🐳"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['Nuxt.js']
published: false
---

## 環境
- Nuxt.js 3.6.2

### SSR設定
このプロジェクトでは、サーバーサイドレンダリング (SSR) を無効にしています。
具体的には以下の設定がしてあります。

```typescript:nuxt.config.ts
export default defineNuxtConfig({
  ssr: false,
})
```

## はじめに
`useFetch` を使用して API を叩いたところ、2 回目以降のリクエストでは実際に API が叩かれず、前回のレスポンスが返ってきてしまうことがありました。
その時の対処法をメモしておきます。

## 原因
[公式](https://nuxt.com/docs/getting-started/data-fetching#caching-and-refetching) によると、`useFetch` は URL または key オプションをキーとして同じデータの再取得を防いでいるようです。

> useFetch and useAsyncData use keys to prevent refetching the same data.
> - useFetch uses the provided URL as a key. Alternatively, a key value can be provided in the options object passed as a last argument.

## 解決方法

key オプションに、ランダムな文字列を入れることで、2 回目以降のリクエストで前回と同じレスポンスが返ってくることを防ぎました。
これで、全く同時に同じエンドポイントの API を叩くことがなければ、同じレスポンスが返ってくることはなくなります。

```typescript
const fetchExample = async () => {
  const { data, error } = await useFetch('/api/example', {
    method: 'GET',
    key: crypto.randomUUID(),
    baseURL: 'http://localhost:3000',
  })
  
  if (data.value) {
    console.log(data.value)
  }

  if (error.value) {
    console.error(error.value)
  }
}
```