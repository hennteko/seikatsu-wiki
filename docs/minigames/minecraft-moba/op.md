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

!!! info "セットアップコマンドはすべてフラット化されました"
    旧 `/moba setup <core\|tower\|minion\|creep>` のサブコマンド形式は廃止され、`setcore` / `setturret` / `setlane` / `setcamp` の独立コマンドに統合されました。コア・タワー・レーン・クリープ湧きなどの **ステージ固有のジオメトリ** は、`setstage` で選んだ編集ステージ（`STAGES.<識別子>.*`）配下に保存されます。

```text title="編集対象ステージを切り替える（以降の地点設定の保存先・既定は default）"
/moba setstage <識別子>
```

```text title="初期リスポーン地点（途中抜け時の戻り先・ステージ非依存）"
/moba setstartspawn
```

```text title="待機ロビー（ステージ非依存）"
/moba setlobby
```

```text title="ゲームエリアの角1（編集ステージに保存）"
/moba setfield 1
```

```text title="ゲームエリアの角2（編集ステージに保存）"
/moba setfield 2
```

```text title="赤チームのスポーン"
/moba setspawn red
```

```text title="青チームのスポーン"
/moba setspawn blue
```

```text title="赤チームのコア"
/moba setcore RED
```

```text title="青チームのコア"
/moba setcore BLUE
```

```text title="赤チームの防衛塔1（1〜5まで繰り返す）"
/moba setturret RED 1
```

```text title="青チームの防衛塔1（1〜5まで繰り返す）"
/moba setturret BLUE 1
```

```text title="ミニオン経路（チーム・レーン・通過順を指定）"
/moba setlane RED LANE_TOP 1
```

```text title="中立クリープの湧き地点を現在地に追加（NORMAL/BUFF/BOSS）"
/moba setcamp NORMAL
```

```text title="1チームの最大人数を設定（GAME_SETTINGS.TEAM_SIZE）"
/moba setmax <人数>
```

```text title="開始最低人数を設定（GAME_SETTINGS.MIN_PLAYERS_TO_START）"
/moba setmin <人数>
```

```text title="最大試合時間を設定（秒・GAME_SETTINGS.MAX_DURATION）"
/moba settime <秒>
```

!!! note "コマンド体系について"
    初期リスポーン・ロビーはステージ非依存の独立コマンド（`setstartspawn` / `setlobby`）です。エリア範囲・チームスポーン・コア・防衛塔・レーン・クリープ湧きは、いずれも **編集ステージ固有** の独立コマンド（`setfield <1\|2>` / `setspawn <red\|blue>` / `setcore <RED\|BLUE>` / `setturret <RED\|BLUE> <1-5>` / `setlane <RED\|BLUE> <LANE> <番号>` / `setcamp <NORMAL\|BUFF\|BOSS>`）です。数値設定は `setmax` / `setmin` / `settime` で行い、実行時に config へ即保存・自動リロードされます。

!!! tip "複数ステージ（マップ）の運用"
    `/moba setstage <識別子>` で編集対象ステージを切り替えると、以降のジオメトリ設定は `STAGES.<識別子>.*` に保存されます。試合は `/moba start <識別子>` または該当ステージの開始看板から、ステージを指定して開始できます。識別子を省略した場合は編集ステージ（単一運用なら `default`）が使われます。登録済みステージは `/moba status` や各コマンドのタブ補完で確認できます。

!!! tip "ミニオン経路（ウェイポイント）"
    `/moba setlane <RED\|BLUE> <LANE_TOP\|LANE_MID\|LANE_BOT> <番号>` でも設定できますが、経路はまとめて `config.yml` の `STAGES.<識別子>.MINION_WAYPOINTS` を直接編集したほうが確実です。`RED_TEAM` / `BLUE_TEAM` それぞれに `LANE_TOP` / `LANE_MID` / `LANE_BOT` の通過座標を順に並べます（既定ステージの既定値は `MAP_SETTINGS.MINION_WAYPOINTS` にあります）。

