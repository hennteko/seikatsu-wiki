<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# GlobalStorage2 ― OP・運営ガイド { .page-op #globalstorage-op }

GlobalStorage2 の導入・`config.yml`・自動収納チェスト・権限・管理コマンド・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | GlobalStorage2 |
| api-version | 26.1.2 |
| メインコマンド | `/gs`（エイリアス `/globalstorage`） |
| 依存プラグイン | なし（Paper 推奨） |
| 設定ファイル | `plugins/GlobalStorage2/config.yml` |
| データファイル | `storage.json`（倉庫の中身）、`auto_chests.json`（自動収納チェスト）、`tensou_filters.json`（自動転送フィルター） |
| 保存形式 | JSON（ItemStack は Base64 シリアライズ） |
| 自動保存 | 5 分ごと（6000 ticks）＋ シャットダウン時 |

!!! success "実装済み機能"
    共有倉庫（無限スタック・JSON 永続化）、6 行 GUI による取り出し／収納／カテゴリ分類／ページ送り、GS スティック、サーバー全体での共有、**自動収納チェスト**（指定したチェストを閉じた瞬間に中身を倉庫へ移送）、**自動転送フィルター**（プレイヤーごとに、拾った素材を倉庫へ自動転送）、NG ワールド指定、Geyser（統合版）クライアントへの収納操作互換。

## 導入手順

1. ビルドした `GlobalStorage2-<version>.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/GlobalStorage2/` に `config.yml` が自動生成される。
3. 必要に応じて `config.yml` の `ng-worlds`（GS スティックを禁止するワールド）を編集する。
4. プレイヤーに `/gs give` で GS スティックを配布する（または各自で `/gs give` を実行させる）。

!!! tip "サーバー実装について"
    Paper API（`AsyncChatEvent`、`InventoryView#title()` の Component 版など）を利用しており、**Paper 26.1.2 以上** を前提に書かれています。Spigot では一部 API が使用できないため Paper の利用を推奨します。

## config.yml 設定項目

```yaml
# GlobalStorage2 Configuration

# 棒を使用できないワールドのリスト
ng-worlds:
  - "example_world_disable"
```

| キー | 既定値 | 説明 |
|---|---|---|
| `ng-worlds` | `["example_world_disable"]` | GS スティックでの倉庫オープンを禁止するワールド名のリスト。`/gs ngworld` / `/gs okworld` でも編集可能 |

!!! note "ng-worlds の効果範囲"
    `ng-worlds` は **GS スティックの右クリック** にのみ作用します。`/gs open`（OP のみ）はワールドに関係なく実行できます。また、自動収納チェスト・自動転送フィルターはワールド制限の対象外です。

## データファイル

`plugins/GlobalStorage2/` 配下に以下の JSON ファイルが自動生成されます。**手動編集は非推奨** です（Base64 シリアライズされたデータを含むため）。

| ファイル | 内容 |
|---|---|
| `storage.json` | 倉庫の中身。`[{ base64: <ItemStack の Base64>, amount: <長整数の数量> }, ...]` の配列 |
| `auto_chests.json` | 自動収納チェストの座標。`Set<{ world, x, y, z }>` |
| `tensou_filters.json` | プレイヤーごとの自動転送フィルター。`{ <UUID>: [<Material 名>, ...] }` |

!!! warning "データのバックアップ"
    `storage.json` にはサーバーの蓄積アイテムがすべて入っており、破損するとプレイヤーの預け入れアイテムが失われます。定期的に `plugins/GlobalStorage2/` フォルダごとバックアップしてください。

## 自動収納チェストの設置手順

特定のチェストを **自動収納チェスト** として登録できます。登録後にそのチェストを閉じると、中身が **GS スティックでなければ自動的に共有倉庫へ移送** されます（チェスト内の元のアイテムは消去）。

