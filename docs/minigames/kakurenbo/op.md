<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Kakurenbo（かくれんぼ）― OP・運営ガイド { .page-op #kakurenbo-op }

かくれんぼの導入・スポーン/フィールド設定・看板・擬態ブロック・config・権限をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Kakurenbo |
| メインコマンド | `/kakurenbo`（エイリアスなし） |
| 依存プラグイン | なし |
| api-version | 26.1.2 |
| 設定ファイル | `plugins/Kakurenbo/config.yml`（設定・メッセージ・擬態ブロック・看板テキスト） |
| 地点ファイル | `plugins/Kakurenbo/locations.yml`（スポーン・看板位置。コマンドで自動保存） |

!!! info "仕組み"
    隠れる側は透明化し、自分に追従するフェイクブロック（クライアント側の表示ブロック）でブロックに擬態します。擬態ブロックは常に升目（プレイヤーの足元マス）にスナップしており、静止していれば同化、移動するとマス単位で追従します。鬼は配られる **雪玉を化けた隠れ側に当てて** 捕獲し、捕まった側は猶予後に鬼へ転向します（増え鬼）。

## 導入手順

1. ビルドした `Kakurenbo` の jar を `plugins/` に配置する。
2. サーバーを起動すると `plugins/Kakurenbo/config.yml` が生成される（地点は `locations.yml` に自動保存）。
3. 後述の手順でスポーン・看板・擬態ブロックを設定する。
4. `/kakurenbo status` で設定状況を確認する。

## セットアップ手順

OP権限（`kakurenbo.admin`）で、設定したい場所に **立って／対象を見て** 実行します。地点は `locations.yml` に即保存されます。

```text title="① ロビー地点を現在地に設定"
/kakurenbo setlobby
```
```text title="② 初期スポーン（離脱・終了時の戻り先）を現在地に設定"
/kakurenbo setstartspawn
```
```text title="③ 鬼スポーンを現在地に設定"
/kakurenbo setspawn seeker
```
```text title="④ 隠れ側スポーンを現在地に設定（任意）"
/kakurenbo setspawn
```
```text title="⑤ 鬼の待機ステージを現在地に設定（任意・隠れ時間中の隔離地点）"
/kakurenbo setstage
```
```text title="⑥ フィールド範囲の頂点1を現在地に設定（任意）"
/kakurenbo setfield 1
```
```text title="⑥ フィールド範囲の頂点2を現在地に設定（任意）"
/kakurenbo setfield 2
```
```text title="設定状況を確認"
/kakurenbo status
```

!!! tip "ロビー・スポーンの配置に注意"
    隠れ側スポーン（`setspawn`）が未設定の場合、隠れ側は **ロビー位置からそのまま擬態を開始** します。ロビーがフィールドから離れていると擬態がフィールド外で始まってしまうため、**ロビーをフィールド内に置く**か、`setspawn`（隠れ側スポーン）を設定してください。フィールド範囲（`setfield 1`/`2`）を設定すると、その範囲外へは移動できなくなります（未設定なら制限なし）。

!!! note "鬼の待機ステージ（setstage）"
    隠れ時間中、鬼は **待機ステージ** に隔離され足止めされます。`setstage` が未設定の場合は鬼スポーン（`setspawn seeker`）の位置で待機し、探索フェーズ開始時に鬼スポーンへ解き放たれます。隠れ側に化け場所を覗かれたくない場合は、フィールドから離れた地点を `setstage` に設定してください。

### 数値設定（コマンドで即保存）

人数や各タイマーは config を直接編集しなくてもコマンドで変更でき、即 `config.yml` に保存されます。

```text title="最大参加人数を設定"
/kakurenbo setmax <人数>
```
```text title="開始に必要な最小人数を設定"
/kakurenbo setmin <人数>
```
```text title="制限時間（秒）を設定"
/kakurenbo settime <秒>
```
```text title="隠れ時間（秒）を設定"
/kakurenbo setdelay <秒>
```

### 擬態ブロックパレットの登録

隠れる側が化けられるブロックの一覧を登録します。化けさせたいブロックを **手に持って** 実行します（config の `blocks` に保存されます）。

```text title="手持ちブロックを擬態パレットに追加"
/kakurenbo addblock
```
```text title="手持ちブロックを擬態パレットから削除"
/kakurenbo removeblock
```

既定パレットは `STONE` / `OAK_LOG` / `GRASS_BLOCK` / `COBBLESTONE` / `OAK_PLANKS` です。マップに合うブロックを登録してください（未登録だと隠れ側が化けられません）。

### 看板の設置

参加・離脱・開始・ブロック選択・鬼立候補の **5種** の看板を登録できます。看板を見ながら（5ブロック以内）以下を実行します。

```text title="参加看板を登録"
/kakurenbo setsign join
```
```text title="離脱看板を登録"
/kakurenbo setsign leave
```
```text title="開始看板を登録"
/kakurenbo setsign start
```
```text title="擬態ブロック選択看板を登録（隠れ側が右クリックでGUIを開く）"
/kakurenbo setsign block
```
```text title="鬼立候補看板を登録（クリックで鬼に立候補・任意）"
/kakurenbo setsign oni
```
```text title="登録済み看板の登録を解除（視線先の看板を自動判別）"
/kakurenbo setsign delete
```

テキストはプラグインが自動で書き込み（config の `sign.*` から描画）、位置は `locations.yml` に保存されます。クリック判定は保存位置との一致で行います。

!!! tip "鬼立候補看板（setsign oni）"
    ロビー参加者がクリックすると **鬼に立候補**（もう一度クリックで取り消し）。看板の4行目に現在の立候補人数（`%count%`）が表示されます。開始時は立候補者から優先的に鬼を抽選し、不足分はランダム指名で補います。未設置でも従来どおり全員ランダムで鬼が決まります。

## config.yml 主要項目

### game セクション

| キー | 既定値 | 説明 |
|---|---|---|
| `game.min-players` | 2 | 開始に必要な最小人数 |
| `game.max-players` | 16 | 最大参加人数 |
| `game.countdown-time` | 10 | 開始前カウントダウン（秒） |
| `game.hiding-time` | 30 | 隠れ時間（この間 鬼は足止め）（秒） |
| `game.match-duration` | 300 | 制限時間（秒） |
| `game.conversion-delay` | 3 | 捕獲から鬼へ転向するまでの猶予（秒） |
| `game.seeker-ratio` | 8 | 何人につき鬼1人か（切り上げ・最低1人・最大 n-1） |
| `game.snap-interval-ticks` | 2 | 擬態ブロックの追従（升目スナップ）判定を行う間隔（tick） |
| `game.snap-idle-ms` | 300 | 予約キー（現行ロジックでは未使用。擬態ブロックは常時升目にスナップされます） |
| `game.result-seconds` | 10 | 結果発表を表示してからロビーへ戻すまでの秒数 |

!!! tip "数値はコマンドでも変更できます"
    `min-players` / `max-players` / `match-duration` / `hiding-time` は、それぞれ `/kakurenbo setmin` / `setmax` / `settime` / `setdelay` でゲーム内から変更でき、即 `config.yml` に保存されます。

### disguise セクション（隠れ側の擬態アイテム）

| キー | 既定値 | 説明 |
|---|---|---|
| `disguise.item-material` | `STICK` | 擬態ステッキのアイテム種別 |
| `disguise.item-name` | `&b擬態ステッキ …` | 擬態ステッキの表示名 |
| `disguise.release-hold-ticks` | 20 | スニーク長押しで擬態解除に必要な tick（20=約1秒） |

### seeker セクション（鬼の捕獲アイテム）

| キー | 既定値 | 説明 |
|---|---|---|
| `seeker.snowball-amount` | 16 | 鬼に配る雪玉の数（投擲後に自動補充） |
| `seeker.snowball-name` | `&f雪玉 …` | 雪玉の表示名 |

### firework セクション（隠れ側の花火アイテム）

隠れる側に配られる **花火**（ホットバー2番目・無限使用）の設定です。擬態中にスロットを選ぶと打ち上がり、位置の自己申告・演出に使えます。

| キー | 既定値 | 説明 |
|---|---|---|
| `firework.item-material` | `FIREWORK_ROCKET` | 花火アイテムの種別 |
| `firework.item-name` | `&d花火 …` | 花火アイテムの表示名 |
| `firework.cooldown-seconds` | 3 | 手動打ち上げの連打防止クールダウン（秒・0で無効） |
| `firework.forced-interval-seconds` | 60 | 鬼が動き出してから、隠れ側全員の花火を強制で一斉に打ち上げる間隔（秒・0で無効） |
| `firework.power` | 1 | 打ち上げの高さ（飛行時間 0〜3） |
| `firework.colors` | 10色 | 打ち上げ色のリスト（毎回ランダム）。染料色名または `#RRGGBB` |
| `firework.types` | `BALL_LARGE` ほか | 形のリスト（ランダム）: `BALL` / `BALL_LARGE` / `STAR` / `BURST` / `CREEPER` |
| `firework.flicker` | `true` | きらめき効果 |
| `firework.trail` | `true` | 尾を引く効果 |
| `firework.sound` | `entity.firework_rocket.launch` | 打ち上げ時に追加で鳴らす音（`""`で追加音なし・バニラ破裂音は常に鳴る） |
| `firework.sound-volume` | 3.0 | 追加音の音量（3.0でおよそ48ブロック先まで） |
| `firework.sound-pitch` | 1.0 | 追加音のピッチ |

### reveal セクション（終盤の擬態ブロック公開）

残り時間が一定以下になると、隠れ側の擬態ブロックを **発光・パーティクル** で鬼にわかるようにして膠着を防ぎます。

| キー | 既定値 | 説明 |
|---|---|---|
| `reveal.seconds-left` | 60 | 残り何秒から擬態ブロックを光らせるか（0で無効） |
| `reveal.glow-color` | `#FF5555` | 発光色（Java版の輪郭・パーティクル色）。染料色名または `#RRGGBB` |
| `reveal.particle` | `true` | 擬態ブロック上にパーティクルを出す（統合版は発光が見えないための代替） |

### blocks / messages / sign / gui

- `blocks` … 擬態パレット（`addblock` / `removeblock` で編集・自動保存）。
- `messages` … 各種通知文（`&` カラーコード対応）。`prefix` ほか役割通知・捕獲・残り時間・鬼立候補・花火（`firework-cooldown` / `firework-forced`）・終盤公開（`reveal-start`）・ロビー参加/退出ブロードキャストなど。
- `sign.{lobby,leave,start,block,oni}` … 各看板の表示文。参加看板の4行目は `%current%/%max%` で人数、鬼立候補看板の4行目は `%count%` で立候補人数を表示。
- `gui.block-select-title` … ブロック選択GUIのタイトル。

!!! note "config は不足キーを自動追記します"
    起動時および `/kakurenbo reload` 時に、`config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更せず、更新があった場合は `config.yml.bak` を保存）。このため `firework` / `reveal` などの新セクションも、既存サーバーで自動的に補完されます。座標・看板などのデータ領域（`locations.yml`）は補完対象外で、コマンドで保存されます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `kakurenbo.admin` | OP | setsign系・setlobby・setspawn系・setstage・setfield・setmax・setmin・settime・setdelay・addblock・removeblock・stop・reload・forceunlock・join/leave の他プレイヤー指定 |
| `kakurenbo.play` | 全員 | ゲームへの参加（`join`） |

!!! note "join / leave / start / status は権限不要で全員可"
    `/kakurenbo join`・`leave`・`start`・`status` は権限チェックがなく全プレイヤーが使えます（看板と同等）。ただし `join <名前>` / `leave <名前>` のように **他プレイヤーを指定** する場合のみ `kakurenbo.admin` が必要です。設定・管理コマンドも `kakurenbo.admin` が必要です。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/kakurenbo join [名前]` / `leave [名前]` / `start` | 全員（名前指定は admin） | 参加 / 退出 / 開始（看板と同等。名前指定で他プレイヤーを操作） |
| `/kakurenbo status` | 全員 | 設定状況・進行状態を表示（読み取り専用） |
| `/kakurenbo setlobby` / `setstartspawn` | `kakurenbo.admin` | ロビー / 初期スポーンを設定 |
| `/kakurenbo setspawn [seeker]` | `kakurenbo.admin` | 隠れ側スポーン（引数なし）／鬼スポーン（`seeker`）を設定 |
| `/kakurenbo setstage` | `kakurenbo.admin` | 鬼の待機ステージ（隠れ時間中の隔離地点）を設定 |
| `/kakurenbo setfield <1\|2>` | `kakurenbo.admin` | フィールド範囲の頂点を設定 |
| `/kakurenbo setsign <join\|leave\|start\|block\|oni\|delete>` | `kakurenbo.admin` | 視線先の看板を各用途で登録 / 解除 |
| `/kakurenbo setmax` / `setmin <人数>` | `kakurenbo.admin` | 最大 / 最小人数を設定 |
| `/kakurenbo settime <秒>` / `setdelay <秒>` | `kakurenbo.admin` | 制限時間 / 隠れ時間を設定 |
| `/kakurenbo addblock` / `removeblock` | `kakurenbo.admin` | 手持ちブロックを擬態パレットに追加 / 削除 |
| `/kakurenbo stop` | `kakurenbo.admin` | ゲームを強制終了 |
| `/kakurenbo reload` | `kakurenbo.admin` | config を再読み込み |
| `/kakurenbo forceunlock <プレイヤー>` | `kakurenbo.admin` | 他ゲーム参加中フラグが異常に残った場合の強制解除（コンソール可） |

## トラブルシューティング

??? failure "ゲームが開始できない"
    参加者が `game.min-players`（既定2人）以上か `/kakurenbo status` で確認してください。

??? failure "隠れ側が化けられない／「擬態ブロックが登録されていません」と出る"
    擬態パレットが空です。化けさせたいブロックを手に持って `/kakurenbo addblock` で登録してください（既定で5種登録済み）。

??? failure "隠れ側がフィールド外で始まってしまう"
    隠れ側スポーン未設定だとロビー位置から擬態が始まります。`/kakurenbo setspawn`（隠れ側スポーン）を設定するか、ロビーをフィールド内に配置してください。

??? failure "看板をクリックしても反応しない"
    看板は `/kakurenbo setsign <種別>` で登録した位置で判定されます。登録済みか `/kakurenbo status` で確認してください（手書きでは機能しません）。

---

## 全ゲーム共通の改修（2026-09）

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ゲーム名】名前」になり、離脱で元に戻ります（表示名は config の `display-name`）。
- **参加/離脱の全体告知** … 参加・離脱時にサーバー全体へ「【ゲーム名】名前 が参加しました (N人)」等を通知します。
- **同時参加は1ゲームまで** … 他ゲームに参加中は参加が拒否されます（「【○○】に参加中です」）。異常で参加ロックが残った場合はOPが `/<コマンド> forceunlock <プレイヤー>` で解除できます。
- **退避データのファイル保存** … ロビー入場時に退避した所持品を `plugins/<プラグイン>/vault/<UUID>.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します（退避・復元は各1回、試合終了時は復元しません）。

### 追加された config キー

| キー | 説明 |
|---|---|
| `display-name` | ゲーム表示名（「【…】」の中身） |
| `messages.join-broadcast` | 参加の全体告知文 |
| `messages.leave-broadcast` | 離脱の全体告知文 |
| `messages.already-in-other-game` | 他ゲーム参加中に拒否したときの文言 |

!!! note "config は自動で追記されるようになりました"
    起動時（`reload` 対応プラグインは reload 時も）に、`config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は `config.yml.bak` を保存）。座標・看板・会場・ステージなどのデータ領域（`locations.yml`）は補完対象外です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Kakurenbo 概要へ](index.md){ .md-button }
