<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# WindPvP（ウインドチャージPVP） ― OP・運営ガイド { .page-op #windpvp-op }

WindPvP の導入・地点設定・看板・config・権限・管理コマンドをまとめます。地点・看板はコマンドで登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | WindPvp |
| メインコマンド | `/windpvp`（エイリアス `/wpvp`・`/windcharge`） |
| api-version | 26.1.2 |
| 依存プラグイン | なし（任意 softdepend：Floodgate / Geyser-Spigot＝統合版対応） |
| 設定ファイル | `plugins/WindPvp/config.yml` |
| 権限ノード | `windpvp.admin`（既定OP） |

## 導入手順

1. ビルドした `WindPvp` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される。
3. `/windpvp setstartspawn`・`/windpvp setlobby` で初期スポーンとロビーを設定する。
4. `/windpvp setspawn <番号>` で個人スポーンを複数登録し、`/windpvp setfield 1`・`2` でフィールド範囲を設定する。
5. 参加・離脱・開始の看板を設置する。
6. `/windpvp status` で設定状況を確認する。

!!! note "フィールド範囲は任意（境界縮小に必要）"
    `setfield` を設定すると **境界判定・縮小** が有効になります。未設定の場合は境界縮小を行わず、奈落・溶岩などマップ側の即死判定のみで進行します。

## 設定ツール（おすすめ）

コマンドを打たずに、見ているブロック・看板へ直接セットアップできる OP用ツール（ブレイズロッド）と GUI メニューを用意しています。いずれも `windpvp.admin` 権限が必要で、非OPが入手しても使えません。

```text title="OP用の設定ツール（ブレイズロッド）を受け取る"
/windpvp wand
```

```text title="設定メニュー（GUI）を開く"
/windpvp menu
```

## セットアップ手順（コマンド）

地点系は **実行した位置**、看板系は看板を **見ながら** 実行します。すべて `windpvp.admin` 権限が必要です。

```text title="初期スポーン（途中抜けの戻り先／その場に立って実行）"
/windpvp setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/windpvp setlobby
```

```text title="個人スポーンを番号で登録（複数登録可・試合開始時にシャッフル割り当て）"
/windpvp setspawn 1
```

```text title="登録済みスポーンを削除"
/windpvp removespawn 1
```

```text title="フィールド範囲の角1／角2（その場に立って実行）"
/windpvp setfield 1
/windpvp setfield 2
```

```text title="参加／離脱／開始の看板を登録（看板を見て実行）"
/windpvp setsign join
/windpvp setsign leave
/windpvp setsign start
```

```text title="視線先の看板の登録を解除"
/windpvp setsign delete
```

## ゲーム設定コマンド

```text title="初期残機を設定"
/windpvp setlives 3
```

```text title="最大参加人数を設定"
/windpvp setmax 12
```

```text title="最低開始人数を設定"
/windpvp setmin 2
```

```text title="リスポーン直後の無敵時間（秒）を設定"
/windpvp setprotection 10
```

```text title="ウインドチャージのクールダウン（秒）を設定"
/windpvp setcooldown 5
```

```text title="境界の縮小開始までの秒数を設定"
/windpvp setshrinkdelay 300
```

## config.yml 設定項目

### 人数・残機・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 12 | 最大参加人数 |
| `settings.min-players` | 2 | 最低開始人数（0人開始のみ常に拒否） |
| `settings.default-lives` | 3 | 初期残機数 |
| `settings.respawn-delay` | 5 | リスポーン待機時間（秒） |
| `game.respawn-invincible-seconds` | 10 | リスポーン直後の無敵時間（秒） |
| `game.result-seconds` | 10 | 結果発表からロビー復帰までの秒数 |
| `game.windcharge-cooldown-seconds` | 5 | ウインドチャージのクールタイム（秒・個数は無限） |

### フィールド境界（縮小）

`setfield 1|2` で設定した範囲が対象です。未設定なら境界判定・縮小は行われません。

