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

```text
/yukigassen setstartspawn       # 初期リスポーン地点（途中抜け時の戻り先）
/yukigassen setlobby            # 受付ロビー地点
/yukigassen setspawn red        # 赤チームのスポーン地点
/yukigassen setspawn blue       # 青チームのスポーン地点
/yukigassen setfield 1          # ゲームエリアの角1
/yukigassen setfield 2          # ゲームエリアの角2
```

!!! tip "ゲーム開始の必須条件"
    `/yukigassen start` には **赤チームと青チームのスポーン地点が両方とも設定済み** であることが必要です。`setspawn red` と `setspawn blue` を必ず実行してください。エリア（`setfield`）が未設定の場合はエリア外移動の制限がかからない（マップ全体が有効）扱いになります。

### 参加用看板の設置

ロビーに看板を設置し、**看板を見た状態**で以下のコマンドを実行します（`yukigassen.admin` 権限が必要）。

```text
/yukigassen setsign join    # 参加看板として登録
/yukigassen setsign leave   # 退出看板として登録
```

コマンドを実行すると、プラグインが1行目に `[Yukigassen]`・2行目に「参加」または「退出」のテキストを自動書き込みし、位置を config に保存します。手書きでのテキスト入力は不要です。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/yukigassen join [player]` | `yukigassen.admin` | 対象をロビーに参加させる（管理者用） |
| `/yukigassen leave [player]` | `yukigassen.admin` | 対象をロビーから離脱させる（管理者用） |
| `/yukigassen setsign <join\|leave>` | `yukigassen.admin` | 視線先の看板を参加/退出看板として登録する |
| `/yukigassen start` | `yukigassen.admin` | ゲームを開始する |
| `/yukigassen stop` | `yukigassen.admin` | ゲームを強制停止する |
| `/yukigassen zanki <数>` | `yukigassen.admin` | 初期残機数を設定する |
| `/yukigassen teamjoinred [player]` | `yukigassen.admin` | プレイヤーを赤チームに参加させる |
| `/yukigassen teamjoinblue [player]` | `yukigassen.admin` | プレイヤーを青チームに参加させる |
| `/yukigassen setstartspawn` | `yukigassen.admin` | 初期リスポーン地点を設定する |
| `/yukigassen setlobby` | `yukigassen.admin` | ロビー地点を設定する |
| `/yukigassen setspawn red` | `yukigassen.admin` | 赤チームのスポーン地点を設定する |
| `/yukigassen setspawn blue` | `yukigassen.admin` | 青チームのスポーン地点を設定する |
| `/yukigassen setfield 1` | `yukigassen.admin` | ゲームエリアの角1を設定する |
| `/yukigassen setfield 2` | `yukigassen.admin` | ゲームエリアの角2を設定する |
| `/yukigassen setstart` | `yukigassen.admin` | 視線先の看板をゲーム開始看板として登録する |

!!! note "対象（player）指定について"
    `join` / `leave` / `teamjoinred` / `teamjoinblue` は、引数でターゲットセレクタ（プレイヤー名や `@a` など）を指定できます。省略した場合は実行者自身が対象になります。`teamjoinred` / `teamjoinblue` で割り振ったチームは、ゲーム開始時のランダム振り分けより優先されます。

## 運営の流れ

1. プレイヤーがコマンドまたはロビー看板から参加し、人数が集まるのを待つ。
2. 必要に応じて `/yukigassen zanki <数>` で残機を調整する。
3. `/yukigassen start` でゲームを開始する（赤・青スポーン設定が前提）。
4. 相手チーム全滅で自動的に勝敗が決まり、全員がロビーへ戻る。
5. 異常時は `/yukigassen stop` で強制停止できる。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `yukigassen.admin` | OP | join/leave・setsign を含む全管理コマンド |

## トラブルシューティング

??? failure "`/yukigassen start` でゲームが開始できない"
    赤・青チームのスポーン地点が未設定の可能性があります。`/yukigassen setspawn red` と `/yukigassen setspawn blue` を実行してください。また、参加者が1人もいない場合も開始できません。

??? failure "プレイヤーがエリア外に弾かれない"
    `locations.area.pos1` / `pos2` が未設定だと、エリア判定が常に有効（マップ全体OK）になります。`/yukigassen setfield 1` と `/yukigassen setfield 2` でエリアの2点を設定してください。

??? failure "参加看板が機能しない"
    看板の1行目が `[Yukigassen]`、2行目が `join` または `leave` になっているか確認してください。看板の設置・編集には `yukigassen.admin` 権限が必要です。

??? failure "ゲーム終了後にプレイヤーがロビーに戻らない"
    `locations.lobby` が未設定の可能性があります。`/yukigassen setlobby` でロビー地点を設定してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Yukigassen 概要へ](index.md){ .md-button }
