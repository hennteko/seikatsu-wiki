<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# DragonRTA（エンドラRTA） ― OP・運営ガイド { .page-op #dragonrta-op }

DragonRTA の導入・ワールド作成／事前生成・シードプリセット・地点／看板設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | DragonRTA |
| メインコマンド | `/dragonrta`（エイリアス `/drta`） |
| api-version | 26.1.2 |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/DragonRTA/config.yml` |
| 権限ノード | `dragonrta.admin`（既定OP） |

## 導入手順

1. ビルドした `DragonRTA` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される。
3. `/drta setlobby`・`/drta setstartspawn` でロビーと初期スポーンを設定する。
4. `/drta world create <プリセット> [チーム数]` で試合用ワールドを作成し、事前生成する。
5. シードプリセットを用意し（`/drta preset`）、参加・開始の看板を設置する。
6. `/drta status`・`/drta check` で設定・状態を確認する。

!!! note "チームごとに専用ワールドを生成します"
    各チームに専用のオーバーワールド・ネザー・エンドを用意する方式のため、`world create` でワールドを生成し、事前生成（pregen）で読み込んでおくと開始がスムーズです。事前生成の範囲や速度は config の `world.pregen` や管理メニュー（`/drta wand` の管理ツール、または `/drta menu`）で調整できます。

## セットアップ手順（コマンド）

すべて `dragonrta.admin` 権限が必要です。

```text title="受付ロビー／初期スポーンを設定"
/drta setlobby
/drta setstartspawn
```

```text title="試合用ワールドを作成（プリセット必須・チーム数省略で既定）"
/drta world create <プリセット> [チーム数]
```

```text title="管理ツール（ブレイズロッド）を受け取る"
/drta wand
```

```text title="管理メニューを開く（参加中でも開けます）"
/drta menu
```

```text title="参加／離脱／開始の看板を登録（看板を見て実行）"
/drta setsign join
/drta setsign leave
/drta setsign start
```

```text title="ソロ看板を登録（看板を見て実行）"
/drta setsign solo
```

```text title="視線先の看板の登録を解除"
/drta setsign delete
```

## ソロRTA（記録・ランキング）

1人で走った撃破タイムを、プレイヤー × プリセットごとの自己ベストとして `plugins/DragonRTA/solo-records.yml` に保存します。ソロメニュー（共通ルール14章のGUIロビー）から始めます。

- **ストックを使う場合（`solo.stock.enabled: true`、2.0.0〜）**：ソロ専用のワールド（ストック）で走ります。チーム戦や別の人のソロRTAと同時に進められます（下の「ストック」）。
- **ストックを使わない場合**：通常の試合を「本人だけの1チーム」で動かします。ワールドはチーム戦と共用で、同時に走れるのは1人だけです。1回走るとワールドは「使用済み」になるので、`world.auto-recreate-after-end: true` で自動で作り直せます。チーム戦の参加者がロビーで待っている間は始められません（`solo.block-when-lobby-waiting`）。
- ソロの記録は、開始から撃破までずっと1人だったチームが対象です（1対1のレースで勝った人も入ります）。従来の `records.yml`（`/drta records`）にもこれまでどおり記録されます。

```text title="ミニゲームメニューのボタン（プレイヤーとして実行する場合）"
/drta solo
```

```text title="ミニゲームメニューのボタン（コンソールとして実行する場合）"
/drta solo <プレイヤー>
```

```text title="ある人のソロ記録を消す（全プリセット）"
/drta ranking reset <プレイヤー>
```

```text title="あるプリセットのソロ記録を全員分消す"
/drta ranking reset all rta <プリセット>
```

```text title="ソロ記録をすべて消す"
/drta ranking reset all
```

### ストック（2.0.0〜）

ソロ専用のワールド（オーバーワールド・ネザー・エンドの3つで1組）を前もって作って保管し、ソロRTAを始める時に読み込みます。

- 保管中は読み込みを外しているので、メモリは使いません（ディスクだけ）。走っている人数分だけメモリを使います。
- 走り終わったストックは削除し、裏で次のストックを作ります（生成は同時に1つ。TPS が下がると速度を落とします）。
- チーム戦のワールドの生成・削除中は、ストックの生成を止めます。`solo.stock.pause-refill-during-match: true` なら、チーム戦の試合中も止めます。
- ストックの一覧・容量は `/drta stock` で確認できます。最初は1組作って容量を確かめてから、保管数を決めてください。
- 使用中にサーバーが止まったストックと、生成途中のストックは、次の起動時に削除して作り直します。
- プリセットのシードを変えた時は、`/drta stock clear <プリセット>` で古いストックを消してください。

```text title="ストックを使う（config.yml の solo.stock.enabled を true にしてから）"
/drta reload
```

```text title="ストックの一覧と容量"
/drta stock
```

```text title="足りないストックを今すぐ作り始める"
/drta stock refill
```

```text title="あるプリセットの保管中のストックを削除（使用中は残る）"
/drta stock clear <プリセット>
```

```text title="ある人のソロRTAを強制停止（記録しない）"
/drta stop solo <プレイヤー>
```

!!! note "共通ルールとの違い"
    - ストックを使う場合、ソロRTAはチーム戦と別のワールドで同時に進みます（共通ルール14章の「アリーナ共用・どちらかが試合中は拒否」の例外）。
    - `/drta stop` はチーム戦だけを止めます。ソロRTAは `/drta stop solo <プレイヤー|all>` で個別に止めます（走っているほかの人を巻き込まないため）。
    - ストックを使わない場合は、1回走るとワールドの作り直しが必要です（自動作り直しは config で ON）。
    - 試合中の切断は、60秒以内に戻れば続行できます（エンドラRTAの既存の仕様）。
    - ソロの撃破は試合結果として全体に告知します。ソロ中の進捗アナウンスは在籍者だけに送ります。
    - `/drta ranking solo` でプリセットを省略すると、一覧ではなく今のワールドのプリセットを表示します。

## 管理ツール（OP専用）

`/drta wand` で管理ツール（ブレイズロッド）を受け取り、右クリック（統合版はタップ/長押し）で管理メニューを開きます。`/drta menu` でも開けます。

- 数値：＋/−ボタンでチーム数・事前読み込みの範囲・ストックの保管数・同時に走れる人数・ストックの事前読み込みの範囲を変更（即保存）
- 地点：ボタンを押すと立ち位置に設定（ロビー地点・初期スポーン）。移動は別のボタン
- 看板：看板を見ながら（5ブロック以内）押すと参加／離脱／開始／チーム選択／観戦／ソロ看板を登録、ハサミで登録解除
- 運用：開始・設定状況・動作チェック・リロード・強制停止（強制停止は5秒以内に2回押し）
- エンドラRTA に参加中は受け取れません（離脱してから実行）。捨てると消えるので再度 /drta wand で取得してください
- 統合版：チェストGUIで操作できます。数値は左右クリックに頼らず＋/−の別ボタンで操作します

管理メニューのそのほかの項目：

| 欄 | 内容 |
|---|---|
| チーム戦のワールド | 状態・作成（プリセットを選ぶ画面へ）・難易度（押すと次へ）・生成の中断／削除（5秒以内に2回押し） |
| 試合 | 途中参加・チームチャット・降参・観戦・味方の方向表示・天気の変化の ON/OFF |
| ソロRTA | ソロ・ストック・試合後の自動作り直し・待機者がいる間はソロ不可・撃破の全体告知・進捗の全体告知の ON/OFF、ストックの設定画面、走っているソロRTAの一覧（強制停止は5秒以内に2回押し） |
| 表示 | 管理ツールを持っている間、ロビー地点（緑）・初期スポーン（白）・登録看板（種類ごとの色）を粒子で表示（本人だけ。ON/OFF） |

- 難易度と天気の変化は、次のワールド作成（または再起動）から反映されます。
- 今のワールドがある状態でプリセットを選ぶと、今のワールドを消して作り直します（5秒以内に2回押し）。
- 1.2.0 以前の設定ツールも、そのまま使えます。

## シードプリセット

シードを **プリセット** として登録し、開始時に選べます。

```text title="シードを試して（テスト生成で）スポーン地点を確認"
/drta preset test <シード|random>
```

```text title="確認したシードをプリセットとして保存"
/drta preset save <ID> <表示名>
```

既定では `random`（ランダム）・`plains`（草原）・`desert`（砂漠）・`ocean`（海）・`village`（村）・`ruined_portal`・`taiga`・`jungle`・`badlands`・`cherry`（サクラ）・`mushroom`（キノコ島）が用意されています（多くは `seed: ""` で未設定なので、良いシードが見つかったら保存してください）。

## config.yml 設定項目

### チーム（`teams`）

| キー | 既定値 | 説明 |
|---|---|---|
| `teams.default-count` | 2 | `world create` でチーム数を省略した時のチーム数 |
| `teams.max-diff` | 1 | チーム間の人数差の上限 |
| `teams.list.<id>` | 8色 | チーム定義（`name`・`color`）。最大8。RED/BLUE/GREEN/YELLOW/AQUA/LIGHT_PURPLE/GOLD/WHITE 等。`stock` で始まるIDはストック用のため使えません |

### 試合（`match`）

| キー | 既定値 | 説明 |
|---|---|---|
| `match.min-players` | 1 | 開始に必要な最低人数（1で練習可） |
| `match.max-players` | 0 | 最大人数（0＝無制限） |
| `match.countdown-seconds` | 10 | 開始カウントダウン |
| `match.team-empty-grace-seconds` | 60 | チームのオンライン人数が0になってから脱落するまでの猶予（秒） |
| `match.allow-mid-join` | true | 試合中の途中参加を許可 |
| `match.rejoin-teleport-to-mate` | true | 切断→再ログイン時に味方の場所へTPするか |
| `match.team-chat` | true | 試合中のチャットをチーム内に限定 |
| `match.global-chat-prefix` | `!` | この文字で始めると全体チャット |

### 降参・コンパス・観戦

| キー | 既定値 | 説明 |
|---|---|---|
| `surrender.enabled` | true | 降参投票の有効化 |
| `surrender.min-minutes` | 5 | 開始から降参できるまでの分数 |
| `surrender.vote-seconds` | 60 | 投票の受付時間（秒） |
| `compass.enabled` | true | 味方の方向をActionBarに表示 |
| `spectate.enabled` | true | 観戦の有効化 |

### 試合用ワールド（`world`）

| キー | 既定値 | 説明 |
|---|---|---|
| `world.weather-cycle` | false | 天気の変化（falseで常に晴れ） |
| `world.difficulty` | NORMAL | 難易度（PEACEFUL/EASY/NORMAL/HARD） |
| `world.cleanup-on-startup` | false | 起動時に前回の試合ワールドを自動削除 |
| `world.auto-delete-after-end-seconds` | 0 | 試合終了から自動削除までの秒数（0＝手動削除）。自動作り直しが ON の間は使われない |
| `world.auto-recreate-after-end` | false | 試合終了後、同じプリセットでワールドを自動で作り直す |
| `world.auto-recreate-delay-seconds` | 15 | 試合終了から作り直しを始めるまでの秒数（最低5。試合用ワールドに死亡中の人がいれば最大1分待つ） |
| `world.pregen.overworld-radius` | 1000 | オーバーワールドの事前生成半径 |
| `world.pregen.nether-radius` | 250 | ネザーの事前生成半径 |
| `world.pregen.end-radius` | 200 | エンドの事前生成半径 |
| `world.pregen.max-concurrent-chunks` | 16 | 同時生成チャンク数（大きいほど速いが重い） |
| `world.pregen.min-tps` | 18.0 | TPSがこれを下回ると生成速度を落とす |

### ソロRTA（`solo`）

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | true | ソロRTAの有効化 |
| `solo.menu-delay` | 3 | 試合終了からソロメニューを開くまでの秒数 |
| `solo.block-when-lobby-waiting` | true | チーム戦の参加者がロビーで待っている間はソロを始めない |
| `solo.giveup-anytime` | true | ソロでは降参（中断）に開始○分の制限をかけない |
| `solo.hide-random-seed` | true | random シードのワールドでは開始前にシードを表示しない（OP には表示） |
| `solo.ranking-size` | 10 | `/drta ranking solo` の表示人数 |
| `solo.broadcast-clear` | true | ソロRTAの撃破をサーバー全体に告知する |
| `solo.broadcast-milestones` | false | ソロ中の進捗アナウンスを全体に流す（false＝本人と観戦者だけ） |
| `solo.stock.enabled` | false | ストックを作って使う（ON の間、ソロRTAはストックで走る） |
| `solo.stock.presets.<プリセットID>` | random: 2 | プリセットごとの保管数（シードが設定されているプリセットだけ。管理メニューからも変更可） |
| `solo.stock.max-concurrent` | 3 | 同時に走れるソロRTAの人数 |
| `solo.stock.pause-refill-during-match` | true | チーム戦の試合中はストックの補充を止める |
| `solo.stock.pregen.overworld-radius` | 500 | ストックの事前読み込み半径（オーバーワールド） |
| `solo.stock.pregen.nether-radius` | 150 | 同（ネザー） |
| `solo.stock.pregen.end-radius` | 200 | 同（エンド） |

### 進捗アナウンス（`announce`）

各チームが節目を **最初に達成したとき** サーバー全体に告知します。`format` は `{game}` `{team}` `{label}` `{time}` を使えます。項目ごとに `enabled`・`label` を持ち、初鉄・ダイヤ・溶岩バケツ・ネザーゲート作成・ネザー到達・ネザー要塞発見・砦の遺跡発見・ブレイズロッド・物々交換・エンダーパール・エンダーアイ・要塞発見・エンドポータル起動・エンド到達・ドラゴンHP半分・クリスタル全破壊・（既定オフ）初死亡 を実況できます。

### 参加・離脱の告知（`messages`）

`messages.join-broadcast` / `leave-broadcast` / `already-in-other-game` / `vault-save-failed` を設定できます（`{game}` `{player}` `{count}` `{other}` を使用）。表示名は `display-name`（既定「【エンドラRTA】」）。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/drta setlobby` / `setstartspawn` | ロビー／初期スポーンを設定 |
| `/drta world <create <プリセット> [チーム数]\|cancel\|delete\|status>` | 試合用ワールドの作成／生成中断／削除／状態確認 |
| `/drta wand` | 管理ツールを受け取る（右クリックで管理メニュー） |
| `/drta menu` | 管理メニューを開く（参加中でも開ける） |
| `/drta reload` | config.yml を再読み込み |
| `/drta stock [refill\|clear [プリセット\|all]]` | ストックの一覧／今すぐ補充／保管中の削除 |
| `/drta stop solo <プレイヤー\|all>` | ソロRTA（ストック）を強制停止（記録しない） |
| `/drta preset <test\|save ...>` | シードプリセットの確認／保存 |
| `/drta setsign <join\|leave\|start\|team\|spectate\|solo\|delete>` | 看板を設定／解除 |
| `/drta solo <プレイヤー> [rta [プリセット]]` | 指定した人をソロに参加させてメニューを開かせる（rta ですぐ開始）。コンソール・コマンドブロックからも可 |
| `/drta ranking reset <プレイヤー\|all> [rta\|all] [プリセット\|all]` | ソロの記録を消す。絞らずに消すと「前回の結果」も消える |
| `/drta stop` | チーム戦を強制終了（ソロRTAは止めない） |
| `/drta forceunlock <プレイヤー>` | 参加ロック（他ゲーム参加中フラグ）を強制解除 |
| `/drta check` | 設定・整合性のチェック |
| `/drta status` | 設定状況・現在の状況を確認（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `dragonrta.admin` | OP | ワールド作成・事前生成・プリセット・看板設置・強制停止・forceunlock・check など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/drta join`・`/drta leave`・`/drta start`・`/drta status`・`/drta team`・`/drta spectate`・`/drta surrender`・`/drta compass`・`/drta records`・`/drta solo`・`/drta solo rta`・`/drta ranking solo` は権限チェックがなく、全プレイヤーが使えます（`/drta solo <プレイヤー>` で他人を指定できるのは OP・コンソール・コマンドブロックだけ）。

## 全ゲーム共通の改修（2026-09）

DragonRTA も全ミニゲーム共通の改修に対応しています（名前表示「【エンドラRTA】名前」、参加/離脱の全体告知、同時参加は1ゲームまで＝他ゲーム参加中は拒否・`/drta forceunlock` で解除、退避データの `plugins/DragonRTA/vault/` へのファイル保存とクラッシュ後復元）。config の `display-name`・`messages.*` は自動追記に対応します。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/drta status` と `/drta check` を確認してください。ロビー・試合用ワールド（`world create`）・プリセットが必要です。事前生成が終わっていないと開始に時間がかかることがあります。

??? failure "参加できない（他ゲーム参加中と出る）"
    別のミニゲームに参加中です。そちらを離脱してください。異常でロックが残った場合は `/drta forceunlock <プレイヤー>` で解除できます。

??? failure "ソロRTAを始められない"
    ソロメニューの開始ボタンに理由が出ます。「チーム戦の参加者が待機中」の場合は、ロビーに残っているチーム戦の参加者が離脱するか `/drta solo` でソロに切り替えると始められます。「使用済み」の場合は `/drta world create <プリセット>` で作り直すか、`world.auto-recreate-after-end` を ON にしてください。

??? failure "サーバーが重い"
    事前生成の `world.pregen.max-concurrent-chunks` を下げるか、`min-tps` を上げてください。試合ワールドは容量を使うため、`auto-delete-after-end-seconds` や `cleanup-on-startup` で掃除できます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← DragonRTA 概要へ](index.md){ .md-button }
