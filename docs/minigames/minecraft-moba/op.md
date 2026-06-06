<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# MinecraftMOBA ― OP・運営ガイド { .page-op #moba-op }

MinecraftMOBA の導入・マップ設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | MinecraftMOBA |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/moba`（エイリアス `/mb`）、`/shop` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/MinecraftMOBA/config.yml` |

!!! warning "実装状況：Phase 1"
    現在は **基本構造のみ実装済み** です。下記「実装状況」の節を必ず確認してください。戦闘・勝敗判定などコアなゲーム進行は未実装です。

## 導入手順

1. ビルドした `MinecraftMOBA-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/MinecraftMOBA/config.yml` が自動生成される。
3. 後述の手順でマップ座標を設定する。
4. 設定を変更したら `/moba reload` で再読み込みする。

## マップのセットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **その場に立って** 実行します（実行位置が座標として保存されます）。

```text
/moba setstartspawn                # 初期リスポーン地点（途中抜け時の戻り先）
/moba setlobby                     # 待機ロビー
/moba setfield 1                   # ゲームエリアの角1
/moba setfield 2                   # ゲームエリアの角2
/moba setspawn red                 # 赤チームのスポーン
/moba setspawn blue                # 青チームのスポーン
/moba setup core RED               # 赤チームのコア
/moba setup core BLUE              # 青チームのコア
/moba setup tower RED 1            # 赤チームのタワー1（1〜5まで繰り返す）
/moba setup tower BLUE 1           # 青チームのタワー1（1〜5まで繰り返す）
/moba setup minion RED LANE_TOP 1  # ミニオン経路（レーン・通過順を指定）
```

!!! note "コマンド体系について"
    初期リスポーン・ロビー・エリア範囲・チームスポーンは独立コマンド（`setstartspawn` / `setlobby` / `setfield <1\|2>` / `setspawn <red\|blue>`）です。コア・タワー・ミニオン経路のみ `/moba setup <core\|tower\|minion> ...` のサブコマンド形式です。

!!! tip "ミニオン経路（ウェイポイント）"
    `/moba setup minion` でも設定できますが、経路はまとめて `config.yml` の `MAP_SETTINGS.MINION_WAYPOINTS` を直接編集したほうが確実です。`RED_TEAM` / `BLUE_TEAM` それぞれに `LANE_TOP` / `LANE_MID` / `LANE_BOT` の通過座標を順に並べます。

### 参加・退出看板の作成

ロビーの参加導線として、看板をコマンドで登録します（`moba.admin` 権限が必要）。

1. 看板を設置する。
2. 看板を見た状態で `/moba setsign <join|leave|shop>` を実行する。
3. テキストはプラグインが自動で書き込みます。

!!! note "手書き登録は廃止済み"
    以前の方式（1行目に `[MOBA]`、2行目に `lobby`/`leave` を手書きして認識させる方式）は廃止されています。必ずコマンドで登録してください。

## config.yml 主要項目

### ゲーム基本設定（`GAME_SETTINGS`）

| キー | 既定値 | 説明 |
|---|---|---|
| `MAX_DURATION` | 3600 | 最大ゲーム時間（秒）。0で無制限 |
| `TEAM_SIZE` | 5 | 1チームの最大人数 |
| `MIN_PLAYERS_TO_START` | 2 | 開始に必要な最小人数 |
| `AUTO_TEAM_BALANCE` | true | チーム人数の自動均等化 |
| `COUNTDOWN_TIME` | 30 | 開始前カウントダウン（秒） |
| `END_SCREEN_DURATION` | 10 | ゲーム終了後の統計表示時間（秒） |

### 経済（`ECONOMY`）

| キー | 既定値 | 説明 |
|---|---|---|
| `GAIN_PLAYER_KILL` | 5 | プレイヤー撃破の獲得エメラルド |
| `GAIN_TOWER_DESTROY` | 25 | タワー破壊ボーナス |
| `GAIN_PASSIVE_RATE` | 5 | 自動獲得量（`PASSIVE_INTERVAL`=10秒ごと） |
| `DEATH_EMERALD_LOST_PERCENT` | 0.10 | デス時に失う割合（10%） |

### 経験値・レベル（`EXPERIENCE`）

