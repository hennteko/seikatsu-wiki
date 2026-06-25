<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Yukigassen ― OP・運営ガイド { .page-op #yukigassen-op }

Yukigassen の導入・地点のセットアップ・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Yukigassen |
| バージョン | 1.0 |
| api-version | 26.1.2 |
| メインコマンド | `/yukigassen <subcommand>` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Yukigassen/config.yml` |

## 導入手順

1. ビルドした `Yukigassen` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Yukigassen/config.yml` が自動生成される。
3. 後述の手順で各種地点を設定する。
4. プレイヤーが集まったら `/yukigassen start` でゲームを開始する。

## config.yml 設定項目

設定ファイルは以下のキーで構成されます。地点（`locations`）はセットアップコマンドで自動的に書き込まれるため、手動編集は不要です。

| キー | 既定値 | 説明 |
|---|---|---|
| `default-lives` | 3 | プレイヤーの初期残機数 |
| `locations.spawn` | 未設定 | 初期リスポーン地点（途中抜け時の戻り先） |
| `locations.lobby` | 未設定 | ロビー地点（受付エリア・ゲーム終了後の戻り先） |
| `locations.spawnred` | 未設定 | 赤チームのスポーン地点（ゲーム中のリスポーン先） |
| `locations.spawnblue` | 未設定 | 青チームのスポーン地点（ゲーム中のリスポーン先） |
| `locations.area.pos1` | 未設定 | ゲームエリアの角1 |
| `locations.area.pos2` | 未設定 | ゲームエリアの角2 |

!!! note "残機設定について"
    `config.yml` の `default-lives` はプラグイン起動時の既定残機です。ゲームごとに変更したい場合は `/yukigassen zanki <数>` を使ってください（こちらは config.yml には保存されません）。

## セットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **その場に立って** 実行します（実行位置が座標として保存されます）。

```text title="初期リスポーン地点（途中抜け時の戻り先）"
/yukigassen setstartspawn
```

```text title="受付ロビー地点"
/yukigassen setlobby
```

```text title="赤チームのスポーン地点"
/yukigassen setspawn red
```

```text title="青チームのスポーン地点"
/yukigassen setspawn blue
```

```text title="ゲームエリアの角1"
/yukigassen setfield 1
```

```text title="ゲームエリアの角2"
/yukigassen setfield 2
```

!!! tip "ゲーム開始の必須条件"
    `/yukigassen start` には **赤チームと青チームのスポーン地点が両方とも設定済み** であることが必要です。`setspawn red` と `setspawn blue` を必ず実行してください。エリア（`setfield`）が未設定の場合はエリア外移動の制限がかからない（マップ全体が有効）扱いになります。

### 参加用看板の設置

ロビーに看板を設置し、**看板を見た状態**で以下のコマンドを実行します（`yukigassen.admin` 権限が必要）。

看板は **参加 / 退出 / 開始** の3種類です。`yukigassen.admin` 権限で、看板を見ながら登録します。

```text title="参加看板として登録"
/yukigassen setsign join
```

```text title="退出看板として登録"
/yukigassen setsign leave
```

```text title="開始看板として登録（クリックでゲーム開始）"
/yukigassen setsign start
```

コマンドを実行すると、プラグインが看板テキストを自動書き込みし、位置を config の `signs.join` / `signs.leave` / `signs.start` に保存します（座標で判定するため手書き登録は廃止）。開始看板は `/yukigassen setsign start` で登録します（独立した `setstart` コマンドはありません）。

## コマンド

### プレイヤー用（全員可）

| コマンド | 説明 |
|---|---|
| `/yukigassen join` | ロビーに参加する（参加看板と同等） |
| `/yukigassen leave` | ロビーから退出する（退出看板と同等） |
| `/yukigassen start` | ゲームを開始する（開始看板と同等） |

!!! note "他プレイヤー指定はadmin権限"
    `join` / `leave` に **対象（プレイヤー名や `@a` などのセレクタ）を指定する場合のみ** `yukigassen.admin` が必要です。引数を省略すると実行者自身が対象になり、権限は不要です。`start` は誰でも実行できます。`teamjoinred` / `teamjoinblue` というコマンドは存在しません（チーム振り分けは開始時に自動）。

### 管理用（`yukigassen.admin`）

| コマンド | 説明 |
|---|---|
| `/yukigassen stop` | ゲームを強制停止する |
| `/yukigassen zanki <数>` | 初期残機数を設定する |
| `/yukigassen setstartspawn` | 初期リスポーン地点を設定する |
| `/yukigassen setlobby` | ロビー地点を設定する |
| `/yukigassen setspawn <red\|blue>` | チームのスポーン地点を設定する |
| `/yukigassen setfield <1\|2>` | ゲームエリアの角を設定する |
| `/yukigassen setsign <join\|leave\|start>` | 視線先の看板を参加/退出/開始看板として登録する |
| `/yukigassen status` | 設定状況（地点・看板登録数・ゲーム状態）を確認する |

## 運営の流れ

1. プレイヤーがコマンドまたはロビー看板から参加し、人数が集まるのを待つ。
2. 必要に応じて `/yukigassen zanki <数>` で残機を調整する。
3. `/yukigassen start` でゲームを開始する（赤・青スポーン設定が前提）。
4. 相手チーム全滅で自動的に勝敗が決まり、全員がロビーへ戻る。
5. 異常時は `/yukigassen stop` で強制停止できる。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `yukigassen.admin` | OP | stop・zanki・setstartspawn・setlobby・setspawn・setfield・setsign・status、および join/leave での他プレイヤー指定。`join`/`leave`/`start`（自分対象）は権限不要 |

## トラブルシューティング

??? failure "`/yukigassen start` でゲームが開始できない"
    赤・青チームのスポーン地点が未設定の可能性があります。`/yukigassen setspawn red` と `/yukigassen setspawn blue` を実行してください。また、参加者が1人もいない場合も開始できません。

??? failure "プレイヤーがエリア外に弾かれない"
    `locations.area.pos1` / `pos2` が未設定だと、エリア判定が常に有効（マップ全体OK）になります。`/yukigassen setfield 1` と `/yukigassen setfield 2` でエリアの2点を設定してください。

??? failure "参加看板が機能しない"
    看板は `/yukigassen setsign <join|leave|start>` で登録した **保存座標** で判定されます（手書きテキストでは機能しません）。看板が登録済みか、`/yukigassen status` の参加/離脱/開始看板の登録数で確認してください。看板の登録には `yukigassen.admin` 権限が必要です。

??? failure "ゲーム終了後にプレイヤーがロビーに戻らない"
    `locations.lobby` が未設定の可能性があります。`/yukigassen setlobby` でロビー地点を設定してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Yukigassen 概要へ](index.md){ .md-button }
