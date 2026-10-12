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
4. `/drta world create [チーム数]` で試合用ワールドを作成し、事前生成する。
5. シードプリセットを用意し（`/drta preset`）、参加・開始の看板を設置する。
6. `/drta status`・`/drta check` で設定・状態を確認する。

!!! note "チームごとに専用ワールドを生成します"
    各チームに専用のオーバーワールド・ネザー・エンドを用意する方式のため、`world create` でワールドを生成し、事前生成（pregen）で読み込んでおくと開始がスムーズです。事前生成の範囲や速度は config の `world.pregen` や設定ツール（`/drta wand`）で調整できます。

## セットアップ手順（コマンド）

すべて `dragonrta.admin` 権限が必要です。

```text title="受付ロビー／初期スポーンを設定"
/drta setlobby
/drta setstartspawn
```

```text title="試合用ワールドを作成（チーム数省略で既定）"
/drta world create <チーム数>
```

```text title="設定ツール（ワンド）を受け取る（事前生成などの調整）"
/drta wand
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

- ソロRTAは通常の試合を「本人だけの1チーム」で動かします。ワールドはチーム戦と共用で、同時に走れるのは1人だけです。
- 1回走るとワールドは「使用済み」になります。続けて遊べるようにするには、`world.auto-recreate-after-end: true` にします（試合後、同じプリセットで自動で作り直します。random は毎回新しいシード）。
- チーム戦の参加者がロビーで待っている間は、ソロを始められません（`solo.block-when-lobby-waiting`）。
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

!!! note "共通ルールとの違い"
    - 1回走るとワールドの作り直しが必要です（自動作り直しは config で ON）。共通ルール14章の「終わったらすぐ次を遊べる」は、作り直しが終わってからになります。
    - 試合中の切断は、60秒以内に戻れば続行できます（エンドラRTAの既存の仕様）。
    - ソロの撃破は試合結果として全体に告知します。ソロ中の進捗アナウンスは在籍者だけに送ります。
    - `/drta ranking solo` でプリセットを省略すると、一覧ではなく今のワールドのプリセットを表示します。

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
| `teams.list.<id>` | 8色 | チーム定義（`name`・`color`）。最大8。RED/BLUE/GREEN/YELLOW/AQUA/LIGHT_PURPLE/GOLD/WHITE 等 |

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
| `solo.broadcast-milestones` | false | ソロ中の進捗アナウンスを全体に流す（false＝在籍者だけ） |

### 進捗アナウンス（`announce`）

各チームが節目を **最初に達成したとき** サーバー全体に告知します。`format` は `{game}` `{team}` `{label}` `{time}` を使えます。項目ごとに `enabled`・`label` を持ち、初鉄・ダイヤ・溶岩バケツ・ネザーゲート作成・ネザー到達・要塞発見・砦発見・ブレイズロッド・物々交換・エンダーパール・エンダーアイ・要塞発見・エンドポータル起動・エンド到達・ドラゴンHP半分・クリスタル全破壊・（既定オフ）初死亡 を実況できます。

### 参加・離脱の告知（`messages`）

`messages.join-broadcast` / `leave-broadcast` / `already-in-other-game` / `vault-save-failed` を設定できます（`{game}` `{player}` `{count}` `{other}` を使用）。表示名は `display-name`（既定「【エンドラRTA】」）。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/drta setlobby` / `setstartspawn` | ロビー／初期スポーンを設定 |
| `/drta world <create [チーム数]\|delete ...>` | 試合用ワールドの作成／削除 |
| `/drta wand` | 設定ツール（事前生成などの調整） |
| `/drta preset <test\|save ...>` | シードプリセットの確認／保存 |
| `/drta setsign <join\|leave\|start\|team\|spectate\|solo\|delete>` | 看板を設定／解除 |
| `/drta solo <プレイヤー> [rta]` | 指定した人をソロに参加させてメニューを開かせる（rta ですぐ開始）。コンソール・コマンドブロックからも可 |
| `/drta ranking reset <プレイヤー\|all> [rta\|all] [プリセット\|all]` | ソロの記録を消す。絞らずに消すと「前回の結果」も消える |
| `/drta stop` | ゲームを強制終了 |
| `/drta forceunlock <プレイヤー> [confirm]` | 参加ロック（他ゲーム参加中フラグ）を強制解除。退避ファイルが残っていれば同時に復元。稼働中の別ゲームに参加中のときは `confirm` で強制 |
| `/drta lobbyfix <プレイヤー> [confirm]` | `forceunlock` の別名。ロビー前の持ち物が戻らない時の復旧にも使える |
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