### 参加・退出看板の作成

ロビーの導線として、看板をコマンドで登録します（`moba.admin` 権限が必要）。看板は **参加 / 退出 / ショップ / チャンピオン選択 / 開始** の5種類です。

```text title="看板を登録（5種類いずれか・看板を見て実行）"
/moba setsign <join|leave|shop|champion|start>
```

```text title="開始看板をステージ指定で登録（省略時は編集ステージ）"
/moba setsign start <識別子>
```

```text title="視線先の看板の登録を解除（種類は自動判別）"
/moba setsign delete
```

看板の種類は **join（参加）/ leave（退出）/ shop（ショップ）/ champion（チャンピオン選択）/ start（開始）** の5種類で、いずれも `setsign` で登録します。

1. 看板を設置する。
2. 看板を見た状態（5ブロック以内）で上記コマンドを実行する。
3. テキストはプラグインが自動で書き込みます。

!!! note "開始看板は setsign start で登録します（ステージ別に複数可）"
    開始看板は **`/moba setsign start [識別子]`** で登録します（独立した `setstart` コマンドは廃止されました）。識別子を省略すると編集ステージで登録され、**開始看板だけは識別子ごとに複数登録できます**（他の4種は1つずつ）。プレイヤーは **チャンピオン選択看板** をクリックしてGUIでチャンピオンを選び、**開始看板** をクリックすると、その看板に紐づくステージでゲームが開始されます。手書き登録は廃止済みで、必ずコマンドで登録してください。`/moba status` で各看板の登録状況（開始看板は登録数）を確認できます。

!!! tip "看板の登録解除"
    登録を取り消すときは、対象の看板を見た状態で **`/moba setsign delete`** を実行します。種類（参加/退出/ショップ/チャンピオン/開始）は自動判別され、看板テキストもクリアされます。

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
| `TOWER.COUNT` | 2 | 1チームのタワー数（ARAM想定。既定マップは各2基。実際の数は `TOWERS` の設定数で決まる） |
| `TOWER.BASE_HEALTH` | 300 | タワーのHP |
| `TOWER.ATTACK_RANGE` | 15 | タワーの攻撃範囲（ブロック） |

### スキル解放レベル（`EXPERIENCE.LEVEL_UP_REWARDS.SKILL_UNLOCK_LEVELS`）

| キー | 既定値 | 説明 |
|---|---|---|
| `SKILL_UNLOCK_LEVELS` | `[6, 11, 16]` | 2つ目以降のスキルが解放されるレベル。**空だと2つ目以降が即解放（Lv1）になる** ため、既存サーバーは要確認 |

### ミニオン・クリープ・ショップ

- `MINION` … スポーン間隔（30秒）、有効レーン（既定 `LANE_MID` のみのARAM想定）、構成（ゾンビ3＋スケルトン2）、時間強化係数を設定。
- `NEUTRAL_CREEPS` … 通常（ゾンビ）／バフ（アイアンゴーレム・撃破で力）／ボス（ウィザー・チーム強化）の有効化・HP・報酬・`RESPAWN_TIME` などを設定。湧き座標は **編集ステージ固有** で `STAGES.<識別子>.CREEP_SPAWNS.<NORMAL\|BUFF\|BOSS>` に保存され、`/moba setcamp <NORMAL\|BUFF\|BOSS>` で追加します。**湧き座標が空だとクリープが湧きません**（既定ステージの参考値は `NEUTRAL_CREEPS.<タイプ>.SPAWN_LOCATIONS` にあります）。
- `SHOP` … **現バージョンの店の品揃えはコード側にハードコード**されており、`config.yml` の `SHOP.ITEMS` は読み込まれていません。実際の店には **剣4種・鉄防具4種・金リンゴ・ルーン5種（攻撃の宝珠/守りの宝珠/俊足のブーツ/賢者のオーブ/加速のルーン）の計14品** のみが並びます。`SHOP.ITEMS` に書いた弓・矢・各ポーション・革防具・ワード等は店に反映されないため、品揃え変更にはコード修正が必要です。

