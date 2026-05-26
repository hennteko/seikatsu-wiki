<div class="audience-banner op">🛠️ OP・運営向けページ — Netherfortress はプレイヤーが初めてネザーに入ったときに最寄り要塞の座標を通知するプラグインです。</div>

# Netherfortress ― OP・運営ガイド { .page-op }

<span class="badge custom">自作プラグイン</span>
<span class="badge done">導入済み</span>

プレイヤーが**初めてネザー（The Nether）に入った瞬間**に、現在地から最寄りのネザー要塞（Nether Fortress）を検索し、その X / Z 座標をチャットで通知する自作プラグインです。イベントベースで動作し、コマンド・権限・設定ファイルは持ちません。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Netherfortress |
| バージョン | 1.0 |
| メインクラス | `netherfortress.plugin.Netherfortress` |
| api-version | `26.1.2` |
| 依存プラグイン | なし |
| 設定ファイル | なし |
| コマンド | なし（イベントベース） |
| 権限ノード | なし |
| 説明 | 最寄りのネザー要塞の座標を初めてネザーに入ったプレイヤーに通知します。 |

## 動作仕様

### トリガーイベント

- `PlayerChangedWorldEvent`（プレイヤーがワールドを移動したとき）を購読します。
- 移動先ワールドの環境が `World.Environment.NETHER` でない場合は何もしません。

### 初回ネザー入場の判定

- プレイヤーの **PersistentDataContainer（PDC）** に保存されたフラグ `netherfortress:has_entered_nether`（`BOOLEAN` 型）の有無で判定します。
- フラグが既に存在する場合（2回目以降の入場）は処理を終了します。
- 初回入場と判断した場合、ただちに同フラグに `true` を書き込みます。

### 要塞座標の取得

- 検索開始地点はネザー入場直後のプレイヤーの現在地（`player.getLocation()`）。
- `World#locateNearestStructure(Location, Structure, int, boolean)` API を使用。
    - 構造物: `org.bukkit.generator.structure.Structure.FORTRESS`
    - 検索半径（`SEARCH_RADIUS`）: **1000 ブロック**（未ロードチャンクを考慮した固定値）
    - 第4引数 `findUnexplored`: `false`
- Paper 26.1 で旧 `StructureType.NETHER_FORTRESS` が deprecated になったため、新 API（`Structure.FORTRESS` ＋ `StructureSearchResult` 経由）を使用しています。

### 通知方法

Adventure API の `Component` によるチャットメッセージを送信します（titleやactionbarではなく**チャット**）。

**要塞が見つかった場合（3行）**

1. `ネザー到達おめでとう！`（GOLD）
2. `最寄りのネザー要塞の座標: X: <x>, Z: <z>はここです。`（AQUA + WHITE）
3. `(要塞はY座標32〜80付近に生成されます)`（GRAY）

**見つからなかった場合**

- `エラー: ネザー要塞が検索範囲(1000ブロック)内で見つかりませんでした。運悪く遠いようです。`（RED）

## 導入手順

1. ビルド済み jar （`Netherfortress-1.0.jar` 相当）を `plugins/` フォルダに配置する。
2. サーバーを起動／再起動する。
3. 起動ログに `Netherfortressプラグインを有効にしました。` が表示されれば成功。
4. 設定ファイルもコマンドもないため、追加の作業は不要。

## config.yml 設定項目

**設定なし。** `config.yml` は生成されず、外部から挙動を変更する手段は提供していません。検索半径（1000ブロック）はソースコード内の定数 `SEARCH_RADIUS` でハードコードされています。

## 権限ノード

**権限ノードなし。** `plugin.yml` に `permissions:` ブロックは定義されておらず、全プレイヤーが対象です（OP/非OP問わず、初回ネザー入場時に通知が送られます）。

## データ保存

| 項目 | 内容 |
|---|---|
| 保存方式 | プレイヤーの **PersistentDataContainer（PDC）** |
| キー | `netherfortress:has_entered_nether` |
| 型 | `BOOLEAN` |
| 値 | `true`（初回入場時に書き込み、以後は更新しない） |
| 保存場所 | プレイヤーデータ（`<world>/playerdata/<UUID>.dat`）内に永続化 |

サーバー再起動後もフラグは保持されます。**フラグをリセットして再度通知を出したい場合は、対象プレイヤーの `.dat` から該当のPDCエントリを削除する必要があります**（プラグイン側にリセットコマンドはありません）。

## 注意点・トラブルシューティング

??? failure "要塞が見つからないと言われた"
    検索半径は **1000 ブロック固定**です。初回入場地点の周囲1000ブロック以内に要塞が存在しない場合、`エラー: ネザー要塞が検索範囲(1000ブロック)内で見つかりませんでした。` と表示され、その後の再通知はありません（フラグはこの時点で `true` に設定済みのため）。

??? failure "2回目以降は通知が出ない"
    仕様通りです。PDC フラグ `netherfortress:has_entered_nether` が立つと、以降のネザー入場では一切メッセージが送信されません。テスト時は別アカウント、または対象プレイヤーの `playerdata/<UUID>.dat` の編集が必要です。

??? failure "Multiverseで作った独自ネザーワールドでも発火する"
    判定はワールド名ではなく `World.Environment.NETHER`（Environment列挙）で行います。Multiverse-Core 等で `NETHER` 環境として作成された**すべてのネザー系ワールド**が対象になり、それぞれ初回入場時に通知されます（PDCフラグはワールド非依存の単一フラグのため、複数ネザーがあっても通知は最初の1回のみ）。

??? failure "ネザーポータルで入っても発火しない場合がある"
    本プラグインは `PlayerChangedWorldEvent` を購読しています。ポータル経由・`/mv tp` 経由・`/execute in` 経由いずれも、ワールドが実際に切り替わればイベントは発火します。発火しない場合は、移動先の `getEnvironment()` が本当に `NETHER` か（カスタム環境になっていないか）を確認してください。

??? failure "通知メッセージを変更したい／検索半径を変えたい"
    現状、設定ファイル経由の変更手段はありません。`NetherEntryListener.java` 内のメッセージ文字列および `SEARCH_RADIUS` 定数を直接書き換えて再ビルドする必要があります。
