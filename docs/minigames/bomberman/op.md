<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Bomberman（ボンバーマン）― OP・運営ガイド { .page-op #bomberman-op }

ボンバーマンの導入・フィールド設定・看板・config・権限・管理コマンド、ソロモード（CPU対戦・パズル）の設定をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Bomberman |
| メインコマンド | `/bomberman`（エイリアス `/bm`・`/bomber`） |
| 依存プラグイン | なし（Geyser / Floodgate は任意） |
| api-version | 26.1.2 |
| 設定ファイル | `plugins/Bomberman/config.yml`・`plugins/Bomberman/puzzles.yml` |
| データファイル | `lobby-inventory.yml`（持ち物の退避）・`solo-records.yml`（ソロの記録） |

!!! info "仕組み"
    `setfield` の2点で囲んだ直方体にゲーム開始時に **格子状フィールドを自動生成**（外周と偶数マスに硬ブロック、残りに軟ブロックを密度指定で配置）し、終了時に元の地形へ **完全復元** します。爆弾は重力無効の TNT 落下ブロック（FallingBlock）で表示し（統合版／Geyserでも表示されるようにするため。BlockDisplay は使用しません）、十字判定はプラグインが自前で計算します。

## 導入手順

1. ビルドした `Bomberman` の jar を `plugins/` に配置する。
2. サーバーを起動すると `plugins/Bomberman/config.yml` と `puzzles.yml` が生成される。
3. 後述の手順でフィールド・スポーン・看板を設定する。
4. `/bomberman status` で設定状況を確認する。

## セットアップ手順

OP権限（`bomberman.admin`）で、設定したい場所に **立って／対象を見て** 実行します。各設定は即 `config.yml` に保存されます。

```text title="① 受付ロビーを現在地に設定"
/bomberman setlobby
```
```text title="② 初期スポーン（離脱・終了時の戻り先）を現在地に設定"
/bomberman setstartspawn
```
```text title="③ フィールド範囲の角1を現在地に設定"
/bomberman setfield 1
```
```text title="③ フィールド範囲の角2を現在地に設定"
/bomberman setfield 2
```
```text title="④ プレイヤー開始スポーンを現在地に追加（参加人数ぶん 1,2,3… と設定）"
/bomberman setspawn <番号>
```
```text title="（任意）視線先のブロックを軟ブロック種別として登録"
/bomberman setsoftblock
```
```text title="設定状況を確認"
/bomberman status
```

!!! tip "フィールドとスポーンの注意"
    `setfield 1`/`2` の2点で直方体（X・Z範囲）を決め、下から床→障害物2段が生成されます。**スポーンはフィールドの升目上の正しい高さ（足元の段）に立って** `setspawn <番号>` で設定してください。スポーン数が実質の最大参加人数になります（`maxplayers` と少ない方が上限）。外周と偶数マスは固定の硬ブロックになります。

### 看板の設置

参加・離脱・開始・ソロの4種の看板を設置できます。看板を見ながら（6ブロック以内）以下を実行します。

```text title="参加看板を登録"
/bomberman setsign join
```
```text title="離脱看板を登録"
/bomberman setsign leave
```
```text title="開始看板を登録"
/bomberman setsign start
```
```text title="開始看板を登録（任意：2行目に表示する識別子ラベルを付与）"
/bomberman setsign start <識別子>
```
```text title="ソロ看板を登録（クリックでソロメニュー）"
/bomberman setsign solo
```
```text title="視線先の看板の登録を解除"
/bomberman setsign delete
```
```text title="視線先の看板の登録を解除（互換エイリアス）"
/bomberman removesign
```

テキストはプラグインが自動で書き込みます（1行目 `[ボンバーマン]`）。ソロ看板の4行目にはアリーナの状況（空き／挑戦中: 名前／通常戦の試合中）が出ます。位置は config の `signs` に保存され、再起動後も有効です。看板の破壊は `bomberman.admin` のみ可能で、破壊すると登録も解除されます。

!!! note "看板の登録解除"
    解除は **視線先（6ブロック以内）の看板を自動判別** して行います。`/bomberman setsign delete` が標準で、`/bomberman removesign` は同じ動作の互換エイリアスです。開始看板に付けた識別子ラベルは単一ステージのため **表示用**（看板2行目に表示）で、ステージ切替などには影響しません。

### ソロモードの設定

通常戦の設定（ロビー・初期スポーン・フィールド）がそろっていれば、追加で必要なのは **ソロ看板** だけです（看板を置かず `/bomberman solo` だけで遊ぶこともできます）。

| モード | 追加で必要なもの |
|---|---|
| CPU対戦 | スポーン地点 **2か所以上**（1番＝プレイヤー、2番以降＝CPU）。CPU の数より少ないときは、CPU を減らして始めます（メニューに「スポーン不足で○体」と出ます） |
| パズル | フィールドが盤面より大きいこと。初期のステージは **7×7・9×9・11×11**（外周込み）。**15×15 のフィールドなら全ステージ遊べます**。入らないステージはメニューで遊べない表示になります |

- パズルの盤面はフィールドの **中央** に置かれ、盤面の外は硬ブロックで埋まります（終わると元の地形に戻ります）。
- 「つよい」で CPU 3体にするには、スポーンが4か所必要です。四隅に置くのがおすすめです。

!!! note "ソロを始められない場合"
    ソロメニューのボタンがバリアになり、理由が表示されます（ソロ無効／ロビー・初期スポーン・フィールド未設定／スポーン不足／フィールドが小さい／アリーナ使用中）。

### ミニゲームメニューへの登録（ソロ）

本人として実行するボタンなら：

```text title="ソロメニューを開く（本人として実行）"
/bomberman solo
```

コンソールとして実行するボタンなら：

```text title="指定した人にソロメニューを開かせる（コンソール・コマンドブロック・OP）"
/bomberman solo <プレイヤー>
```

`/bomberman solo <プレイヤー> cpu [難易度]`・`/bomberman solo <プレイヤー> puzzle <ステージ>` にすると、メニューを通さずすぐ始めさせます。`@p` などのセレクターも使えます。

## ゲーム設定コマンド（`bomberman.admin`）

各設定は実行直後に config へ保存されます。

```text title="軟ブロックの配置密度（%・0〜100）"
/bomberman setratio <0-100>
```
```text title="爆弾の起爆秒数（>0・小数可）"
/bomberman setbombtimer <秒>
```
```text title="初期火力（1〜50）"
/bomberman setpower <数字>
```
```text title="初期の同時設置数（1〜50）"
/bomberman setbombs <数字>
```
```text title="サドンデス開始秒数（0〜100000・0で無効）"
/bomberman setsuddendeath <秒>
```
```text title="最大参加人数（2〜64）"
/bomberman maxplayers <数字>
```
```text title="開始に必要な最小人数（1〜64・1で一人練習モード可）"
/bomberman setminplayers <数字>
```

!!! tip "一人練習モード"
    `setminplayers 1` にすると、通常参加1人でも `start` できます。この場合は通常の勝敗判定を行わず、本人が全滅（爆風で脱落）するまで自由に爆弾を試せる **練習モード** になります。終了は `/bomberman stop` で行います。記録が残る1人用の遊びは **ソロモード** を使ってください。

!!! note "爆弾の秒数・初期火力はソロにも効きます"
    `bomb-timer` はソロ（CPU対戦・パズル）にも使われます。`default-power`・`default-bombs` は CPU対戦のプレイヤーの初期値です（パズルはステージの値を使います）。パズルは「既定の 2.5 秒」で解けることを確認しています。

## config.yml 主要項目

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 8 | 最大参加人数（通常参加の人数。ソロで入った人は数えない） |
| `settings.min-players` | 2 | 開始に必要な最小人数（1で一人練習モード可） |
| `settings.bomb-timer` | 2.5 | 爆弾の起爆秒数 |
| `settings.default-power` | 2 | 初期火力 |
| `settings.default-bombs` | 1 | 初期の同時設置数 |
| `settings.sudden-death-time` | 120 | サドンデス開始秒（0で無効） |
| `settings.soft-block-ratio` | 75 | 軟ブロックの配置密度（%） |
| `settings.item-drop-chance` | 35 | 軟ブロック破壊時のパワーアップ出現率（%） |
| `blocks.soft` | TERRACOTTA | 軟ブロックの種類 |
| `blocks.floor` | SMOOTH_STONE | 床ブロックの種類 |
| `powerups.<種別>.weight` | fire30 / bomb30 / speed25 / pierce8 / full7 | パワーアップの抽選重み |

- 硬ブロックは `IRON_BLOCK` 固定（config項目なし）。
- `locations`（ロビー/スポーン/フィールド）と `signs` はコマンド実行時に自動生成・保存されます。手動編集は不要です。

### ソロ（`solo.*`）

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | true | false でソロを無効にする |
| `solo.countdown` | 3 | 選んでから始まるまでの秒数 |
| `solo.result-seconds` | 3 | 結果を出してからロビーへ戻すまでの秒数 |
| `solo.menu-delay` | 3 | ロビーへ戻ってからソロメニューを開くまでの秒数 |
| `solo.broadcast-result` | true | クリアをサーバー全体に1行で流す |
| `solo.ranking-size` | 10 | `/bomberman ranking solo` の表示人数 |
| `solo.cpu.sudden-death` | true | CPU対戦でもサドンデスを使う（開始秒は `settings.sudden-death-time`） |
| `solo.cpu.default-level` | normal | 難易度を省略したとき・ランキングの既定 |
| `solo.cpu.levels.<id>.*` | easy / normal / hard | CPU の難易度（下表）。並び順でメニューに並ぶ（最大8） |
| `solo.puzzle.time-limit` | 180 | パズルの制限時間（秒。0で無制限） |

| `levels.<id>` のキー | よわい | ふつう | つよい | 説明 |
|---|---|---|---|---|
| `display` | よわい | ふつう | つよい | 表示名 |
| `icon` | LIME_CONCRETE | YELLOW_CONCRETE | RED_CONCRETE | メニューのアイコン |
| `count` | 1 | 2 | 3 | CPU の数（最大4） |
| `move-ticks` | 8 | 6 | 5 | 1マス進む tick（小さいほど速い。プレイヤーの歩きは約5） |
| `think-ticks` | 12 | 4 | 0 | 1マスごとに立ち止まる tick |
| `aggression` | 30 | 60 | 90 | 相手を爆風に入れられる位置でボムを置く確率（%） |
| `dig` | 60 | 80 | 90 | 軟ブロックを壊せる位置でボムを置く確率（%） |
| `hunt` | 10 | 40 | 80 | 相手に近づこうとする強さ（0〜100） |
| `seek-items` | false | true | true | パワーアップを拾いに行くか |
| `power` / `bombs` | 1 / 1 | 2 / 1 | 2 / 1 | CPU の初期火力・同時設置数 |

!!! info "config は起動時に自動で更新されます"
    プラグインを更新すると、起動時に **足りない項目だけ** が既存の `config.yml` に追加されます（設定済みの値・看板・地点はそのまま。更新前のファイルは `config.yml.bak-日時` に退避され、コンソールに反映内容が出ます）。`config.yml` を消して作り直す必要はありません。`puzzles.yml` も同じ仕組みで、新しいステージが増えたときに追加されます。
    ただし、CPU の難易度やパズルのステージを **消しても、次の起動で初期のものが戻ります**。使わない難易度は残したままにするか、ソロ全体を `solo.enabled: false` で止めてください。

## パズルのステージ（`puzzles.yml`）

```yaml title="ステージの書き方（例：ステージ1）"
stages:
  p01:
    display: "はじめの一歩"   # 表示名
    stars: 1                  # 難しさ（1〜5。表示用）
    power: 1                  # 火力
    bombs: 2                  # 使えるボムの総数
    max-active: 1             # 同時に置けるボムの数
    hint: "四方が空いた十字路に置くと、4方向に爆風が届きます。"
    map:                      # # 硬 / x 軟 / . 空き / S スタート（1つ）
      - "#######"
      - "#S....#"
      - "#.#x#.#"
      - "#.x.x.#"
      - "#.#x#.#"
      - "#.....#"
      - "#######"
```

- ステージは並び順に 1, 2, 3… と番号が付きます（最大16。メニュー2段目・3段目に並ぶ）。
- 行の長さがそろっていない・S が1つでない・x が無い・使えない文字がある ステージは、起動時に警告を出して読み込みません。
- 初期の7ステージは、すべて **ちょうど `bombs` 個で解ける** ことを確認済みです（`bomb-timer` 2.5秒・パワーアップなし）。盤面を変えたら必ず自分で解けるか試してください。
- `/bomberman reload` で読み直せます。ステージ名（`p01` 等）を変えると、記録は別のステージとして扱われます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `bomberman.admin` | OP | set系・stop・reload・setsign・removesign・lobbyfix・他人の join/leave/solo・ranking reset などの設定/管理、看板の破壊 |

!!! note "join / leave / start / status / solo / ranking は権限不要"
    `/bomberman join`・`leave`・`start`・`status`・`solo`・`ranking solo` は権限チェックがなく **全プレイヤーが実行可能** です（看板と同等）。設定・管理コマンドのみ `bomberman.admin` が必要です。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/bomberman join` / `leave` / `start` | 全員 | 参加 / 退出 / 開始（看板と同等） |
| `/bomberman solo [cpu [難易度] \| puzzle <ステージ>]` | 全員 | ソロメニュー／すぐ開始 |
| `/bomberman ranking solo [cpu [難易度] \| puzzle [ステージ]]` | 全員 | ソロのランキング |
| `/bomberman status` | 全員 | 設定・進行状況を表示（ソロの状況も） |
| `/bomberman stop` | `bomberman.admin` | ゲームを強制終了（ソロも。コマンドブロック/コンソール可） |
| `/bomberman solo <プレイヤー> [cpu\|puzzle] [...]` | `bomberman.admin` | 指定した人をソロへ（コンソール・コマンドブロック可） |
| `/bomberman ranking reset <プレイヤー\|all> [cpu\|puzzle\|all] [難易度・ステージ\|all]` | `bomberman.admin` | ソロ記録を消す（フィールドやステージを作り直したとき用） |
| `/bomberman reload` | `bomberman.admin` | config・puzzles.yml の再読み込み＋看板の再ロード |
| `/bomberman set... / maxplayers` | `bomberman.admin` | 各種設定（前述） |

```text title="例：ステージ3の記録を全員分消す"
/bomberman ranking reset all puzzle 3
```
```text title="例：あるプレイヤーのソロ記録をすべて消す"
/bomberman ranking reset <プレイヤー>
```

## ソロの仕様メモ（共通ルールとの関係）

- ソロは共通ルール14章（GUIロビー）に従います。通常戦とアリーナを共用し、**同時にどちらか1つだけ** です。ソロ中に `/bomberman start` を打つと理由を出して断り、通常戦の試合中はソロを始められません。
- 試合中（通常戦・ソロとも）の `/bomberman join` はロビー在籍として受け付け、次の試合から参加します（共通ルール12章）。ソロで入った人は通常戦の参加者に含めません。
- `/bomberman stop`：ソロ中なら中断（記録なし）して挑戦者をロビーへ戻し、ソロメニューを開きます。カウントダウン中なら開始を取りやめます。結果表示中なら記録はそのまま、片付けを早めます。試合が無いときは「進行中の試合はありません」と出ます。
- leave・切断・やり直し（時計）は「中断」で記録しません（1人なので脱落＝試合の終わり。負けそうで抜けても記録にならないようにするため）。共通ルール12章の例外です。
- CPU対戦の難易度を省略すると既定の難易度（ふつう）で始めます（共通ルール1章の例外。他ゲームのソロと同じ）。
- ロビー中はソロメニューのネザースター、ソロの試合中はやり直しの時計を1つ置きます（共通ルール6章の例外）。どちらも PDC で識別し、捨てる・移動・持ち替え・額縁／防具立てへの設置はできません。
- CPU は **AI を切ったゾンビ**（PDC 付き）をプラグインが1マスずつ動かしています。無敵で、日光で燃えず、変身せず、アイテムを拾わず、だれも攻撃しません。脱落は爆風（とサドンデスのブロック）の判定だけです。**セーブされないので再起動で残らず**、残っていても起動時・読み込み時に消えます。
- 進行のメッセージ・タイトルは挑戦者本人にだけ送ります。サーバー全体に流すのはクリアの結果1行だけです（`solo.broadcast-result`）。
- 記録の時間はサーバーの tick で数えます（ラグがあっても表示とずれません）。

!!! warning "統合版（Geyser）について"
    ソロメニュー（チェスト画面）・ネザースター・時計は統合版でも使えます（空中タップ、ネザースターはスニークでも開く）。CPU（ゾンビ）は統合版でも表示されますが、通信の遅れで動きが少しカクつくことがあります。実機で見え方を確認してください。

## トラブルシューティング

??? failure "ゲームが開始できない"
    開始には **最小開始人数（既定2人・`setminplayers` で変更可）以上** の通常参加と、**ロビー・初期スポーン・フィールド（1/2）・スポーン地点（人数分）** がすべて設定されている必要があります。ソロ挑戦中は開始できません。`/bomberman status` で不足項目を確認してください。

??? failure "スポーン地点でプレイヤーが落ちる／埋まる"
    フィールド生成で床・障害物の高さが決まるため、`setspawn` は **フィールドの升目上（足元の段）に立って** 設定してください。フィールド外や高さがずれた位置だと落下・埋まりの原因になります。CPU もスポーン地点に出るので同じです。

??? failure "サドンデスの効果が薄い"
    外周（元々の硬い壁）からせり上がるため、最初の数リングは壁の上書きになります。フィールドが大きいほど中央が詰まるまで時間がかかります。`setsuddendeath` の秒数やフィールドサイズで調整してください。

??? failure "パズルのステージが選べない（バリアになる）"
    フィールドが盤面より小さいと遊べません。メニューのボタンに必要な大きさが出ます。`/bomberman status` の「パズル」行に、このフィールドで遊べるステージ数が出ます。

??? failure "CPU が少ない"
    CPU はスポーン地点の2番以降に出ます。スポーンが CPU の数＋1か所より少ないと、CPU を減らして始めます。`setspawn 3`・`setspawn 4` を追加してください。

??? failure "アナウンスが一部のプレイヤーに見えない"
    各種アナウンスはロビー在籍者に送られます。観戦のみ（未参加）のプレイヤーには表示されない設計です。ソロの進行メッセージは挑戦者本人だけに送られます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Bomberman 概要へ](index.md){ .md-button }
