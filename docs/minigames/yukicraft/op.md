<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Yukicraft ― OP・運営ガイド { .page-op #yukicraft-op }

Yukicraft の導入・地点セットアップ・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Yukicraft |
| api-version | 26.1.2 |
| メインコマンド | `/yukicraft <join\|leave\|start\|status\|stop\|setstartspawn\|setlobby\|setspawn\|setfield\|setsign\|reload>` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Yukicraft/config.yml` |

## 導入手順

1. ビルドした `Yukicraft` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Yukicraft/config.yml` が自動生成される。
3. 後述の手順で地点・エリア座標を設定する。
4. 設定を変更したら `/yukicraft reload` で再読み込みする。

## config.yml 設定項目

`config.yml` の各項目は次のとおりです。地点・エリアは基本的にセットアップコマンド（`setstartspawn` / `setlobby` / `setspawn` / `setfield`）で設定しますが、直接編集も可能です。

### 地点設定

| キー | 説明 |
|---|---|
| `spawn` | 初期リスポーン地点。離脱時の移動先（world / x / y / z / yaw / pitch） |
| `lobby` | ロビー地点。参加時・ゲーム終了時の移動先 |
| `game-spawn` | ゲーム開始時のスポーン地点（雪フィールド上空に設定する） |

### エリア設定（`arena`）

| キー | 既定値 | 説明 |
|---|---|---|
| `arena.world` | `world` | ゲームエリアのワールド名 |
| `arena.min-x` / `max-x` | 1075 / 1099 | エリアのX範囲（`setfield 1`/`setfield 2` で設定） |
| `arena.min-z` / `max-z` | 110 / 134 | エリアのZ範囲（`setfield 1`/`setfield 2` で設定） |
| `arena.snow-layers` | 5,10,15,20,25 | 雪を生成するY座標のリスト（各層の高さ） |
| `arena.clear-min-y` | 5 | マップリセット時にクリアする高さの下限 |
| `arena.clear-max-y` | 25 | マップリセット時にクリアする高さの上限 |

!!! note "マップリセットの挙動"
    ゲーム開始時、エリア内の `clear-min-y`〜`clear-max-y` の範囲がリセットされます。`snow-layers` に含まれるYは雪ブロックに、それ以外は空気になります（バリアブロックはそのまま保持されます）。

### ゲーム設定（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `game.max-players` | 16 | 最大参加人数 |
| `game.result-seconds` | 10 | ゲーム終了後、結果発表してからロビーへ戻るまでの秒数 |

!!! note "旧キー `game.end-delay` からの自動移行"
    旧バージョンの `game.end-delay` は、プラグイン起動時に自動で `game.result-seconds` へ移行され、旧キーは削除されます。手動での書き換えは不要です。

### クリーパー設定（`creeper`）

| キー | 既定値 | 説明 |
|---|---|---|
| `creeper.enabled` | true | クリーパー出現の有効化 |
| `creeper.interval` | 30 | 出現間隔（秒） |
| `creeper.count` | 5 | 1回の出現数 |
| `creeper.block-damage` | true | 爆発による地形破壊を許可（true でも雪ブロックのみ破壊。false で破壊なし） |

### メッセージ設定（`messages`）

`messages` 以下に各種メッセージ文字列を定義します。`prefix` のほか、`join` / `leave` / `game-start` / `eliminated` / `winner` / `creeper-spawn` / `game-ending` などがあり、`{player}` `{count}` `{seconds}` のプレースホルダーが使えます。`§` で色コードを指定できます。

## セットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **その場に立って** 実行します（実行位置が座標として保存されます）。

```text title="初期リスポーン地点（離脱時の戻り先）"
/yukicraft setstartspawn
```

```text title="ロビー地点（参加時・終了時の戻り先）"
/yukicraft setlobby
```

```text title="ゲーム開始スポーン地点（雪フィールド上空）"
/yukicraft setspawn
```

```text title="ゲームエリアの角1"
/yukicraft setfield 1
```

```text title="ゲームエリアの角2"
/yukicraft setfield 2
```

!!! tip "セットアップのポイント"
    `setfield 1`/`setfield 2` はエリアのX・Z範囲のみを記録します（高さ範囲は config の `clear-min-y` / `clear-max-y` と `snow-layers` で別途指定）。`setspawn` は雪フィールドより十分上空に設定すると、開始時に安全に着地できます。設定後は `/yukicraft reload` を実行してください。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/yukicraft join` / `leave` / `start` | 全員 | 参加 / 退出 / 開始（看板と同等。引数省略で自分） |
| `/yukicraft status` | 全員 | 設定状況・ゲーム状態を確認する |
| `/yukicraft join <対象>` / `leave <対象>` | `yukicraft.admin` | 対象を指定して参加／離脱させる |
| `/yukicraft stop` | `yukicraft.admin` | ゲームを強制終了する |
| `/yukicraft setstartspawn` | `yukicraft.admin` | 初期リスポーン地点を設定する |
| `/yukicraft setlobby` | `yukicraft.admin` | ロビー地点を設定する |
| `/yukicraft setspawn` | `yukicraft.admin` | ゲーム開始スポーン地点を設定する |
| `/yukicraft setfield <1\|2>` | `yukicraft.admin` | ゲームエリアの角を設定する |
| `/yukicraft setsign <join\|leave\|start\|delete>` | `yukicraft.admin` | 視線先の看板を参加／離脱／開始看板として登録、または登録解除する |
| `/yukicraft reload` | `yukicraft.admin` | config.yml を再読み込みする |

!!! note "看板による参加導線"
    ロビーに看板を設置し、**その看板を見た状態**で以下のコマンドを実行します（`yukicraft.admin` 権限が必要）。

    ```text title="参加看板として登録（1行目 [Yukicraft]・2行目 lobby・4行目に参加人数表示）"
    /yukicraft setsign join
    ```

    ```text title="離脱看板として登録（2行目 leave）"
    /yukicraft setsign leave
    ```

    ```text title="開始看板として登録（クリックでゲーム開始）"
    /yukicraft setsign start
    ```

    ```text title="看板の登録を解除（種別を自動判別・テキストもクリア）"
    /yukicraft setsign delete
    ```

    コマンドを実行すると、プラグインがテキストを自動書き込みし、位置を config（`signs.join` / `signs.leave` / `signs.start`）に保存します。手書きでのテキスト入力は不要です。参加・離脱・開始はすべて `setsign <join\|leave\|start>` で登録し、`setsign delete` で登録解除できます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `yukicraft.admin` | OP | stop / setstartspawn / setlobby / setspawn / setfield / setsign / reload / join &lt;対象&gt; / leave &lt;対象&gt;（管理者コマンドすべて） |

## ゲームの運営

1. プレイヤーが参加看板のクリックまたは管理者による `/yukicraft join` でロビーに集まる。
2. 人数がそろったら `/yukicraft start` で開始する。
3. 異常時は `/yukicraft stop` で強制終了する（`result-seconds` 秒後に全員ロビーへ戻る）。

## トラブルシューティング

??? failure "ゲームを開始できない"
    `/yukicraft start` は参加者が1人以上いないと開始できません。プレイヤーがロビーに参加しているか確認してください。すでにゲームが進行中の場合も開始できません。

??? failure "開始しても雪フィールドが生成されない／おかしい"
    `arena` のエリア座標（`area pos1/pos2`）と `snow-layers`・`clear-min-y`・`clear-max-y` の設定を確認してください。マップリセットはこのエリア・高さ範囲に対して行われます。範囲が未設定だと雪が生成されません。設定変更後は `/yukicraft reload` を実行してください。

??? failure "プレイヤーが看板で参加できない"
    `/yukicraft setsign join`（または `leave`）を、登録したい看板を見ながら実行してください（`yukicraft.admin` 権限が必要）。

??? failure "クリーパーが地形を壊しすぎる"
    `creeper.block-damage` を `false` にすると、クリーパー爆発による地形破壊が無効になります（爆発エフェクトとプレイヤーへのダメージは残ります）。`true` の場合でも雪ブロックのみが破壊対象です。

??? failure "クリーパーを出したくない"
    `creeper.enabled` を `false` にするとクリーパーは出現しなくなります。設定後は `/yukicraft reload` を実行してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Yukicraft 概要へ](index.md){ .md-button }
