# 01. Nuxt Overview

## Nuxt とは

Nuxt は Vue をベースにしたフルスタックフレームワークです。  
このプロジェクトでは、画面描画だけでなく、`server/api` で API も同じリポジトリ内に実装しています。

このリポジトリを見ると、Nuxt は大きく次の役割を持っています。

- 画面をルーティングする
- レイアウトを共通化する
- サーバー API を提供する
- クライアントとサーバーのデータ取得をつなぐ
- 設定やメタ情報を管理する

## このプロジェクトの Nuxt の全体像

```text
app.vue
  -> layouts/default.vue
    -> pages/index.vue
    -> pages/pokemons/[name]/index.vue

pages/*
  -> composables/pokemons/*
    -> /api/v1/pokemons
    -> /api/v1/pokemons/[name]

server/api/*
  -> server/domains/*
  -> server/infrastructures/pokeapi/*
    -> https://pokeapi.co/api/v2/
```

## 重要ディレクトリ

### `app.vue`

アプリ全体の入口です。今は非常にシンプルで、`NuxtLayout` の中に `NuxtPage` を置いています。

```vue
<NuxtLayout>
  <NuxtPage />
</NuxtLayout>
```

理解ポイント:

- `NuxtLayout` は共通レイアウトを適用する
- `NuxtPage` は現在のルートに対応するページを描画する

### `pages/`

ファイルベースルーティングです。ファイル名と URL が対応します。

- `pages/index.vue` -> `/`
- `pages/pokemons/[name]/index.vue` -> `/pokemons/:name`

理解ポイント:

- Vue Router を手書きしなくてもルートが作られる
- `[name]` のような名前付き動的セグメントを使える

### `layouts/`

共通レイアウトを置く場所です。  
このプロジェクトの [default.vue](/c:/apps/pokedex/layouts/default.vue) ではヘッダー、フッター、メイン領域を共通化しています。

理解ポイント:

- ページごとの差分は `<slot />` に入る
- 共通 UI をページごとに重複実装しなくてよい

### `composables/`

再利用可能な状態管理やロジックを置きます。

このプロジェクトでは主に次を扱っています。

- ポケモン一覧取得
- ポケモン詳細取得
- shiny 表示状態の共有

理解ポイント:

- `useXxx` という名前で切り出す
- データ取得と画面描画を分離しやすい

### `server/api/`

Nuxt のサーバー API を置く場所です。  
フロントエンドからは `/api/...` としてアクセスできます。

- `server/api/v1/pokemons/index.get.ts`
- `server/api/v1/pokemons/[name]/index.get.ts`

理解ポイント:

- フロントと同じ repo に API を書ける
- 外部 API への依存をここで吸収できる
- クライアントから直接外部 API を叩かずに済む

### `nuxt.config.ts`

Nuxt 全体の設定です。  
このプロジェクトでは次が特に重要です。

- `modules`
- `css`
- `runtimeConfig`
- `experimental.viewTransition`
- `app.head`

理解ポイント:

- モジュール追加はここで行う
- 環境依存の値は `runtimeConfig` に寄せる
- 共通メタ情報もここで管理できる

## このプロジェクトの `nuxt.config.ts` で見るべき点

### `modules`

```ts
modules: ['@nuxt/eslint', '@nuxt/icon', '@nuxthub/core']
```

- `@nuxt/icon`: `<Icon>` コンポーネントを使える
- `@nuxt/eslint`: ESLint 連携
- `@nuxthub/core`: NuxtHub 関連機能

### `runtimeConfig`

```ts
runtimeConfig: {
  pokeapi: {
    baseURL: 'https://pokeapi.co/api/v2/',
  },
}
```

外部 API の URL をハードコードせず、設定として切り出しています。

### `app.head`

アプリ全体のタイトルや meta 情報を設定しています。  
個別ページでは `useSeoMeta` で上書きや追加もできます。

## Nuxt 学習の見方

このリポジトリを読むときは、単にファイルを上から追うより、次の順で追うと理解しやすいです。

1. URL に対してどの `pages` が開くか見る
2. そのページがどの `composable` を呼ぶか見る
3. `useFetch` がどの `server/api` を叩くか見る
4. `server/api` がどのドメイン層、インフラ層を呼ぶか見る
5. 最後に `nuxt.config.ts` で共通設定を見る
