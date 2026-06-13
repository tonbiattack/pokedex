# 02. PokeDex Walkthrough

このドキュメントでは、このプロジェクトで Nuxt がどう使われているかを、実際のファイルに沿って追います。

## 1. アプリの入口

[app.vue](/c:/apps/pokedex/app.vue) は非常に小さく、役割を Nuxt に委ねています。

```vue
<NuxtLayout>
  <NuxtPage />
</NuxtLayout>
```

ここで理解したいのは、`app.vue` 自体に画面を詰め込まないことです。  
Nuxt では、アプリの入口は薄く保ち、ページやレイアウトへ責務を分ける構成が自然です。

## 2. 共通レイアウト

[layouts/default.vue](/c:/apps/pokedex/layouts/default.vue) では次を担当しています。

- ヘッダー表示
- フッター表示
- shiny モード切り替え UI
- ページ本文の受け皿

ここでの学習ポイント:

- `useShinyMode()` を layout で呼ぶと、複数ページから同じ状態を使える
- `<slot />` に各ページの内容が差し込まれる

## 3. 一覧ページ

[pages/index.vue](/c:/apps/pokedex/pages/index.vue) は `/` に対応します。

主な責務は次のとおりです。

- 一覧データの初回取得
- 無限スクロールでの追加取得
- 一覧の描画
- 詳細ページへのリンク

### `usePokemons()` の利用

```ts
const { pokemons, hasMore, fetchNextPokemons, isLoading } = await usePokemons()
```

`await usePokemons()` としているため、ページ初期表示に必要なデータ取得を `composable` 側へ寄せています。

### `IntersectionObserver`

画面下部の `sentinel` を監視して、表示されたら次のポケモンを取得しています。  
Nuxt 固有というよりブラウザ API ですが、ページロジックと組み合わせる実例として良い題材です。

### `NuxtLink`

```vue
<NuxtLink :to="`/pokemons/${pokemon.name}`">
```

Nuxt のページ遷移では通常 `NuxtLink` を使います。  
通常の `<a>` と違い、アプリ内遷移として扱われます。

## 4. 詳細ページ

[pages/pokemons/[name]/index.vue](/c:/apps/pokedex/pages/pokemons/[name]/index.vue) は動的ルートです。

### ルートパラメータ

```ts
const route = useRoute('pokemon-name')
const { pokemon, pokemonImage } = await usePokemon(route.params.name as string)
```

ここで `route.params.name` を使って、URL からポケモン名を取り出しています。

### ページごとの SEO

```ts
useSeoMeta({
  title: `PokeDex | ${formatName(pokemon.value.name)}`,
  ogTitle: `PokeDex | ${formatName(pokemon.value.name)}`,
  ogImage: pokemon.value.image.still.default,
})
```

Nuxt ではページ単位で SEO メタ情報を設定できます。  
一覧ページでは共通設定、詳細ページでは個別設定、という分け方が分かりやすいです。

## 5. `composables` によるロジック分離

[composables/pokemons/index.ts](/c:/apps/pokedex/composables/pokemons/index.ts) は、画面から API 呼び出しの細部を隠しています。

### `usePokemon()`

- 1 件のポケモン詳細を取得する
- shiny 状態に応じて表示画像を切り替える

### `usePokemons()`

- 一覧を取得する
- `hasMore` を計算する
- `fetchNextPokemons()` で続きを取得する

学習ポイント:

- ページから API 実装の詳細を隠せる
- `computed` や `ref` をまとめて扱える
- UI とデータ取得の責務を分離できる

## 6. `useFetch` と `/api` の接続

`composables` では次のように Nuxt の `useFetch` を使っています。

```ts
const { data } = await useFetch('/api/v1/pokemons')
```

ここで重要なのは、クライアントが直接 `https://pokeapi.co/...` を叩いていないことです。  
いったん自分の `server/api` に向けてアクセスし、サーバー側で外部 API を扱っています。

この構成の利点:

- API の責務を分離しやすい
- バリデーションを入れやすい
- キャッシュを入れやすい
- 外部 API 変更の影響を局所化しやすい

## 7. `server/api` の役割

[server/api/v1/pokemons/index.get.ts](/c:/apps/pokedex/server/api/v1/pokemons/index.get.ts) では一覧 API を提供しています。  
[server/api/v1/pokemons/[name]/index.get.ts](/c:/apps/pokedex/server/api/v1/pokemons/[name]/index.get.ts) では詳細 API を提供しています。

どちらも大まかには次の流れです。

1. パラメータやクエリを検証する
2. repository を呼ぶ
3. 結果を返す
4. `defineCachedEventHandler` でキャッシュする

### `defineCachedEventHandler`

このプロジェクトでは API レスポンスを 1 日キャッシュしています。

```ts
maxAge: 24 * 60 * 60
```

学習ポイント:

- Nuxt のサーバー API にキャッシュ戦略を持たせられる
- 毎回外部 API を叩かなくて済む

## 8. ドメイン層とインフラ層

このプロジェクトは `server/domains` と `server/infrastructures` を分けています。

### `server/infrastructures/pokeapi/*`

- 外部 API との通信を担当する

### `server/domains/models/*`

- Zod を使って型や変換ルールを定義する

### `server/domains/repositories/*`

- ユースケースに近い取得処理をまとめる

Nuxt 学習だけに絞るならここは少し進んだ話ですが、実務では重要です。  
「Nuxt の機能」と「業務ロジックの整理」を混ぜすぎない構成として参考になります。

## 9. このプロジェクトで押さえるべき Nuxt の強み

- Vue 単体よりもルーティング開始が速い
- `pages` と `layouts` で構成が自然に分かれる
- `useFetch` と `server/api` の相性が良い
- 画面と API を同じ TypeScript プロジェクトで管理できる
- SEO や head 管理が組み込みで扱いやすい

## 10. 最低限の理解チェック

次の問いに答えられれば、かなり追えています。

1. `/pokemons/pikachu` はどのファイルが表示するか
2. 一覧ページの初回データ取得はどこで行うか
3. 外部の PokeAPI を直接呼ばず、どこを経由しているか
4. shiny 状態はどこで共有しているか
5. 詳細ページのタイトルはどこで設定しているか