| キー | 既定値 | 説明 |
|---|---|---|
| `field.shrink-delay-seconds` | 300 | 試合開始から縮小を開始するまでの秒数 |
| `field.shrink-interval-seconds` | 10 | 縮小を刻む間隔（秒） |
| `field.shrink-step-blocks` | 2 | 1回の縮小で内側へ詰める距離（ブロック） |
| `field.min-size-blocks` | 10 | これ以上は縮小しない下限サイズ（一辺） |
| `field.border-damage-per-second` | 1.0 | 境界の外にいる間、1秒ごとに与えるダメージ |

### 落下ダメージ・落下死（`fall`）

参加者だけに作用し、一般プレイヤーや他ゲームには影響しません。ワールドの `fallDamage` ゲームルールが `false` のサーバーでも、試合中の参加者には落下ダメージが入ります。

| キー | 既定値 | 説明 |
|---|---|---|
| `fall.damage-enabled` | true | WindPvP 側で落下ダメージを与える（設定ツールのメニューからも切替可） |
| `fall.damage-multiplier` | 1.0 | 落下ダメージの倍率（1.0＝バニラ同等、2.0で2倍） |
| `fall.death-line` | auto | 即死ライン。`auto`＝フィールド下端から `death-line-margin` ブロック下／`none`＝無効／数値＝そのY座標 |
| `fall.death-line-margin` | 2 | `death-line: auto` のとき、フィールド下端から何ブロック下を即死ラインにするか |

!!! note "ウインドチャージと落下ダメージ"
    ウインドチャージで吹き飛ばされた場合はバニラと同じく「吹き飛ばされた高さより下に落ちた分」だけダメージになります（その場のウインドチャージジャンプでは痛くなく、柱や足場から落とされると痛い）。リスポーン無敵中に即死ラインを割っても残機は減らさずスポーンへ戻します。

### 配布装備（`loadout`）

開始時・リスポーン時に付与する装備を定義します（コード直書きせず config で構築）。`items` の各キーはスロット番号（0〜8がホットバー）で、`material`・`amount`・`name`・`enchantments`・`potion-effects` を指定できます。`offhand` はオフハンド装備です。

!!! note "無限ウインドチャージ"
    `WIND_CHARGE` を割り当てたスロットは、コード側で **「投げても減らない（無限）」** 処理の対象になります。既定のロードアウトは 鉄剣・弓・無限ウインドチャージ・パン32・矢32・盾（オフハンド）です。

!!! note "自動生成される領域（手動編集不要）"
    `locations`（初期スポーン・ロビー・フィールド範囲・個人スポーン）・`signs`（看板）はコマンドで自動保存されます。

### ロビー入退室のインベントリ（`lobby-inventory`・ミニゲーム共通）

ロビーに入ると入室前の状態（持ち物・ゲームモード等）を退避して空にし、ロビーから出ると完全復元します（試合の開始・終了では復元せず、ロビー在籍中は空のまま）。退避データは `plugins/WindPvp/lobby-inventory.yml` に自動保存され、再起動やクラッシュをまたいでも復元されます。

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | 機能の有効／無効。`false` でもロビー入退室で持ち物・ゲームモードに触れないが、試合中の持ち物はメモリ退避で保護（クラッシュは非対応・`true` 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE / SURVIVAL / CREATIVE / KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値とレベルを0にする |
| `lobby-inventory.expire-days` | 30 | 復元されないまま残った退避データの保持日数 |

### 参加中の名前表示と参加通知（`name-tag`・ミニゲーム共通）

参加すると頭上ネームタグ・Tabリスト・チャットの名前が「【ゲーム名】名前」になり、離脱・ログアウト・プラグイン停止で元に戻ります。参加・退出時にはサーバー全体へ告知します。

| キー | 既定値 | 説明 |
|---|---|---|
| `name-tag.enabled` | true | 名前表示を付けるか |
| `name-tag.display-name` | WindPvP | 【】に入れる正式名 |
| `name-tag.color` | GOLD | 【ゲーム名】の色（GOLD / AQUA / GREEN / RED / YELLOW など） |
| `name-tag.announce-join` | true | サーバー全体への参加通知を出すか |

