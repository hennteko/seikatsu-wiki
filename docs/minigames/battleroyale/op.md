<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Battleroyale ― OP・運営ガイド { .page-op #battleroyale-op }

Battleroyale の導入・初期設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Battleroyale |
| バージョン | 1.0 |
| api-version | 26.1.2 |
| メインコマンド | `/battleroyale`（エイリアス `/btr`） |
| 作者 | henry |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Battleroyale/config.yml` |

## 導入手順

1. ビルドした `Battleroyale` の jar ファイルをサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Battleroyale/config.yml` が自動生成される。
3. 後述の手順でスポーン地点・ロビー・ゲームエリア・看板を設定する。
4. チェストとアイテムプールを登録する。
5. `/btr status` で設定状況を確認する。

!!! note "サーバー起動時の安全対策"
    プラグインは起動時に全ワールドのワールドボーダーをリセットします。また、サーバー再起動を検出するとゲーム状態を自動でリセットするため、ボーダーが縮小したまま残ることはありません。

## config.yml 設定項目

`config.yml` には設定値とゲーム状態（プレイヤーUUIDなど）の両方が保存されます。基本的に下記の「ゲーム設定」項目をコマンドまたは直接編集で設定します。状態系の項目はプラグインが自動管理するため、手動編集は不要です。

### ゲーム設定

| キー | 既定値 | 説明 |
|---|---|---|
| `maxPlayers` | 16 | ロビーの最大参加人数 |
| `initialArea` | 1000 | ワールドボーダーの初期範囲（ブロック） |
| `shrinkTime` | 60 | ワールドボーダーが縮小する間隔（秒）。1回につき **2ブロック** 縮小し、サイズ10で停止 |
| `spawnMinY` | 1 | スポーン地点の最小Y（地表探索の下限） |
| `spawnMaxY` | 5 | スポーン地点の最大Y |
| `chestItemsMin` | 3 | 1チェストに入れるアイテム数の最小 |
| `chestItemsMax` | 9 | 1チェストに入れるアイテム数の最大 |

### 地点・看板設定

| キー | 形式 | 説明 |
|---|---|---|
| `spawn` | `ワールド名,x,y,z,yaw,pitch` | 初期リスポーン場所（途中抜け時などの戻り先） |
| `lobby` | `ワールド名,x,y,z,yaw,pitch` | 受付エリア（ロビー） |
| `areaPos1` | `ワールド名,x,y,z` | ゲームエリアの角1 |
| `areaPos2` | `ワールド名,x,y,z` | ゲームエリアの角2 |
| `joinSign` | `ワールド名,x,y,z` | 参加看板の位置 |
| `leaveSign` | `ワールド名,x,y,z` | 退出看板の位置 |

### チェスト設定

| キー | 形式 | 説明 |
|---|---|---|
| `chestLocations` | 座標のリスト | アイテムが補充されるチェストの座標一覧 |
| `itemPool` | 重み付きエントリのリスト | チェストにランダムで入るアイテム。各要素は `material` / `weight`（出現重み）/ `min-amount` / `max-amount`。既定で6種が初期投入される（旧形式のアイテムID文字列リストも後方互換で読み込み可） |

### 状態管理項目（自動管理）

| キー | 説明 |
|---|---|
| `isGameRunning` | ゲームが進行中かどうか |
| `deadPlayers` | 敗北したプレイヤーのUUIDリスト |
| `teamData` | プレイヤーUUIDとチーム名の対応 |
| `lobbyPlayers` | ロビーに参加しているプレイヤーのUUIDリスト |

!!! note "アイテムプールの動作"
    各チェストには、開始時に `itemPool` から **`chestItemsMin`〜`chestItemsMax`（既定3〜9）個** のアイテムが補充されます。アイテムは **重み（`weight`）に応じて抽選** され、それぞれ `min-amount`〜`max-amount` の個数でスタック配置されます。`itemPool` が空の場合、チェストにはアイテムが入りません。

!!! warning "既存サーバーは config が自動追記されません"
    本プラグインは `saveDefaultConfig()` のみで、**既存の `config.yml` に新しいキーは自動追記されません**。旧バージョンから更新した場合、`spawnMinY` / `spawnMaxY` / `chestItemsMin` / `chestItemsMax` や `itemPool` の重み付き新形式が書き込まれません（コード側に既定値があるため動作はします）。これらを調整するには手動追記、または config を退避して再生成してください。

## セットアップ手順

OP権限で以下のコマンドを実行します。地点系のコマンドは **実行した位置** が座標として保存されます。看板・チェスト系のコマンドは対象ブロックを **5ブロック以内で見ながら** 実行します。

```text title="初期リスポーン地点（その場に立って実行）"
/btr setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/btr setlobby
```

```text title="ゲームエリアの角1（その場に立って実行）"
/btr setfield 1
```

```text title="ゲームエリアの角2（その場に立って実行）"
/btr setfield 2
```

```text title="参加看板（看板を見ながら実行）"
/btr setsign join
```

```text title="退出看板（看板を見ながら実行）"
/btr setsign leave
```

```text title="開始看板（看板を見ながら実行）"
/btr setstart
```

```text title="チェストを登録（チェストを見ながら実行・複数登録可）"
/btr setchest
```

```text title="手に持っているアイテムをアイテムプールに追加"
/btr setitem
```

```text title="ボーダー縮小間隔を設定"
/btr settime <秒>
```

```text title="ボーダーの初期範囲を設定"
/btr setarea <範囲>
```

```text title="最大参加人数を設定"
/btr setmax <人数>
```

!!! tip "看板について"
    `/btr setsign join` / `/btr setsign leave` で登録した看板は、プラグインが自動的に装飾します（`[BattleRoyale]` の見出しや参加人数表示など）。参加看板はクリックでロビー参加、退出看板はクリックでロビー離脱になります。

!!! note "設定の確認"
    `/btr status` で、地点・看板・ゲーム設定・チェスト数・アイテムプール数・現在のロビー状況をまとめて確認できます。ゲーム開始前に未設定項目がないか確認してください。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/btr setstartspawn` | 初期リスポーン地点を設定 |
| `/btr setlobby` | 受付ロビー地点を設定 |
| `/btr setfield <1\|2>` | ゲームエリアの角1または2を設定 |
| `/btr setsign <join\|leave>` | 参加または退出看板を設定 |
| `/btr setstart` | 開始看板を設定 |
| `/btr setchest` | 見ているチェストを登録 |
| `/btr setitem` | 手持ちアイテムをアイテムプールに追加 |
| `/btr settime <秒>` | ワールドボーダーの縮小間隔を設定 |
| `/btr setarea <範囲>` | ワールドボーダーの初期範囲を設定 |
| `/btr setmax <人数>` | 最大参加人数を設定 |
| `/btr setteam <プレイヤー> <チーム名>` | 指定プレイヤーのチームを設定（`@p` などのセレクタ可） |
| `/btr start` | ゲームを開始する |
| `/btr stop` | ゲームを停止する |
| `/btr reset` | 緊急リセット（ゲーム状態とワールドボーダーを強制解除） |
| `/btr status` | 設定状況・現在の状況を確認 |

!!! tip "ゲーム開始の前提条件"
    `/btr start` はロビー・ゲームエリア（pos1/pos2）が設定済みで、かつロビーにプレイヤーが1人以上いる場合のみ成功します。条件を満たさない場合はエラーメッセージが表示されます。

!!! warning "緊急リセット"
    ワールドボーダーが縮小したまま残ってしまった、ゲームが正常に終わらないなどの異常時は `/btr reset` を実行してください。ゲーム状態を強制リセットし、全ワールドのボーダーを解除します。

## 権限ノード

plugin.yml には次の2つの権限が定義されています。コード上でも、管理系コマンドは `battleroyale.admin` でチェックされています。

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `battleroyale.admin` | OP | `setup` / `setchest` / `setitem` / `settime` / `setarea` / `setmax` / `setteam` / `start` / `stop` / `reset` / `status` |
| `battleroyale.play` | OP | プレイヤー参加用コマンド権限。一般プレイヤーの参加・退出は看板で行うためOP既定 |

!!! note "プレイヤー用コマンドの権限チェック"
    `/btr join` / `leave` / `team` は `battleroyale.play` 権限をチェックしています。既定が OP のため、一般プレイヤーはこれらのコマンドで参加・退出できません。一般プレイヤーの参加・退出はロビーに設置された看板から行います。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/btr status` で設定を確認してください。ロビー（`lobby`）とゲームエリア（`areaPos1` / `areaPos2`）が未設定だと開始できません。また、ロビーにプレイヤーが1人以上いる必要があります。

??? failure "ワールドボーダーが縮小したまま残った"
    `/btr reset` を実行してください。ゲーム状態を強制リセットし、全ワールドのボーダーを解除します。プラグインはサーバー起動時にもボーダーを自動リセットするため、サーバー再起動でも解消されます。

??? failure "チェストにアイテムが入らない"
    アイテムプール（`itemPool`）が空の可能性があります。`/btr setitem` でアイテムを追加するか、`config.yml` の `itemPool` にアイテムIDを記載してください。また、登録した座標のブロックが実際にチェストでないとアイテムは補充されません。

??? failure "看板をクリックしても参加・退出できない"
    `/btr setsign join` / `/btr setsign leave` で看板が登録済みか `/btr status` で確認してください。看板の位置を変更・破壊した場合は再登録が必要です。

??? failure "プレイヤーがゲームエリア外にスポーンする"
    `areaPos1` と `areaPos2` がゲームエリアの正しい対角に設定されているか確認してください。スポーン地点はこの2点で囲まれた範囲内からランダムに選ばれます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Battleroyale 概要へ](index.md){ .md-button }