1. OP（`gs.admin` 権限）が対象ワールドに入る。
2. `/gs setchest` を実行する。
3. メッセージが表示されたら、自動収納にしたい **設置済みチェスト** を右クリックする。
4. 「自動収納チェストとして登録しました！」と表示されたら登録完了。

解除は次の手順で行います。

1. `/gs removechest` を実行する。
2. 解除したいチェストを右クリックする。
3. 「自動収納チェストの登録を解除しました。」が表示されれば解除完了。

!!! tip "登録・解除モードのキャンセル"
    `/gs setchest` / `/gs removechest` のあと、もう一度同じコマンドを実行すると **チェストを右クリックする前にキャンセル** できます。

!!! warning "チェスト破壊時の動作"
    自動収納チェストとして登録されたチェストが破壊されると、**登録は自動的に解除** されます（チェストを破壊したプレイヤーにチャットで通知されます）。再度使う場合は新しく `/gs setchest` で登録してください。

!!! note "Geyser（統合版）プレイヤーに対する挙動"
    自動転送・自動収納・倉庫 GUI 操作の各処理は、統合版プレイヤーでも動作するよう実装されています（カーソル経由の収納操作・Shift クリック互換など）。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `gs.use` | 全員（`true`） | GS スティックでの倉庫オープン、`/gs give`、`/gs tensou` |
| `gs.admin` | OP | `/gs open`、`/gs save`、`/gs ngworld`、`/gs okworld`、`/gs setchest`、`/gs removechest`、`/gs listchests` |