### チャンピオン/スキル・AFK・リコール・ワード

| セクション | 主なキー | 説明 |
|---|---|---|
| `AFK` | `KICK_TIME`(300) / `CHECK_INTERVAL`(60) / `AUTO_KICK` | 一定時間無移動で自動離脱 |
| `RECALL` | `CHANNEL_TIME`(8) / `COOLDOWN`(60) / `CANCEL_ON_DAMAGE` / `CANCEL_ON_MOVE` | 拠点帰還の詠唱・CD・中断条件 |
| `VISION.WARD` | `DURATION`(180) / `VISION_RANGE`(20) / `MAX_WARDS_PER_PLAYER`(3) / `COST`(75) | ワードの持続・範囲・上限・価格 |
| `RESPAWN` | `BASE_TIME`(5) / `TIME_PER_LEVEL`(0.5) / `KEEP_INVENTORY` | リスポーン時間と装備保持 |
| `KILLSTREAK` | `REQUIRED_KILLS`(3) / `BONUS_EMERALD`(2) | キルストリーク |
| `SIGNS` | `JOIN` / `LEAVE` / `SHOP` / `CHAMPION` / `START.<識別子>` | 看板位置（コマンドで自動保存）。開始看板のみ `START` 配下に識別子ごとのキーで複数保存される |
| `STAGES` | `<識別子>.TEAM_RED` / `TEAM_BLUE` / `AREA` / `MINION_WAYPOINTS` / `CREEP_SPAWNS` ほか | ステージ固有のジオメトリ（コア/防衛塔/スポーン/エリア/レーン/クリープ湧き）。`setstage` で選んだ編集ステージ配下に保存 |
| `EDIT_STAGE` | （文字列・既定 `default`） | 現在の編集ステージ識別子（`setstage` で永続化） |

!!! warning "既存サーバーを更新する場合（config自動マージなし）"
    本プラグインは `saveDefaultConfig()` のみで、**既存の `config.yml` に新セクションを自動追記しません**。旧バージョンから更新したサーバーでは `AFK` / `RECALL` / `VISION` / `NEUTRAL_CREEPS` / `SKILL_UNLOCK_LEVELS` / `SIGNS.CHAMPION`・`START` 等が無いままになります。コード側に既定値があるので落ちはしませんが、「クリープが湧かない」「2つ目以降のスキルがLv1で即解放」などの想定外動作になります。さらに本バージョンからは **ステージ固有ジオメトリが `STAGES.<識別子>.*`（既定 `EDIT_STAGE: default`）配下に保存される** 仕様になったため、旧 `MAP_SETTINGS.TEAM_RED` 等に直接書いていた座標は読まれません（既定値の参考用としては残ります）。コア・防衛塔・スポーン・エリア・レーン・クリープ湧きは `setstage` で編集ステージを選んだうえで `setcore` / `setturret` / `setspawn` / `setfield` / `setlane` / `setcamp` で再設定してください。**新規インストール、または config を退避して再生成（手動マージ）を推奨** します。

!!! note "設定変更後は必ず `/moba reload`"
    `config.yml` を編集したら `/moba reload` を実行してください。マップ座標・経済・ショップ・クリープ・看板などの変更が反映されます。

## 権限ノード

`plugin.yml` で定義されている権限は **`moba.admin` のみ** です。コード上でも `moba.admin` のみがチェックされ、`/shop` や `/moba stats` には専用の権限ノードはありません（一般プレイヤーはそのまま実行可能）。

