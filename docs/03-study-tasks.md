# 03. Study Tasks

このファイルは、読むだけで終わらせず、実際に手を動かして Nuxt を覚えるための課題集です。

## 進め方

- 1 つずつ小さく変更する
- 変更後は `npm run dev` で挙動を見る
- 「どのファイルを触ると何が変わるか」を意識する

## 課題 1: ヘッダー文言を変える

対象:

- [layouts/default.vue](/c:/apps/pokedex/layouts/default.vue)

やること:

- `PokeDex` の横に短いサブタイトルを追加する

学べること:

- layout を変えると全ページに影響する

## 課題 2: 一覧カードに情報を追加する

対象:

- [pages/index.vue](/c:/apps/pokedex/pages/index.vue)

やること:

- カードにタイプ数や簡単なラベルを追加する

学べること:

- `v-for` とテンプレート描画
- ページの表示ロジック変更

## 課題 3: 詳細ページにメタ説明を追加する

対象:

- [pages/pokemons/[name]/index.vue](/c:/apps/pokedex/pages/pokemons/[name]/index.vue)

やること:

- `useSeoMeta()` に `description` や `ogDescription` を追加する

学べること:

- ページ単位の SEO 設定

## 課題 4: 一覧の取得件数を変える

対象:

- [composables/pokemons/index.ts](/c:/apps/pokedex/composables/pokemons/index.ts)

やること:

- 初回取得件数 `40` を `20` に変更する
- 画面表示と無限スクロールの挙動差分を見る

学べること:

- `useFetch` のクエリ
- composable がページ表示に与える影響

## 課題 5: API で受け取るクエリを追う

対象:

- [composables/pokemons/index.ts](/c:/apps/pokedex/composables/pokemons/index.ts)
- [server/api/v1/pokemons/index.get.ts](/c:/apps/pokedex/server/api/v1/pokemons/index.get.ts)
- [server/domains/models/pokemons/index.ts](/c:/apps/pokedex/server/domains/models/pokemons/index.ts)

やること:

- `offset` と `limit` がどこから来て、どこで検証され、どこで使われるかを追う

学べること:

- クライアントからサーバーまでのデータの流れ
- Zod バリデーション

## 課題 6: 新しいページを追加する

対象:

- `pages/about.vue` を新規作成

やること:

- この PokeDex の説明ページを追加する
- ヘッダーからそのページへ遷移できるようにする

学べること:

- ファイルベースルーティング
- `NuxtLink`

## 課題 7: composable を 1 つ追加する

候補:

- 「お気に入り表示切り替え」
- 「表示件数の切り替え」

やること:

- `composables/` に新しい `useXxx()` を作る
- `pages/index.vue` か `layouts/default.vue` から利用する

学べること:

- 状態の再利用
- ロジックの分離

## 課題 8: server/api を 1 本追加する

候補:

- `/api/v1/health`
- `/api/v1/version`

やること:

- `server/api/v1/health/index.get.ts` を作り、固定 JSON を返す

学べること:

- Nuxt の API 追加方法
- フロントとサーバーが同居する感覚

## 次に読むときの観点

1. そのファイルは UI なのか、状態管理なのか、API なのか
2. そのデータはどこで作られ、どこで消費されるか
3. Nuxt の機能なのか、Vue の機能なのか、ブラウザ API なのか
4. この責務は別ファイルへ分けたほうが良いか
