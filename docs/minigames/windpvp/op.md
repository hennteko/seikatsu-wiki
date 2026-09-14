<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# WindPvP（ウインドチャージPVP） ― OP・運営ガイド { .page-op #windpvp-op }

WindPvP の導入・地点設定・看板・config・権限・管理コマンドをまとめます。地点・看板はコマンドで登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | WindPvp |
| メインコマンド | `/windpvp`（エイリアス `/wpvp`・`/windcharge`） |
| api-version | 26.1.2 |
| 依存プラグイン | なし |
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

### 配布装備（`loadout`）

開始時・リスポーン時に付与する装備を定義します（コード直書きせず config で構築）。`items` の各キーはスロット番号（0〜8がホットバー）で、`material`・`amount`・`name`・`enchantments`・`potion-effects` を指定できます。`offhand` はオフハンド装備です。

!!! note "無限ウインドチャージ"
    `WIND_CHARGE` を割り当てたスロットは、コード側で **「投げても減らない（無限）」** 処理の対象になります。既定のロードアウトは 鉄剣・弓・無限ウインドチャージ・パン32・矢32・盾（オフハンド）です。

!!! note "自動生成される領域（手動編集不要）"
    `locations`（初期スポーン・ロビー・フィールド範囲・個人スポーン）・`signs`（看板）はコマンドで自動保存されます。

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
| `/windpvp stop` | ゲームを強制終了 |
| `/windpvp reload` | 設定を再読み込み |
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
    `/windpvp reload` で再読み込みしてください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← WindPvP 概要へ](index.md){ .md-button }
