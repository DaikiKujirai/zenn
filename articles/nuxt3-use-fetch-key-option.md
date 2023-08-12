---
title: "Nuxt3 useFetch 2回目以降のリクエストで前回と同じレスポンスが返る際の対処法"
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

## 序章

`useFetch` を使用して API を呼び出した際、2 回目以降のリクエストで先のレスポンスが返される問題が発生しました。本稿では、その際の対処方法について述べます。

## 原因の解明

[Nuxt3の公式](https://nuxt.com/docs/getting-started/data-fetching#caching-and-refetching) によれば、`useFetch` は URL または key オプションなどのパラメータをキーとし、同一データの再取得を防ぎます。

> useFetch and useAsyncData use keys to prevent refetching the same data.
> - useFetch uses the provided URL as a key. Alternatively, a key value can be provided in the options object passed as a last argument.

## 解決方法

key オプションにランダムな文字列を指定することで、2 回目以降のリクエストで先のレスポンスが返される問題を回避しました。

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

## まとめ

本稿では、Nuxt3 の `useFetch` を使用して 2 回目以降のリクエストで先のレスポンスが返される問題と、その解決方法について説明しました。ランダムな文字列を key オプションに設定することで、この問題を効果的に回避することが可能です。

## 参考
- [Nuxt3 公式](https://nuxt.com/)
