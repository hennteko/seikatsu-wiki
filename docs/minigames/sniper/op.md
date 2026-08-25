<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# スナイパーバトロワ ― OP・運営ガイド { .page-op #sniper-op }

スナイパーバトロワの導入・初期設定・config・権限・管理コマンドをまとめます。地点やステージは **設定ツール（ブレイズロッド）** かコマンドで登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Sniper |
| メインコマンド | `/sniper`（エイリアス `/sn`） |
| api-version | 26.1.2 |
| 作者 | henry |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Sniper/config.yml` |
| 権限ノード | `sniper.admin`（既定OP） |

## 導入手順

1. ビルドした `Sniper` の jar ファイルをサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Sniper/config.yml` が自動生成される。
3. `/sniper wand` で **設定ツール（ブレイズロッド）** を受け取り、ロビー・初期スポーン・ステージ・看板を設定する。
4. ステージのフィールド範囲・スポーン・チェストを登録し、アイテムプールを調整する。
5. `/sniper status` で設定状況を確認する。

!!! tip "設定は「設定ツール」が最速です"
    `/sniper wand` で受け取るブレイズロッドを使うと、見ているブロックへ直接セットアップできます。ツールを持って `/sniper menu`（またはツールで開くGUI）から、ステージ選択・スポーン追加・看板モード・開始/停止までGUIで操作できます。コマンド派の方は後述の個別コマンドでも同じ設定が可能です。

## セットアップ手順（設定ツール）

`/sniper wand` で受け取ったブレイズロッドを持って操作します。GUIメニューは `/sniper menu` で開きます。

- **ステージ選択** … メニュー中央の「ステージ選択」を左クリックで次、右クリックで前へ。フィールド角を設定すると新ステージが自動作成されます。
- **初期スポーン** … 途中抜け・切断時の戻り先。現在地を登録。
- **ロビー地点** … 受付エリア。現在地を登録。
- **スポーン地点** … 選択中ステージのゲーム開始位置。現在地を複数登録できます。
- **フィールド範囲** … ツールで角1（左クリック）・角2（右クリック）を指定。ワールドボーダーの初期サイズはこの範囲から自動算出されます。
- **チェスト登録** … ツールでチェストを右クリックして登録／解除。
- **看板モード** … 参加／離脱／開始（ステージ指定）／登録解除の各モードをONにして、看板をツールで右クリックすると設置・解除できます。

## セットアップ手順（コマンド）

コマンドで設定する場合は以下を使います。地点系は **実行した位置** が保存され、看板・チェスト系は対象ブロックを **見ながら** 実行します。

```text title="設定ツール（ブレイズロッド）を受け取る"
/sniper wand
```

```text title="設定GUIメニューを開く"
/sniper menu
```

```text title="初期スポーン地点（その場に立って実行）"
/sniper setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/sniper setlobby
```

```text title="ステージのフィールド角1（その場に立って実行・ステージ自動作成）"
/sniper setfield stage1 1
```

```text title="ステージのフィールド角2（その場に立って実行）"
/sniper setfield stage1 2
```

```text title="ゲームスポーン地点を追加（その場に立って実行・複数可）"
/sniper setspawn stage1
```

```text title="指定番号のスポーン地点を削除"
/sniper delspawn stage1 1
```

```text title="チェストを登録／解除（チェストを見ながら実行・複数可）"
/sniper setchest stage1
```

```text title="参加看板（看板を見ながら実行）"
/sniper setsign join
```

```text title="離脱看板（看板を見ながら実行）"
/sniper setsign leave
```

```text title="開始看板（看板を見ながら実行・ステージ指定）"
/sniper setsign start stage1
```

```text title="視線先の看板の登録を解除（看板を見ながら実行・表示もクリア）"
/sniper setsign delete
```

```text title="ステージ一覧（フィールド・スポーン・チェストの登録状況）"
/sniper stagelist
```

```text title="指定ステージを削除"
/sniper delstage stage1
```

!!! note "ステージ名は任意"
    上記の `stage1` は例です。`/sniper setfield <好きな名前> 1` で任意名のステージが自動作成されます。複数ステージを作り、開始看板や `/sniper start <ステージ>` で切り替えられます。

## 人数・時間・ボーダーの設定

```text title="制限時間を設定（秒・0=無制限）"
/sniper settime 600
```

```text title="最低人数を設定（1以上）"
/sniper setmin 2
```

```text title="最大参加人数を設定"
/sniper setmax 16
```

```text title="ボーダー縮小間隔を設定（秒）"
/sniper setborder interval 60
```

```text title="ボーダー1回の縮小量を設定（ブロック）"
/sniper setborder amount 50
```

```text title="ボーダーの最小サイズを設定（ブロック）"
/sniper setborder min 20
```

```text title="ボーダー縮小演出の秒数を設定"
/sniper setborder speed 5
```

```text title="ボーダー外での1秒あたりダメージを設定"
/sniper setborder damage 0.5
```