!!! note "plugin.yml の記述との差分"
    `plugin.yml` の `gs.admin` の説明には「`open, save, ngworld`」とだけ記載されていますが、実装上は **`setchest` / `removechest` / `listchests` / `okworld`** も `gs.admin` を要求します。一方で `sort` は権限チェックが行われず、誰でも実行できます（GUI からのソートと同等）。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/gs give [プレイヤー名]` | `gs.use` | GS スティックを配布（省略時は自分） |
| `/gs open` | `gs.admin` | 倉庫 GUI を強制的に開く（NG ワールド判定をバイパス） |
| `/gs sort` | なし | 倉庫アイテムをアルファベット順にソート（開いている全プレイヤーの GUI を再描画） |
| `/gs save` | `gs.admin` | 倉庫・自動収納チェスト・自動転送フィルターを手動で **非同期保存** |
| `/gs ngworld <ワールド名>` | `gs.admin` | 指定ワールドを NG リストに追加（GS スティックを禁止） |
| `/gs okworld <ワールド名>` | `gs.admin` | 指定ワールドを NG リストから削除 |
| `/gs setchest` | `gs.admin` | 次に右クリックしたチェストを自動収納チェストとして登録 |
| `/gs removechest` | `gs.admin` | 次に右クリックしたチェストの自動収納登録を解除 |
| `/gs listchests` | `gs.admin` | 登録されている自動収納チェスト一覧をチャット表示 |
| `/gs tensou` | `gs.use` | 自動転送フィルター設定 GUI を開く（プレイヤー用） |

!!! note "タブ補完"
    `/gs give` のあとはオンラインプレイヤー名、`/gs ngworld` / `/gs okworld` のあとはサーバーのワールド名が補完されます。

## アイテムカテゴリ判定について

GUI 最下行のカテゴリボタンは、`Material` 名と `material.isEdible()` などのキーワード判定で自動分類しています（実装上のハードコード）。`config.yml` でカテゴリ割り当てを変更することはできません。

| カテゴリ | 主な判定キーワード（一部） |
|---|---|
| 建築ブロック | `PLANKS`, `BRICKS`, `STONE_BRICK`, `POLISHED`, `CUT`, `CHISELED`, `SMOOTH` ほか |
| 色付きブロック | `WOOL`, `CARPET`, `TERRACOTTA`, `CONCRETE`, `STAINED_GLASS`, `CANDLE`, `BANNER`, `BED`, `SHULKER_BOX` |
| 天然ブロック | `STONE`, `DIRT`, `GRASS`, `SAND`, `LOG`, `LEAVES`, `_ORE`, `DEEPSLATE` ほか |
| 機能的ブロック | `CRAFTING_TABLE`, `FURNACE`, `CHEST`, `BARREL`, `ANVIL`, `BEACON`, `LECTERN` ほか |
| レッドストーン | `REDSTONE`, `PISTON`, `HOPPER`, `OBSERVER`, `RAIL`, `TNT`, `NOTE_BLOCK` ほか |
| 道具と実用品 | `PICKAXE`, `AXE`, `SHOVEL`, `HOE`, `SHEARS`, `FISHING_ROD`, `BUCKET` ほか |
| 戦闘 | `SWORD`, `BOW`, `CROSSBOW`, `TRIDENT`, `HELMET`, `CHESTPLATE`, `LEGGINGS`, `BOOTS`, `SHIELD`, `ARROW` |
| 食べ物と飲み物 | `material.isEdible()`, `POTION`, `MILK_BUCKET`, `CAKE`, 各種スープ |
| 材料 | `INGOT`, `NUGGET`, `GEM`, `DUST`, `SCRAP`, `DYE`, `STICK`, `STRING` ほか |
| その他 | 上記いずれにも該当しないもの |

## トラブルシューティング

??? failure "倉庫が空になった / アイテムが消えた"
    `plugins/GlobalStorage2/storage.json` を確認してください。`onDisable` 前にクラッシュした場合、最後の自動保存（5 分ごと）以降の更新が失われる可能性があります。バックアップから `storage.json` を復元するとロールバックできます。

??? failure "GS スティックで倉庫が開かない"
    プレイヤーの現在地のワールドが `config.yml` の `ng-worlds` に含まれている可能性があります。`/gs okworld <ワールド名>` で許可するか、別のワールドに移動して試してください。また `gs.use` 権限が外されていないかも確認してください。

??? failure "自動収納チェストが反応しない"
    `/gs listchests` で対象チェストが登録されているか確認してください。チェストを破壊した場合は登録が自動解除されるため、再登録が必要です。なお、自動収納はチェストを **閉じた瞬間** に発火するため、開いたままでは動作しません（中の `GS スティック` は対象外で残ります）。

??? failure "`/gs save` がフリーズに見える"
    保存処理は **非同期スレッド** で実行されます。チャットには即座に「データを手動バックアップしました。」と表示されますが、実際のディスク書き込みはバックグラウンドで進行します。`storage.json` が大きい場合は完了まで数秒～数十秒かかることがあります。

??? failure "自動転送フィルターが反映されない"
    `/gs tensou` を開いて、対象アイテムが緑色（ON）になっているかを確認してください。フィルターは **素材名（Material 名）** で判定するため、ピックアップ対象が同じ素材でないと発火しません。ON/OFF を切り替えた直後にチャットへ通知が出ますので、状態を確認してください。

??? failure "サーバー起動時にアイテムが読み込めなかった旨の警告が出る"
    `storage.json` の Base64 デコードに失敗したエントリは `Failed to deserialize an item: ...` の WARNING ログを出してスキップされます。原因は MC バージョンの大きな変更や、対象アイテム ID の削除などが考えられます。該当エントリは復元できないため、ログを確認のうえバックアップから復旧するか、欠損として受け入れてください。

??? failure "倉庫に細工アイテムが大量にたまる"
    GlobalStorage2 は **アイテムの種類を内容（メタデータ含む）で判定** するため、名前付き・エンチャント付きアイテムは別エントリになります。整理したい場合は `/gs sort` でアルファベット順に並べ替えると見やすくなります。

??? failure "Spigot で動かない / エラーが出る"
    `AsyncChatEvent` や `InventoryView#title()` の Component 版など、Paper API を利用しています。**Paper 26.1.2 以上** で運用してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← GlobalStorage2 概要へ](index.md){ .md-button }
