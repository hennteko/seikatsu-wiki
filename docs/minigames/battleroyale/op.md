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
| `game.result-seconds` | 10 | 勝敗確定後、結果発表を表示してからロビーへ戻すまでの秒数 |
| `shrinkTime` | 60 | ワールドボーダーが縮小する **間隔**（秒） |
| `shrinkAmount` | 50 | 1回の縮小で縮むブロック数 |
| `shrinkSpeed` | 5 | 1回の縮小にかける秒数（ボスバー演出） |
| `minBorderSize` | 10 | これ以下には縮まない停止サイズ |
| `spawnMinY` | 1 | スポーン地点の最小Y（地表探索の下限） |
| `spawnMaxY` | 5 | スポーン地点の最大Y |
| `chestItemsMin` | 3 | 1チェストに入れるアイテム数の最小 |
| `chestItemsMax` | 9 | 1チェストに入れるアイテム数の最大 |

!!! note "ボーダーの初期サイズは自動算出です"
    ワールドボーダーの **初期サイズは設定したフィールド範囲（`areaPos1`〜`areaPos2`）から自動で決まります**（X幅・Z幅の大きい方）。初期サイズを直接指定する設定キー（旧 `initialArea`）はありません。`shrinkTime` 秒ごとに `shrinkAmount` ブロックずつ（`shrinkSpeed` 秒かけて）縮小し、`minBorderSize` で停止します。

### 地点・看板設定

| キー | 形式 | 説明 |
|---|---|---|
| `spawn` | `ワールド名,x,y,z,yaw,pitch` | 初期リスポーン場所（途中抜け時などの戻り先） |
| `lobby` | `ワールド名,x,y,z,yaw,pitch` | 受付エリア（ロビー） |
| `areaPos1` | `ワールド名,x,y,z` | ゲームエリアの角1 |
| `areaPos2` | `ワールド名,x,y,z` | ゲームエリアの角2 |
| `joinSign` | `ワールド名,x,y,z` | 参加看板の位置 |
| `leaveSign` | `ワールド名,x,y,z` | 退出看板の位置 |
| `startSign` | `ワールド名,x,y,z` | 開始看板の位置（`/btr setsign start` で登録） |

### チェスト設定

| キー | 形式 | 説明 |
|---|---|---|
| `chestLocations` | 座標のリスト | アイテムが補充されるチェストの座標一覧 |
| `itemPool` | 重み付きエントリのリスト | チェストにランダムで入るアイテム。既定で6種が初期投入される。3形式に対応：①`material` 形式（`material` / `weight` / `min-amount` / `max-amount`）、②`item` 形式（ポーション効果・チップ矢・エンチャント等のメタ情報を含む `ItemStack`。`/btr setitem` で自動的にこの形式で保存）、③旧形式（アイテムID文字列のみ・重み1/個数1扱い）も後方互換で読み込み可 |

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
/btr setsign start
```

```text title="視線先の看板の登録を解除（看板を見ながら実行・表示もクリア）"
/btr setsign delete
```

```text title="チェストを登録（チェストを見ながら実行・複数登録可）"
/btr setchest
```

```text title="手持ちアイテムをアイテムプールに追加（重み・最小/最大個数を任意指定）"
/btr setitem [重み] [最小個数] [最大個数]
```

```text title="アイテムプールの一覧と出現割合(%)を表示"
/btr itemlist
```

```text title="指定番号のアイテムの重みを変更"
/btr setweight <番号> <重み>
```

```text title="指定番号のアイテムをプールから削除"
/btr removeitem <番号>
```

```text title="ボーダー縮小間隔を設定（秒）"
/btr settime <秒>
```

```text title="最大参加人数を設定"
/btr setmax <人数>
```

!!! note "ボーダーの初期範囲はコマンド不要"
    初期サイズはフィールド範囲（`setfield 1`/`2`）から自動算出されます。範囲を直接指定するコマンド（旧 `setarea`）はありません。縮小量・停止サイズ・縮小演出秒は `config.yml` の `shrinkAmount` / `minBorderSize` / `shrinkSpeed` で調整します。

!!! tip "看板について"
    `/btr setsign <join|leave|start>` で登録した看板は、プラグインが自動的に装飾します（`[BattleRoyale]` の見出しや参加人数表示など）。参加看板はクリックでロビー参加、退出看板はクリックで離脱、開始看板はクリックでゲーム開始（**開始は全員可**）です。なお `/btr join`・`/btr leave`・`/btr start` コマンド（いずれも全員可）でも操作できます。登録した看板を解除したいときは、その看板を見ながら `/btr setsign delete` を実行すると config から削除され、看板の表示もクリアされます。

!!! note "設定の確認"
    `/btr status` で、地点・看板・ゲーム設定・チェスト数・アイテムプール数・現在のロビー状況をまとめて確認できます。ゲーム開始前に未設定項目がないか確認してください。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/btr setstartspawn` | 初期リスポーン地点を設定 |
| `/btr setlobby` | 受付ロビー地点を設定 |
| `/btr setfield <1\|2>` | ゲームエリアの角1または2を設定 |
| `/btr setsign <join\|leave\|start>` | 参加／退出／開始看板を設定 |
| `/btr setsign delete` | 視線先の看板の登録を解除（表示もクリア） |
| `/btr setchest` | 見ているチェストを登録 |
| `/btr setitem [重み] [最小] [最大]` | 手持ちアイテムをアイテムプールに追加 |
| `/btr itemlist` | アイテムプールの一覧と出現割合を表示 |
| `/btr setweight <番号> <重み>` | 指定アイテムの重みを変更 |
| `/btr removeitem <番号>` | 指定アイテムを削除 |
| `/btr settime <秒>` | ワールドボーダーの縮小間隔を設定 |
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
| `battleroyale.admin` | OP | 管理系（setstartspawn / setlobby / setfield / setsign / setchest / setitem / itemlist / setweight / removeitem / settime / setmax / setteam / stop / reset。`/btr join <名前>`・`/btr leave <名前>` の他プレイヤー指定も含む） |
| `battleroyale.play` | OP | `/btr team`（チーム参加）の使用権限 |

!!! info "権限不要で全員が使えるコマンド"
    `/btr join`・`/btr leave`（自分のみ）・`/btr start`・`/btr status` は権限チェックがなく、全プレイヤー（コマンドブロック／コンソール含む）が実行できます。`/btr start` は看板クリックと同じく誰でもゲームを開始できる点に注意してください。

!!! note "join / leave は権限不要・team はOP権限"
    `/btr join`・`/btr leave` は **権限チェックがなく全プレイヤーが使えます**（看板の右クリックと同じ動作）。一方 `/btr team <チーム名>` は `battleroyale.play`（既定OP）が必要なため、一般プレイヤーにチーム分けを使わせたい場合は権限プラグインで `battleroyale.play` を付与してください。

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
