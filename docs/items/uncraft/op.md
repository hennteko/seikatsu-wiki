<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Uncraft2 ― OP・運営ガイド { .page-op #uncraft-op }

Uncraft2 の導入・コマンド・権限・配布アイテム「アンクラフトステーション」の運用・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Uncraft2 |
| バージョン | 1.1.2 |
| main クラス | `com.yourname.uncraft2.Uncraft2Plugin` |
| api-version | 26.1.2 |
| 作者 | YourName |
| 説明 | アイテムを素材に戻すアンクラフトプラグイン |
| 提供コマンド | `/uncraft`、`/giveuncraft` |
| 依存プラグイン | なし |
| 設定ファイル | **なし**（`config.yml` を持たない） |

!!! note "config.yml は存在しません"
    Uncraft2 は `plugin.yml` 以外の設定ファイルを持ちません。挙動の調整は **権限ノードの付与 / 剥奪** と、必要に応じたコマンドの公開／非公開で行います。

## 導入手順

1. ビルド済みの `Uncraft2-1.1.2.jar`（成果物）をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動するとログに以下が出力される。

    ```
    Uncraft2が有効になりました。
    アンクラフトステーション専用アイテム機能が有効になりました。
    ```

3. 既定で `uncraft.use` は **OPのみ**、`uncraft.give` も **OPのみ**。一般プレイヤーには `/giveuncraft` で配布した **アンクラフトステーション（作業台アイテム）** を右クリックさせる運用が標準（右クリック側は `uncraft.use` を要求しない）。
4. 運営で「アンクラフトステーション」を配布したい場合は `/giveuncraft` を使用する（後述）。

!!! warning "対応するレシピ種別"
    Uncraft2 が対象にするのは **Shaped レシピ / Shapeless レシピ** のみです。かまど（Furnace / Smoker / Blast Furnace）・焚き火・醸造・鍛冶台・石切台のレシピは対象外です。さらに `結果が AIR / 数量 0 / 素材が空` のレシピは内部で除外されます。

## コマンド一覧

### `/uncraft`

| 項目 | 内容 |
|---|---|
| 用途 | アンクラフトGUIを開く |
| 実行可能 | プレイヤーのみ（コンソール不可） |
| permission | `uncraft.use`（default: `op`） |
| permission-message | `§cこのコマンドを使用する権限がありません。` |

GUIは1段9スロットの `Inventory`（タイトル：白＋太字「アンクラフトステーション」）で、`UncraftGuiHolder` という独自 InventoryHolder で識別されます。

### `/giveuncraft [プレイヤー] [数量]`

| 項目 | 内容 |
|---|---|
| 用途 | 「アンクラフトステーション」専用アイテムを配布 |
| permission | `uncraft.give`（default: `op`） |
| permission-message | `§cこのコマンドを使用する権限がありません。` |
| 引数 | 0個：自分に1個 / 1個：指定プレイヤーに1個 / 2個：指定プレイヤーに指定数量 |
| 数量範囲 | `1`〜`64`（範囲外は拒否） |
| タブ補完 | 第1引数：オンラインプレイヤー名 / 第2引数：`1`,`8`,`16`,`32`,`64` のヒント |

!!! note "実装上の権限チェック"
    `/giveuncraft` のコマンドハンドラ内では `sender.hasPermission("uncraft.give")` 単体で再チェックされ、満たさない場合は赤字でエラーメッセージを返します（OPは plugin.yml の `default: op` で自動的に権限を持つため通過します）。権限プラグインで明示的に否定（`-uncraft.give`）するか、OP権限を外してください。

## アンクラフトステーション専用アイテム

`/giveuncraft` で配布されるアイテムの仕様は次のとおりです。

| 項目 | 値 |
|---|---|
| ベースアイテム | `CRAFTING_TABLE`（作業台） |
| 表示名 | `アンクラフトステーション`（GOLD ＋ BOLD ＋ UNDERLINED） |
| lore | `右クリックでアンクラフト画面を開く` ／ `アイテムを素材に戻すことができます` ／ `※ 何度でも使用可能` |
| 識別タグ | PersistentDataContainer に `<plugin>:uncraft_station` キーで `BYTE` 値 `1` を保存 |
| 旧版との互換 | NamespacedKey 名が同一のため、過去に配布したアイテムも引き続き認識 |

### 配布手順

```text title="自分に1個"
/giveuncraft
```

```text title="指定プレイヤーに1個"
/giveuncraft Steve
```

```text title="指定プレイヤーに16個"
/giveuncraft Steve 16
```

成功時、受け取り側に次が送られます。

```
[アンクラフト] アンクラフトステーション x○ を受け取りました！
```

第三者付与の場合は実行者側にも「`<対象> に アンクラフトステーション x○ を付与しました。`」が表示されます。受け取り側ではアイテム取得音（`ENTITY_ITEM_PICKUP`）が再生されます。

### 右クリック時の挙動

- 右クリック（空中・ブロックいずれも）でアイテムを判定し、PDCタグが一致した場合のみ反応。
- `event.setCancelled(true)` でブロック設置を抑止 → **設置不可、アイテムのまま使用** する運用です。
- GUI を開き、`BLOCK_CHEST_OPEN` 音が鳴り、チャットに `アンクラフトステーション を開きました` が送られます。
- 開く時点で `RecipeStateManager` 内のプレイヤー状態が一度クリアされます。

### 「設置型ステーション」を作りたい場合

現バージョンの Uncraft2 には **ブロックを設置してそれを右クリックでGUI起動** という機能はありません。アイテム配布型のみです。設置物として運営したい場合は、`/giveuncraft` で配布したアイテムを **アイテムフレームに入れて壁掛けする** などの運用で代用してください（プレイヤー側はインベントリに持ったアイテムでないと右クリックできない点に注意）。

## 権限ノード

| 権限ノード | 既定値 | 説明 |
|---|---|---|
| `uncraft.use` | `op` | `/uncraft` でGUIを開ける（プレイヤーは専用アイテム右クリックで代替） |
| `uncraft.give` | `op` | `/giveuncraft` でアンクラフトステーションを配布できる |
| `uncraft.*` | `op` | 上記すべて（children: `uncraft.use`, `uncraft.give`） |

!!! example "LuckPerms 設定例"
    ```text title="全員に基本許可（既定値と同じだが明示する場合）"
    /lp group default permission set uncraft.use true
    ```

    ```text title="モデレーターに配布権限"
    /lp group moderator permission set uncraft.give true
    ```

    ```text title="管理者に全権限"
    /lp group admin permission set uncraft.* true
    ```

    ```text title="特定グループから明示的に剥奪"
    /lp group guest permission set uncraft.use false
    ```

## 管理コマンドまとめ

| コマンド | 必要権限 | 用途 |
|---|---|---|
| `/uncraft` | `uncraft.use` | アンクラフトGUIを開く |
| `/giveuncraft` | `uncraft.give`（または OP） | アンクラフトステーションを自分／他人に配布 |

**リロードコマンドは存在しません。** 設定ファイルを持たないため、運用上の再読み込みは不要です。プラグイン本体を差し替える場合のみ、サーバーの再起動 / `plugman` 等での再ロードで対応してください。

## トラブルシューティング

??? failure "コンソールから `/uncraft` を叩いてもGUIが開かない"
    仕様です。プレイヤー専用コマンドのため、コンソール実行時は `このコマンドはプレイヤーのみ実行できます。` と返します。

??? failure "右クリックしてもアンクラフトステーションのGUIが開かない"
    `UncraftItemListener` は PDCタグ `<plugin>:uncraft_station` で判定します。次を確認してください。
    - 手に持っているアイテムが **`/giveuncraft` で配布したもの** か（クリエイティブでコピーした通常の作業台では反応しません）。
    - 別プラグインが `PlayerInteractEvent` を **HIGHEST 以降でキャンセル** していないか。
    - アイテムが手の **メインハンド** にあるか（オフハンド固有の制御はしていませんが、右クリック対象がオフハンドの場合はバニラ仕様で挙動が変わります）。

??? failure "/giveuncraft の数量を 100 にしたら拒否された"
    仕様です。`amount` は `1` 以上 `64` 以下に制限されています。それを超える数を渡したい場合は複数回に分けて実行してください。

??? failure "GUI内のアイテムが消えた／返ってこない"
    GUI を閉じた時点で投入スロット（スロット 0）のアイテムは自動でプレイヤーに返却され、入りきらない場合は **足元にドロップ** されます（`UncraftCalculator#returnRemainingItems`）。装飾スロット（1, 3, 6, 7, 8）、結果スロット（2）、矢印スロット（4, 5）は対象外です。

??? failure "「レシピが見つかりません」が頻発する"
    対象は Shaped / Shapeless レシピのみです。かまど・醸造・鍛冶台・石切台で作るアイテムは仕様上対象外です。別プラグインがレシピを上書き／削除している可能性もあるため、`/recipe` 系の管理プラグインや独自レシピプラグインとの競合を確認してください。

??? failure "返ってくる素材が想定と違う"
    レシピの素材スロットが **MaterialChoice / ExactChoice** の場合、Uncraft2 は **最初の候補** を返却します。狙いの素材で返したい場合は、矢印ボタンで該当する別レシピに切り替えてください（同じ結果アイテムでも、たとえば板材種別ごとに別レシピとして登録されているケースがあります）。

??? failure "分解時に正しい個数が消費されない"
    分解はレシピ結果数の倍数単位で消費されます（`itemsAvailable / recipeResultAmount * recipeResultAmount`）。例えば階段（4個結果）を 7 個入れると 4 個だけ消費され、3 個は残ります。バグではなく仕様です。

## 実装メモ（運用上の留意点）

- Paper 26.1.2 では `event.getView().getTitle()` ベースの GUI 識別が将来削除予定／Component 化により破綻するため、Uncraft2 は **`UncraftGuiHolder` の `instanceof` 判定** でGUIを識別しています。他プラグインが同名タイトルのインベントリを開いても誤検知しません。
- `RecipeStateManager` は `ConcurrentHashMap` でプレイヤーごとのレシピ候補リストと選択インデックスを保持します。インベントリクローズ時に自動でエントリ削除されますが、プラグインリロード等で `onDisable` が走った場合の永続化はありません（再ログイン／GUI再オープンで再構築されます）。
- アンクラフトステーション用 NamespacedKey は `uncraft_station` 固定。旧バージョンで配布したアイテムも識別可能です。
- 成功時の効果音：`BLOCK_ANVIL_USE`（0.3, 1.5）＋ `ENTITY_EXPERIENCE_ORB_PICKUP`（0.5, 1.2）。失敗時：`ENTITY_VILLAGER_NO`（0.5, 1.0）。配布時：`ENTITY_ITEM_PICKUP`（0.5, 1.2）。GUI起動時：`BLOCK_CHEST_OPEN`（0.5, 1.2）。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Uncraft2 概要へ](index.md){ .md-button }