| キー | 既定値 | 説明 |
|---|---|---|
| `PLAYER_MAX_LEVEL` | 18 | 最大レベル |
| `EXP_PLAYER_KILL` | 100 | プレイヤー撃破の獲得経験値 |
| `HEALTH_PER_LEVEL` | 2.0 | レベルごとの体力増加 |
| `DAMAGE_PER_LEVEL` | 0.1 | レベルごとの攻撃力増加（10%） |

### 構造物（`STRUCTURES`）

| キー | 既定値 | 説明 |
|---|---|---|
| `CORE.BASE_HEALTH` | 1000 | コアのHP |
| `CORE.ATTACKABLE_REQUIREMENT` | ALL_TOWERS_DESTROYED | コア攻撃の条件 |
| `TOWER.COUNT` | 5 | 1チームのタワー数 |
| `TOWER.BASE_HEALTH` | 300 | タワーのHP |
| `TOWER.ATTACK_RANGE` | 15 | タワーの攻撃範囲（ブロック） |

### ミニオン・クリープ・ショップ

- `MINION` … スポーン間隔（30秒）、レーン構成（ゾンビ3＋スケルトン2）、時間強化係数を設定。
- `NEUTRAL_CREEPS` … 通常／バフ（アイアンゴーレム）／ボス（ウィザー）の有効化・HP・報酬・スポーン座標。
- `SHOP` … 販売アイテムを `SHOP.ITEMS` で定義。価格・エンチャント・表示名・説明を編集可能。

!!! note "設定変更後は必ず `/moba reload`"
    `config.yml` を編集したら `/moba reload` を実行してください。マップ座標・経済・ショップなどの変更が反映されます。

## 権限ノード

`plugin.yml` で定義されている権限は **`moba.admin` のみ** です。コード上でも `moba.admin` のみがチェックされ、`/shop` や `/moba stats` には専用の権限ノードはありません（一般プレイヤーはそのまま実行可能）。

| 権限 | 既定 | 用途 |
|---|---|---|
| `moba.admin` | OP | `/moba start` / `stop` / `reload` / `setup ...` および `[MOBA]` 看板の設置に必要 |

!!! note "プレイヤー向け権限について"
    `/shop`・`/moba stats` は権限チェックがありません（コマンドの実行制限なし）。プレイヤーに使わせたくない場合はサーバーの権限管理プラグインで `moba` / `shop` コマンド自体を制限してください。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/moba start` | `moba.admin` | ゲームを開始する |
| `/moba stop` | `moba.admin` | ゲームを停止する |
| `/moba reload` | `moba.admin` | config.yml を再読み込み |
| `/moba setup ...` | `moba.admin` | マップ座標の設定（前述） |
| `/moba setsign <join\|leave\|shop>` | `moba.admin` | 視線先の看板を参加・退出・ショップ看板として登録する |
| `/moba stats` | 全員 | 統計を表示 |

## ゲームの運営

1. プレイヤーがロビー看板から参加し、`MIN_PLAYERS_TO_START` 以上集まるのを待つ。
2. 必要に応じて `/moba start` で開始（自動開始の挙動は実装状況に依存）。
3. 異常時は `/moba stop` で強制停止。

## トラブルシューティング

??? failure "プレイヤーがゲームに参加できない"
    参加導線は **ロビー看板** です。看板が正しく設置されているか、`/moba setlobby` でロビー座標が設定済みかを確認してください。

??? failure "ミニオンが進軍しない／挙動がおかしい"
    既知の制約です。ミニオンのAI（Pathfinding）は簡易実装の段階で、`config.yml` の経路設定が正しくても期待通り動かない場合があります。

??? failure "コアが破壊できない"
    `STRUCTURES.CORE.ATTACKABLE_REQUIREMENT` が `ALL_TOWERS_DESTROYED` の場合、タワー5本破壊が前提です。なお勝敗判定自体は未実装です（下記参照）。

## 実装状況（Phase 1）

!!! danger "未実装の主要機能"
    以下は **未実装** です。本番イベントでの利用前に必ず把握してください。

    - 戦闘・デス・リスポーンのイベント処理
    - タワーの自動攻撃、コアへのダメージ・**勝敗判定**
    - ミニオンの本格的なPathfinding AI
    - リコール（帰還）、中立クリープの一部挙動
    - スキルシステム、視界（戦場の霧）、アシスト判定

実装済みは「プラグイン基本構造／config読み込み／チーム管理／ゲーム状態管理／経済・経験値・ショップの土台」までです。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← MinecraftMOBA 概要へ](index.md){ .md-button }
