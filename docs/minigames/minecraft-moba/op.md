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

!!! success "実装状況：チャンピオン制MOBA"
    **チャンピオン/スキル制・中立クリープ・ワード・リコール・AFK対策・HUD・ルーン（スタッツ装備）** まで実装されました。プレイヤーは5チャンピオンから選択し、スキル（マナ・CD・レベル解放）で戦います。詳細は下記「実装状況」を確認してください。

## 導入手順

1. ビルドした `MinecraftMOBA-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/MinecraftMOBA/config.yml` が自動生成される。
3. 後述の手順でマップ座標を設定する。
4. 設定を変更したら `/moba reload` で再読み込みする。

## マップのセットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **その場に立って** 実行します（実行位置が座標として保存されます）。

```text title="初期リスポーン地点（途中抜け時の戻り先）"
/moba setstartspawn
```

```text title="待機ロビー"
/moba setlobby
```

```text title="ゲームエリアの角1"
/moba setfield 1
```

```text title="ゲームエリアの角2"
/moba setfield 2
```

```text title="赤チームのスポーン"
/moba setspawn red
```

```text title="青チームのスポーン"
/moba setspawn blue
```

```text title="赤チームのコア"
/moba setup core RED
```

```text title="青チームのコア"
/moba setup core BLUE
```

```text title="赤チームのタワー1（1〜5まで繰り返す）"
/moba setup tower RED 1
```

```text title="青チームのタワー1（1〜5まで繰り返す）"
/moba setup tower BLUE 1
```

```text title="ミニオン経路（レーン・通過順を指定）"
/moba setup minion RED LANE_TOP 1
```

```text title="中立クリープの湧き地点を現在地に追加（NORMAL/BUFF/BOSS）"
/moba setup creep NORMAL
```

!!! note "コマンド体系について"
    初期リスポーン・ロビー・エリア範囲・チームスポーンは独立コマンド（`setstartspawn` / `setlobby` / `setfield <1\|2>` / `setspawn <red\|blue>`）です。コア・タワー・ミニオン経路のみ `/moba setup <core\|tower\|minion> ...` のサブコマンド形式です。

!!! tip "ミニオン経路（ウェイポイント）"
    `/moba setup minion` でも設定できますが、経路はまとめて `config.yml` の `MAP_SETTINGS.MINION_WAYPOINTS` を直接編集したほうが確実です。`RED_TEAM` / `BLUE_TEAM` それぞれに `LANE_TOP` / `LANE_MID` / `LANE_BOT` の通過座標を順に並べます。

### 参加・退出看板の作成

ロビーの導線として、看板をコマンドで登録します（`moba.admin` 権限が必要）。看板は **参加 / 退出 / ショップ / チャンピオン選択 / 開始** の5種類です。

```text title="参加・退出・ショップ・チャンピオン選択看板を登録"
/moba setsign <join|leave|shop|champion>
```

```text title="開始看板を登録（クリックでゲーム開始）"
/moba setstart
```

1. 看板を設置する。
2. 看板を見た状態（5ブロック以内）で上記コマンドを実行する。
3. テキストはプラグインが自動で書き込みます。

!!! note "チャンピオン選択看板と開始看板が追加されました"
    プレイヤーは **チャンピオン選択看板** をクリックしてGUIでチャンピオンを選びます。**開始看板**（`/moba setstart`）はクリックでゲームを開始できます。手書き登録は廃止済みで、必ずコマンドで登録してください。`/moba status` で5種すべての登録状況を確認できます。

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

### スキル解放レベル（`EXPERIENCE.LEVEL_UP_REWARDS.SKILL_UNLOCK_LEVELS`）

| キー | 既定値 | 説明 |
|---|---|---|
| `SKILL_UNLOCK_LEVELS` | `[6, 11, 16]` | 2つ目以降のスキルが解放されるレベル。**空だと2つ目以降が即解放（Lv1）になる** ため、既存サーバーは要確認 |

### ミニオン・クリープ・ショップ

- `MINION` … スポーン間隔（30秒）、有効レーン（既定 `LANE_MID` のみのARAM想定）、構成（ゾンビ3＋スケルトン2）、時間強化係数を設定。
- `NEUTRAL_CREEPS` … 通常（ゾンビ）／バフ（アイアンゴーレム・撃破で力）／ボス（ウィザー・チーム強化）の有効化・HP・報酬・`SPAWN_LOCATIONS`・`RESPAWN_TIME` を設定。**`SPAWN_LOCATIONS` が空だとクリープが湧きません**（`/moba setup creep` で追加）。
- `SHOP` … 武器・防具・消耗品・ワードは `SHOP.ITEMS` で定義。**ただしルーン（攻撃/守り/俊足/賢者/加速）の価格・効果はコード側にハードコードされており、config非連動** です（価格変更にはコード修正が必要）。

### チャンピオン/スキル・AFK・リコール・ワード