| 権限 | 既定 | 用途 |
|---|---|---|
| `moba.admin` | OP | `stop` / `reload` / `setstage` / `setsign` / `setstartspawn` / `setlobby` / `setfield` / `setspawn` / `setcore` / `setturret` / `setlane` / `setcamp` / `setmax` / `setmin` / `settime` などの設定・管理コマンド、および `/shop` の実行に必要 |

!!! note "プレイヤー向け権限について"
    `/moba join`・`leave`・`start`・`stats`・`status` は **権限不要で全員が使えます**（`status` は読み取り専用）。`join`・`leave`・`stats` でプレイヤー名を指定して他者を対象にする操作のみOP限定です。一方 **`/shop` は `plugin.yml` で `moba.admin` 限定** のため、一般プレイヤーは `/shop` コマンドを直接使えません（ショップ看板から開きます）。

## コマンド

### プレイヤー用（全員可）

| コマンド | 説明 |
|---|---|
| `/moba join [名前]` / `leave [名前]` | ロビーに参加 / 退出する（看板と同等）。名前を指定して他者を対象にするのはOP限定 |
| `/moba start [識別子]` | ゲームを開始する（開始看板と同等・全員可）。識別子でステージ指定（省略時は編集ステージ） |
| `/moba stats [名前]` | 統計を表示する（自分の試合中はライブ成績／非試合中・名前指定は通算成績） |
| `/moba status` | 地点・看板・ステージ・ゲーム状況を確認する（読み取り専用・権限不要） |

### 管理用（`moba.admin`）

| コマンド | 説明 |
|---|---|
| `/moba stop` | ゲームを停止する |
| `/moba reload` | config.yml を再読み込み（看板・構造物も再構築） |
| `/moba setstage <識別子>` | 以降の地点設定の編集ステージを切り替える |
| `/moba setstartspawn` / `setlobby` | 初期リスポーン地点 / 待機ロビーを現在地に設定（ステージ非依存） |
| `/moba setfield <1\|2>` | ゲームエリアの角1/角2を現在地に設定（編集ステージ） |
| `/moba setspawn <red\|blue>` | チームスポーンを現在地に設定（編集ステージ） |
| `/moba setcore <RED\|BLUE>` | コア位置を現在地に設定（編集ステージ） |
| `/moba setturret <RED\|BLUE> <1-5>` | 防衛塔位置を現在地に設定（編集ステージ） |
| `/moba setlane <RED\|BLUE> <LANE_TOP\|LANE_MID\|LANE_BOT> <番号>` | ミニオン経路のウェイポイントを現在地に設定（編集ステージ） |
| `/moba setcamp <NORMAL\|BUFF\|BOSS>` | 中立クリープの湧き地点を現在地に追加（編集ステージ） |
| `/moba setmax <人数>` / `setmin <人数>` / `settime <秒>` | 最大人数 / 開始最低人数 / 最大試合時間を設定（即保存・自動リロード） |
| `/moba setsign <join\|leave\|shop\|champion\|start [識別子]\|delete>` | 視線先の看板を各種看板として登録、または `delete` で登録解除 |

## ゲームの運営

1. プレイヤーがロビー看板から参加し、`MIN_PLAYERS_TO_START` 以上集まるのを待つ。
2. 必要に応じて `/moba start` で開始（自動開始の挙動は実装状況に依存）。
3. 異常時は `/moba stop` で強制停止。

## トラブルシューティング

??? failure "プレイヤーがゲームに参加できない"
    参加導線は **ロビー看板** です。看板が正しく設置されているか、`/moba setlobby` でロビー座標が設定済みかを確認してください。

??? failure "中立クリープが湧かない"
    クリープの湧き座標は編集ステージ固有で `STAGES.<識別子>.CREEP_SPAWNS.<タイプ>` に保存されます。これが空だと湧きません。`/moba setcamp <NORMAL|BUFF|BOSS>` で湧き地点を追加するか、config に座標を記載してください。旧configから更新したサーバーは `NEUTRAL_CREEPS` の有効化設定が無い場合もあります（再生成推奨）。

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