!!! note "ボーダーの初期サイズは自動算出です"
    ワールドボーダーの **初期サイズはフィールド範囲（`setfield 1`/`2`）から自動で決まります**。`config.yml` の `border.initial-size` を `0` にしておくと自動算出、値を入れると固定サイズになります。`border.interval` 秒ごとに `border.amount` ブロックずつ（`border.speed` 秒かけて）縮小し、`border.min-size` で停止します。

## チェストのアイテムプール

チェストに入るアイテムは重み付きの抽選プールで管理します。手に持ったアイテムをそのまま登録できます（ポーション効果なども保持）。

```text title="手持ちアイテムを抽選プールに追加（重み・最小/最大個数を任意指定）"
/sniper setitem 10 1 2
```

```text title="抽選プールの一覧と出現割合(%)を表示"
/sniper itemlist
```

```text title="指定番号のアイテムの重みを変更"
/sniper setweight 1 40
```

```text title="指定番号のアイテムをプールから削除"
/sniper removeitem 1
```

!!! note "アイテムプールの動作"
    各チェストには、開始時に `chest.items-min`〜`chest.items-max`（既定3〜6）種のアイテムが補充されます。アイテムは **重み（`weight`）に応じて抽選** され、それぞれ `min-amount`〜`max-amount` の個数で配置されます。既定では弾丸（矢）・回復／スピード／ジャンプの各ポーション・煙幕の5種が初期投入されています。

## config.yml 設定項目

地点・スポーン・チェスト・看板・ステージはコマンドまたは設定ツールで自動保存されます（手動編集は基本不要）。主な調整項目は次のとおりです。

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 2 | 最低人数（0人は必ず拒否。1にすると1人でも開始可） |
| `max-players` | 16 | 最大参加人数 |
| `time-limit` | 600 | 制限時間（秒）。0=無制限。時間切れ時はキル数最多が勝利 |
| `countdown-seconds` | 5 | 開始カウントダウン（秒） |
| `result-seconds` | 10 | 結果発表の表示秒数 |

### 武器（スナイパーライフル）

| キー | 既定値 | 説明 |
|---|---|---|
| `weapon.range` | 150.0 | 射程（ブロック） |
| `weapon.ray-size` | 0.3 | 命中判定の太さ |
| `weapon.body-damage` | 2.0 | 胴体1発のダメージ（最大体力6.0→3発でキル） |
| `weapon.headshot-instant` | true | ヘッドショット即死 |
| `weapon.reload-ms` | 2000 | リロード時間（ミリ秒） |
| `weapon.use-item-cooldown` | false | バニラのクールダウン表示を出す（trueだとリロード中スコープ不可） |
| `weapon.shot-volume` | 4.0 | 銃声の音量（大きいほど遠くまで聞こえる。4.0≒64ブロック） |
| `weapon.trail` | true | 弾道パーティクル |
| `weapon.max-arrows` | 32 | 弾（矢）の最大所持数 |

### 体力・環境

| キー | 既定値 | 説明 |
|---|---|---|
| `health.max` | 6.0 | 最大体力（6.0=ハート3個） |
| `health.kill-heal-full` | true | キル時に体力全回復 |
| `health.natural-regen` | false | 自然回復（false推奨。回復ポーションは別途有効） |
| `fall-damage` | true | 落下ダメージ |
| `jump-potion-no-fall` | true | ジャンプポーション中は落下ダメージ無効 |
| `melee-damage` | false | 近接攻撃を許可（false＝純狙撃戦） |
| `keep-food` | true | 満腹度を減らさない |

### 安全地帯（ワールドボーダー）

| キー | 既定値 | 説明 |
|---|---|---|
| `border.initial-size` | 0 | 0=`setfield` の範囲から自動算出。値を入れると固定 |
| `border.interval` | 60 | 縮小間隔（秒） |
| `border.amount` | 50 | 1回の縮小量（ブロック） |
| `border.min-size` | 20 | これ以下には縮まない停止サイズ |
| `border.speed` | 5 | 縮小にかける秒数（壁が動く演出） |
| `border.outside-damage` | 0.5 | 圏外での1秒あたりダメージ |
| `border.warn-before` | `[30, 10]` | 縮小の予告タイミング（秒前） |

### チェスト・煙幕・観戦

| キー | 既定値 | 説明 |
|---|---|---|
| `chest.items-min` | 3 | 1チェストに入る種類数の最小 |
| `chest.items-max` | 6 | 1チェストに入る種類数の最大 |
| `chest.refill-interval` | 0 | 再補充間隔（秒）。0=無効 |
| `smoke.radius` | 4.0 | 煙幕の半径（ブロック） |
| `smoke.duration` | 10 | 煙幕の持続時間（秒） |
| `smoke.density` | 25 | 1tickあたりのパーティクル数 |
| `smoke.blindness` | false | trueで煙の中のプレイヤーに盲目を付与 |
| `smoke.block-line-of-fire` | true | 煙越しの狙撃を遮断する |
| `spectator.chat-isolate` | true | 観戦者チャットを観戦者間のみに制限 |
| `pending-expire-days` | 30 | 復元待ちデータ（players.yml）の保持日数 |
| `messages.prefix` | `[スナイパー]` | メッセージの接頭辞 |