| セクション | 主なキー | 説明 |
|---|---|---|
| `AFK` | `KICK_TIME`(300) / `CHECK_INTERVAL`(60) / `AUTO_KICK` | 一定時間無移動で自動離脱 |
| `RECALL` | `CHANNEL_TIME`(8) / `COOLDOWN`(60) / `CANCEL_ON_DAMAGE` / `CANCEL_ON_MOVE` | 拠点帰還の詠唱・CD・中断条件 |
| `VISION.WARD` | `DURATION`(180) / `VISION_RANGE`(20) / `MAX_WARDS_PER_PLAYER`(3) / `COST`(75) | ワードの持続・範囲・上限・価格 |
| `RESPAWN` | `BASE_TIME`(5) / `TIME_PER_LEVEL`(0.5) / `KEEP_INVENTORY` | リスポーン時間と装備保持 |
| `KILLSTREAK` | `REQUIRED_KILLS`(3) / `BONUS_EMERALD`(2) | キルストリーク |
| `SIGNS` | `JOIN` / `LEAVE` / `SHOP` / `CHAMPION` / `START` | 看板位置（コマンドで自動保存） |

!!! warning "既存サーバーを更新する場合（config自動マージなし）"
    本プラグインは `saveDefaultConfig()` のみで、**既存の `config.yml` に新セクションを自動追記しません**。旧バージョンから更新したサーバーでは `AFK` / `RECALL` / `VISION` / `NEUTRAL_CREEPS` / `SKILL_UNLOCK_LEVELS` / `SIGNS.CHAMPION`・`START` 等が無いままになります。コード側に既定値があるので落ちはしませんが、「クリープが湧かない」「2つ目以降のスキルがLv1で即解放」などの想定外動作になります。**新規インストール、または config を退避して再生成（手動マージ）を推奨** します。

!!! note "設定変更後は必ず `/moba reload`"
    `config.yml` を編集したら `/moba reload` を実行してください。マップ座標・経済・ショップ・クリープ・看板などの変更が反映されます。

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
| `/moba status` | `moba.admin` | 地点・看板・ゲーム状況の設定状況を確認する |
| `/moba setup <core\|tower\|minion\|creep> ...` | `moba.admin` | コア・タワー・ミニオン経路・クリープ湧き地点の設定 |
| `/moba setsign <join\|leave\|shop\|champion>` | `moba.admin` | 視線先の看板を参加・退出・ショップ・チャンピオン選択看板として登録 |
| `/moba setstart` | `moba.admin` | 視線先の看板を開始看板として登録 |
| `/moba stats` | 全員 | 統計を表示 |

## ゲームの運営

1. プレイヤーがロビー看板から参加し、`MIN_PLAYERS_TO_START` 以上集まるのを待つ。
2. 必要に応じて `/moba start` で開始（自動開始の挙動は実装状況に依存）。
3. 異常時は `/moba stop` で強制停止。

## トラブルシューティング

??? failure "プレイヤーがゲームに参加できない"
    参加導線は **ロビー看板** です。看板が正しく設置されているか、`/moba setlobby` でロビー座標が設定済みかを確認してください。

??? failure "中立クリープが湧かない"
    `NEUTRAL_CREEPS.<タイプ>.SPAWN_LOCATIONS` が空だと湧きません。`/moba setup creep <NORMAL|BUFF|BOSS>` で湧き地点を追加するか、config に座標を記載してください。旧configから更新したサーバーは `NEUTRAL_CREEPS` セクションごと無い場合があります（再生成推奨）。

??? failure "2つ目以降のスキルが最初から使える / おかしい"
    `EXPERIENCE.LEVEL_UP_REWARDS.SKILL_UNLOCK_LEVELS` が空（旧config）だと、2つ目以降のスキルがLv1で即解放されます。`[6, 11, 16]` を設定してください。

??? failure "チャンピオンが選べない"
    チャンピオン選択看板を `/moba setsign champion` で登録してください。`/moba status` の「チャンピオン看板」が設定済みか確認できます。

??? failure "ミニオンが進軍しない／挙動がおかしい"
    ミニオンのAI（Pathfinding）は簡易実装です。`config.yml` の `MINION_WAYPOINTS` の経路が正しくても期待通り動かない場合があります。既定は ARAM 想定で `LANE_MID` のみ有効です。

## 実装状況

!!! success "実装済みの主要機能"
    - チャンピオン/スキル制（5職・各スキル3種・パッシブ・マナ・CD・Lv6/11/16解放・レベルスケール）
    - 中立クリープ（通常/バフ/ボス・報酬・バフ・リスポーン）
    - ワード（敵の発光・上限3個）、リコール（拠点帰還）、AFK自動離脱
    - HUDサイドバー（KDA/CS/Lv/マナ/エメラルド/コアHP/チームキル）、ルーン（スタッツ装備）
    - 経済・経験値・ショップ、チーム管理・状態管理、看板（参加/退出/ショップ/チャンピオン/開始）

!!! warning "既知の制約・注意点"
    - ミニオンの Pathfinding は簡易実装で、経路が正しくても期待通り動かない場合があります。
    - ボスクリープのバフは現状 **力（STRENGTH）固定** で、`BUFF_EFFECT` の設定値を反映しません。
    - クリープをスキル/投射物で倒した場合、撃破者の判定（報酬付与）が状況により外れることがあります。
    - ショップのルーン・ワードの価格/効果はコードにハードコードされており、`SHOP` config の値とは連動しません。
    - 既存サーバーは config の新セクションが自動追記されません（上記「既存サーバーを更新する場合」を参照）。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← MinecraftMOBA 概要へ](index.md){ .md-button }