### 統合版（Geyser / Floodgate）対応（`bedrock`）

統合版プレイヤー向けの挙動です。Floodgate / Geyser への依存は持たず、あればリフレクションで判定します（`plugin.yml` の softdepend）。Floodgate が無い環境では `name-prefix` の接頭辞で統合版と見なします。

| キー | 既定値 | 説明 |
|---|---|---|
| `bedrock.wax-signs` | true | 登録看板を蝋引きし、統合版でタップしても編集画面を開かせない |
| `bedrock.spectator-style` | AUTO | 観戦方式。AUTO＝統合版だけゴースト観戦／GHOST＝全員ゴースト／VANILLA＝全員観戦モード |
| `bedrock.name-prefix` | `.` | Floodgate が無いとき、統合版と見なす名前の接頭辞 |

!!! note "config.yml の自動更新"
    `/windpvp reload` 実行時、プラグインは jar 内の最新 config.yml と比較し、**足りないキーがあれば自動で補って**から読み直します（古いファイルは `config.yml.bak-日時` に退避、設定済みの値・看板・地点データは保持）。プラグイン更新後もキーの手動追記は不要です。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/windpvp setstartspawn` | 初期スポーンを設定 |
| `/windpvp setlobby` | ロビーを設定 |
| `/windpvp setspawn <番号>` | 個人スポーンを登録（複数可） |
| `/windpvp removespawn <番号>` | 個人スポーンを削除 |
| `/windpvp setfield <1\|2>` | フィールド範囲の角を設定 |
| `/windpvp setsign <join\|leave\|start\|delete>` | 看板を設定／解除 |
| `/windpvp setlives <数>` / `setmax <数>` / `setmin <数>` | 残機・最大・最低人数を設定 |
| `/windpvp setprotection <秒>` | リスポーン無敵時間を設定 |
| `/windpvp setcooldown <秒>` | ウインドチャージのクールダウンを設定 |
| `/windpvp setshrinkdelay <秒>` | 境界縮小開始までの秒数を設定 |
| `/windpvp wand` | OP用の設定ツール（ブレイズロッド）を受け取る |
| `/windpvp menu` | 設定メニュー（GUI）を開く |
| `/windpvp stop` | ゲームを強制終了 |
| `/windpvp reload` | 設定を再読み込み（最新 config.yml へ自動更新） |
| `/windpvp lobbyfix <プレイヤー> [force]` | ロビー入室前の持ち物が戻らない時の復旧（`force` で退避データを破棄しロックのみ解除） |
| `/windpvp status` | 設定状況・現在の状況を確認（全員可） |
| `/windpvp stats [名前]` | 成績を確認（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `windpvp.admin` | OP | 看板設置・各種設定・強制停止・他プレイヤーの join/leave など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/windpvp join`・`/windpvp leave`（自分）・`/windpvp start`・`/windpvp status`・`/windpvp stats` は権限チェックがなく、全プレイヤーが使えます。他プレイヤーを指定する `join <名前>`・`leave <名前>` は `windpvp.admin` が必要です。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/windpvp status` を確認してください。ロビー・個人スポーンが設定され、ロビーに最低人数以上いる必要があります。

??? failure "境界が縮小しない"
    `setfield 1`・`2` でフィールド範囲が設定されているか確認してください。未設定だと境界縮小は行われません。

??? failure "config を編集したのに反映されない"
    `/windpvp reload` で再読み込みしてください。足りないキーは自動で補われます。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/windpvp lobbyfix <プレイヤー>` で個別に復旧できます。退避データ自体が壊れている場合は `/windpvp lobbyfix <プレイヤー> force` でロックだけ解除してください（退避データは破棄されます）。未復元の件数は `/windpvp status` で確認できます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← WindPvP 概要へ](index.md){ .md-button }