!!! note "自動生成される領域（手動編集不要）"
    `stages`（フィールド・スポーン・チェスト）、`signs`（看板）、`default-spawn` / `lobby-spawn` はコマンドまたは設定ツールで自動生成・自動保存されます。手動編集は不要です。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/sniper wand` | 設定ツール（ブレイズロッド）を受け取る |
| `/sniper menu` | 設定GUIメニューを開く |
| `/sniper setstartspawn` | 初期スポーン地点を設定 |
| `/sniper setlobby` | 受付ロビー地点を設定 |
| `/sniper setspawn <ステージ>` | ゲームスポーン地点を追加 |
| `/sniper delspawn <ステージ> <番号>` | 指定スポーン地点を削除 |
| `/sniper setfield <ステージ> <1\|2>` | フィールドの角1／角2を設定（ステージ自動作成） |
| `/sniper setchest <ステージ>` | 見ているチェストを登録／解除 |
| `/sniper setsign <join\|leave\|start <ステージ>\|delete>` | 参加／離脱／開始／解除看板を設定 |
| `/sniper settime <秒>` | 制限時間を設定（0=無制限） |
| `/sniper setmin <人数>` | 最低人数を設定 |
| `/sniper setmax <人数>` | 最大参加人数を設定 |
| `/sniper setborder <interval\|amount\|min\|speed\|damage> <値>` | ボーダー各値を設定 |
| `/sniper setitem [重み] [最小] [最大]` | 手持ちアイテムを抽選プールに追加 |
| `/sniper itemlist` | 抽選プールの一覧と出現割合を表示 |
| `/sniper setweight <番号> <重み>` | 指定アイテムの重みを変更 |
| `/sniper removeitem <番号>` | 指定アイテムを削除 |
| `/sniper stagelist` | ステージ一覧を表示 |
| `/sniper delstage <ステージ>` | 指定ステージを削除 |
| `/sniper stop` | ゲームを停止する |
| `/sniper reset` | 在籍・試合データを全消去（緊急リセット） |
| `/sniper reload` | config を再読み込み |
| `/sniper status` | 設定状況・現在の状況を確認（全員可） |

## 看板について

`/sniper setsign <join|leave|start>` で登録した看板は、プラグインが自動的に装飾します。

- **参加看板** … `[スナイパー]` / `参加` と、`現在人数 / 最大人数`・`待機中/進行中` を表示。クリックでロビー参加。
- **離脱看板** … `[スナイパー]` / `離脱`。クリックで離脱。
- **開始看板** … `[スナイパー]` / `開始` と対象ステージ名・`待機中/進行中` を表示。クリックでそのステージのゲーム開始（**開始は全員可**）。

登録を解除したいときは、その看板を見ながら `/sniper setsign delete`（または設定ツールの登録解除モード）を実行すると config から削除され、看板の表示もクリアされます。

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `sniper.admin` | OP | セットアップ・看板設置・強制停止など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/sniper join`・`/sniper leave`・`/sniper start <ステージ>`・`/sniper status` は権限チェックがなく、全プレイヤーが実行できます。`/sniper start` は看板クリックと同じく誰でもゲームを開始できる点に注意してください。それ以外のサブコマンド（wand / menu / set系 / stop / reset / reload など）は `sniper.admin`（既定OP）が必要です。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/sniper status` と `/sniper stagelist` で設定を確認してください。対象ステージにフィールド範囲が未設定だと開始できません。また、ロビーに最低人数（既定2人）以上いる必要があります。スポーン地点が未設定の場合はフィールド内のランダム地点で代用されます。

??? failure "プレイヤーが「撃てない」と言っている"
    操作方法を案内してください。望遠鏡を **右クリック長押しで構え**、構えたまま **しゃがみ（Shift）を押した瞬間**に発砲します。弾（矢）が0だと撃てないので、チェストで補充が必要です。1発ごとに約2秒（`weapon.reload-ms`）のリロードがあります。

??? failure "チェストにアイテムが入らない"
    抽選プールが空、またはチェストが未登録の可能性があります。`/sniper itemlist` でプールを、`/sniper stagelist` でチェスト登録数を確認してください。登録した座標が実際にチェストでないと補充されません。

??? failure "ワールドボーダーが縮小したまま残った／試合が正常に終わらない"
    `/sniper reset` を実行してください。在籍・試合データを全消去し、残留物も含めてリセットします。

??? failure "看板をクリックしても参加・退出できない"
    `/sniper status` で看板が登録済みか確認してください。看板の位置を変更・破壊した場合は再登録が必要です。

??? failure "config を編集したのに反映されない"
    `/sniper reload` で config を再読み込みしてください。人数・時間・武器・ボーダーなどの数値項目に反映されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← スナイパーバトロワ 概要へ](index.md){ .md-button }
