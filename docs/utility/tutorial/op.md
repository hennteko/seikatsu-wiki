<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Tutorial ― OP・運営ガイド { .page-op #tutorial-op }

Tutorial（v2）の導入、地点の登録、config、遊んだ回数の数え方、サーバー Bot（Discord 連携・VC 報酬・人狼RPG の夜ミュート）、管理コマンドをまとめます。旧 **Tutorial2**（サバイバル向け21ステップ）は v2 で置き換えました。

## 導入手順

1. サーバーの `plugins` から **旧 `Tutorial2.jar` を削除** します（両方入れると `/tutorial` が競合します）。旧データ `plugins/Tutorial2/` は使いません。
2. `Tutorial.jar` を **ミニゲームサーバー** の `plugins` に入れて起動します。
3. **CasinoPlugin が必要** です（報酬の E 残高）。CasinoPlugin が止まっている間の報酬は「未払い」として保存し、次のログイン時に渡します。
4. **MiniGameMenu が必要** です（手順1・2でメニューアイテムを使います）。必要なら「地点の登録」をします。
5. 「Discord 連携の設定（サーバー Bot）」をします（人狼RPG の Bot をこちらへ移します）。

!!! info "対象サーバー"
    ミニゲームサーバー（25566）だけに入れます。E 残高（CasinoPlugin）がミニゲームサーバーにあるため、ログインボーナス・VC 報酬もミニゲームサーバーで受け取ります。

## チュートリアルの流れ（初期設定）

ホットバー9番目のメニューアイテム（MiniGameMenu）を使って、ミニゲームの参加まで体験させる流れです。看板は「ロビーにもある」と案内するだけで、メインはメニューです。

| 手順 | type | 完了の条件 |
|---|---|---|
| 1 メニューを開く | `menu` | MiniGameMenu の一覧画面を開いて閉じた（右クリック・`/mgmenu` どちらでも） |
| 2 ゲームを1回遊ぶ | `play` | ミニゲームに参加して抜けた（`tutorial.play-min-seconds`＝30秒以上） |
| 3 E・3サーバーの説明 | `info` | 説明を出して自動で次へ |
| 4 Discord 連携 | `discord` | 連携した（コードは自動で表示）。`/tutorial later` で後回しにすると連携なしで進む |
| 完了 | — | 100E・`finish` 地点へ移動 |

- Discord 連携が使えない設定（サーバー Bot 未設定）のときは、手順4は自動で飛ばします。連携済みの人（人狼RPG から取り込んだ人など）もすぐ次へ進みます。
- MiniGameMenu が入っていないときは、手順1は説明だけ出して進みます。
- 手順2は **参加中フラグに対応したゲーム** で数えます（ソロモードも含む。下の「遊んだの数え方」）。

## 地点の登録（任意）

開始時・完了時に移動させたい場所があれば登録します（無ければ移動しません）。立っている場所と向きを保存します。

| 地点名 | 使いどころ |
|---|---|
| `start` | チュートリアル開始時に移動する場所 |
| `finish` | 完了・スキップしたときに移動する場所 |

```
/tutorial admin setpoint start
```

```
/tutorial admin setpoint finish
```

```
/tutorial admin points
```

## チュートリアルの手順を変える

手順は `config.yml` の `tutorial.steps` に上から順に書きます。

| 項目 | 内容 |
|---|---|
| `type: menu` | MiniGameMenu の一覧画面を開いて閉じたら完了 |
| `type: play` | ミニゲームに参加して抜けたら完了（`tutorial.play-min-seconds` 秒以上） |
| `type: info` | `done` の説明を出して自動で次へ |
| `type: discord` | Discord と連携したら完了（`/tutorial later` で後回し可） |
| `type: reach` | `point` の地点に着いたら完了（地点は `setpoint` で登録） |
| `type: command` | `commands` のどれかを実行したら完了（例 `["/mgmenu"]`） |
| `goal` | やること（チャット・タイトル・アクションバー） |
| `done` | 完了したときに出す説明（複数行） |

!!! info "旧版（道を歩く流れ）からの更新"
    `config.yml` の手順が旧初期値（lobby / menu / emerald / servers / play）のままなら、起動時に新しい流れへ自動で置き換えます（`config-version` 3、更新前は `config.yml.bak`）。手順をカスタマイズしていた場合は置き換えずに警告を出します。手順が変わると、進行中の人は手順1からやり直しになります。旧版で使っていた地点（lobby / emerald / servers）は使わなくなります。

## ミニゲームを「遊んだ」の数え方

ミニゲーム共通（minigame-common）の参加中フラグ **`mattari:active_game`** を1秒ごとに見て、**フラグが付いてから外れるまでの時間** で判定します。各ミニゲームを改修しなくても使えます。

| 判定 | 条件 |
|---|---|
| チュートリアル最後の手順 | `tutorial.play-min-seconds`（既定 30秒）以上 |
| ミッション「3種類」 | `play-count.min-seconds`（既定 120秒）以上。ゲームごとに `play-count.min-seconds-by-game` で変更可 |
| 数えない | 途中で切断した場合、`play-count.ignore` のゲーム |

ゲームの ID（ロックID）は、そのゲームに参加中の人がいるときに次のコマンドで確認できます。

```
/tutorial admin tracking
```

!!! warning "フラグを使っていないゲームは数えません"
    参加中フラグに対応しているのは、ミニゲーム共通（minigame-common）のゲーム17個と、ロビー共通部品（lobby-inventory）のゲーム36個です（2026-11-07 から。ロビー共通部品のゲームではフラグの値が `プラグイン名|【ゲーム名】` で、ゲームIDはプラグイン名になります）。ロビー共通部品のゲームはロビーに入った時点でフラグが付くため、待ち時間も参加秒数に入ります。数えたいゲームが対応していない場合は、下の `record` をゲーム側から呼んでください。

    **カジノ（CasinoPlugin）** も数えます（2026-11-07 から）。ロビーを使うゲーム（ブラックジャック・ポーカー・チンチロ・ヨット・ルーレット）は参加中フラグ `CasinoPlugin#<ゲーム>` で時間を計り、ロビーを使わないゲーム（スロット・競馬・育成競馬・くじ・クイズタワー）は遊んだとき（ベット・購入・出走登録・挑戦）にカジノから `record` で報告されます。ソロ版は通常版と同じ種類（例: `CasinoPlugin#blackjack`）です。カジノを数えたくない場合は `play-count.ignore` にゲームIDを入れてください。

### 「最後まで遊んだ」で数える（record）

ゲームが終わったとき（結果発表・ゴールなど）に、ゲーム側からコンソールで次のコマンドを実行すると、時間に関係なく「遊んだ」になります。

```
/tutorial record <プレイヤー> <ゲームID> [表示名]
```

ゲーム側のコード例（ゴール時など）：

```java
Bukkit.dispatchCommand(Bukkit.getConsoleSender(),
    "tutorial record " + player.getName() + " AthleticTimer アスレタイマー");
```

`record` で報告するゲームを時間では数えないようにする（＝最後まで遊んだ人だけ数える）には、`play-count.record-only` にゲームIDを入れます。Tutorial が入っていないサーバーでは「不明なコマンド」になるだけで、ゲームには影響しません。

## ログインボーナス

その日最初のログインで渡します（日付は `timezone`、既定 `Asia/Tokyo`）。ログインしたまま日付をまたいだ人にも、1分ごとの確認で渡します（未払いの E があれば、そのときに払います）。金額は `login-bonus.base` ＋ `login-bonus.streak-bonus` の「連続日数番目」です。リストより長く続いたら最後の値がずっと続きます。

| 連続日数 | 既定の金額 |
|---|---|
| 1日目 | 100E |
| 2〜6日目 | 250E / 300E / 350E / 400E / 450E |
| 7日目以降 | 500E |

## Discord 連携の設定（サーバー Bot）

Discord Bot は **このプラグインの中で動く「サーバー Bot」** です（旧・人狼RPG の Bot をここへ移しました）。1回連携すれば、チュートリアルの報酬・VC 報酬・**人狼RPG の夜ミュート** のすべてに使えます。

| 機能 | 内容 |
|---|---|
| 連携 | マイクラで `/tutorial link` → 6桁コード → Discord で `/link <コード>` か、連携パネルの **連携する** ボタン |
| ロビー連携パネル | 人狼RPG の `/wajinro linkpost` で、ロビーにいる未連携の人の **名前ボタン** を投稿（押した人と連携） |
| VC 報酬 | 連携している人が、Bot 以外 2人以上の VC に 5分いると 500E（1日1回）。スピーカーミュート・AFK チャンネルは数えない |
| 人狼RPG | 夜の一括ミュート・死者ミュートを、この Bot で行う |

### 1. Bot の設定を人狼RPG から移す

人狼RPG の `plugins/WaJinro/config.yml` の `discord.bot-token` と `discord.guild-id` を、Tutorial の `plugins/Tutorial/config.yml` へ移します。

```yaml title="plugins/Tutorial/config.yml（discord の中の bot）"
discord:
  bot:
    token: "（人狼RPG の bot-token）"
    guild-id: "（人狼RPG の guild-id）"
    link-channel-id: "（連携パネルを置くテキストチャンネルの ID）"
```

書き換えたら、反映します。

```
/tutorial admin reload
```

人狼RPG 側は `discord.use-server-bot: true`（既定）のままにします。Tutorial に Bot が設定されていると、人狼RPG は **自前の Bot を起動しません**（同じトークンで2重に接続しないため）。人狼RPG の `discord.enabled` はどちらでも構いません。`discord.voice-channels`（夜ミュートする VC）と `link-channel-id` は人狼RPG 側の設定をそのまま使います。

### 2. 人狼RPG の連携を取り込む

人狼RPG ですでに連携している人を、Tutorial の連携に取り込みます（1回だけ実行）。取り込んだ人には **連携報酬 1,000E** を渡します（オフラインの人は次のログイン時にお知らせ）。

```
/tutorial admin importlinks
```

既定で `plugins/WaJinro/links.yml` を読みます。同じ連携は飛ばし、別のアカウントと連携済みのものはスキップしてログに出します。取り込み前でも、人狼RPG は `links.yml` の連携を予備として使うので夜ミュートは止まりません。

### 3. 連携パネルを投稿する

```
/tutorial admin postlink
```

`discord.bot.link-channel-id` のチャンネルに **連携する** ボタンを投稿します（チャンネル ID を後ろに付けると、そのチャンネルへ）。

!!! note "Discord 側の準備"
    - `/link` はスラッシュコマンドです。Bot の招待に **`applications.commands`** が入っていないと登録に失敗します（起動ログに警告）。その場合も連携パネルのボタンは使えます。
    - VC の人数を見るため **ボイス状態（GUILD_VOICE_STATES）** を使います（人狼RPG と同じ。Developer Portal の設定は不要）。
    - 人狼RPG のミュートには、これまでどおり Bot に **メンバーをミュート** 権限が必要です。

!!! info "BumpBotHub と RCON は使いません"
    BUMP カウント Bot（BumpBotHub）は今までどおり BUMP だけを担当します。BumpBotHub に入れたマイクラ連携（`mclink.enabled`）は **false のまま** にしてください（true にすると VC 報酬の判定が2か所になります）。RCON の設定も不要です。`tutorial discord ...` のコンソールコマンドは動作確認用に残しています。

## config.yml

起動時に、足りない項目だけ自動で追記します（既存の値は変えません。追記したときは `config.yml.bak` を保存し、コンソールに追記した項目を出します）。

| キー | 既定 | 内容 |
|---|---|---|
| `rewards.tutorial-complete` | 100 | チュートリアル完了 |
| `rewards.mission-games` | 200 | ミッション「ミニゲームを○種類」 |
| `rewards.discord-link` | 1000 | ミッション「Discord と連携」（Discord ID・マイクラアカウントそれぞれ1回） |
| `tutorial.auto-start-first-join` | true | 初めて入った人に自動で開始 |
| `tutorial.skip-gives-reward` | false | スキップした人にも完了報酬を渡すか |
| `tutorial.point-radius` | 3.0 | 地点に着いたとみなす半径 |
| `tutorial.play-min-seconds` | 30 | 最後の手順で必要な参加秒数 |
| `play-count.required-kinds` | 3 | 何種類でミッション達成か |
| `play-count.min-seconds` | 120 | ミッションで数える参加秒数 |
| `login-bonus.base` / `streak-bonus` | 100 / [0,150,…,400] | ログインボーナス |
| `discord.code-expire-minutes` | 10 | 連携コードの有効時間 |
| `discord.bot.token` / `guild-id` | 空 | サーバー Bot のトークンと Discord サーバー ID |
| `discord.bot.link-channel-id` | 空 | 連携パネルの投稿先 |
| `discord.vc-reward.amount` / `daily-max` | 500 / 1 | VC 報酬の額と1日の回数 |
| `discord.vc-reward.interval-minutes` / `min-members` | 5 / 2 | 何分で1回分か／Bot 以外何人以上の VC を数えるか |
| `discord.vc-reward.exclude-channels` | [] | 数えない VC（VC 報酬のお知らせはマイクラ内だけ。Discord の DM は送りません） |
| `points` | — | 地点（コマンドで登録。手で書かなくてよい） |

## 管理コマンド

| コマンド | 内容 |
|---|---|
| `/tutorial admin setpoint <名前>` | 今いる場所を地点として登録 |
| `/tutorial admin delpoint <名前>` | 地点を削除 |
| `/tutorial admin points` | 地点の一覧と未登録の確認 |
| `/tutorial admin start <プレイヤー>` | その人のチュートリアルを最初から開始（完了報酬は1人1回） |
| `/tutorial admin skip <プレイヤー>` | スキップ扱いにする |
| `/tutorial admin reset <プレイヤー> [all]` | チュートリアルを未開始に戻す（`all` でミッションも） |
| `/tutorial admin info <プレイヤー>` | 進み具合・遊んだゲーム・未払い・Discord 連携を表示 |
| `/tutorial admin tracking` | 今ミニゲームに参加中で計測している人とゲームID |
| `/tutorial admin postlink [チャンネルID]` | Discord に「連携する」パネルを投稿 |
| `/tutorial admin importlinks [パス]` | 人狼RPG の連携（既定 `plugins/WaJinro/links.yml`）を取り込む（連携報酬あり） |
| `/tutorial admin reload` | config を再読み込み（Bot も再接続） |
| `/tutorial record <プレイヤー> <ゲームID> [表示名]` | 「遊んだ」を記録（コンソール・OP） |
| `tutorial discord confirm/vc/whois ...` | 動作確認用（**コンソールのみ**。OP でも実行不可） |

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `tutorial.use` | 全員 | `/tutorial`・start・goal・skip・link・unlink・status |
| `tutorial.admin` | op | `admin` と `record` |

## データ

`plugins/Tutorial/data.yml` に、プレイヤーごとの進み具合・遊んだゲーム・ログボの連続日数・Discord ID・未払いの E を保存します（30秒ごと＋停止時）。手で直すときはサーバーを止めてから編集してください。

## トラブルシューティング

??? failure "初めて入った人なのにチュートリアルが始まらない"
    自動開始は「そのサーバーに初めて入った（`hasPlayedBefore()` が false）」人だけです。旧生活鯖から入っていた人は対象外なので、ログイン時に `/tutorial start` の案内が出ます。手動で始めるときは `/tutorial admin start <プレイヤー>`。

??? failure "メニューを開いても手順1が進まない"
    メニューの画面を **閉じたとき** に進みます。MiniGameMenu が入っているか、`tutorial.menu-holder-class` が `com.henry.minigamemenu.gui.MenuHolder` になっているかを確認してください。

??? failure "Discord の手順で止まってしまう"
    コードの期限（10分）が切れたら `/tutorial link` でもう一度出せます。Discord を使っていない人は `/tutorial later` で先へ進めます。

??? failure "最後の「ミニゲームを1回遊ぶ」が終わらない"
    参加中フラグに対応していないゲームは数えません。対応しているゲームで遊ぶか、そのゲームから `tutorial record` を呼んでください。`/tutorial admin tracking` で計測されているか確認できます。

??? failure "E が入らない（未払い）"
    CasinoPlugin が起動していないと入金できず、未払いとして保存します。`/tutorial admin info <プレイヤー>` の「未払い」に出ます。次のログイン時に自動で入金します。

??? failure "サーバー Bot が接続しない"
    起動ログに「サーバー Bot が接続しました」が出ているか確認してください。出ない場合はトークンの貼り間違いが大半です。初回起動時は JDA ライブラリを自動ダウンロードするため、ネット接続が必要です。

??? failure "人狼RPG の夜ミュートが動かない"
    `/wajinro status` の「BOT」が「サーバーBot（Tutorial）を使用」になっているか確認してください。Tutorial を先に入れて起動する必要があります（人狼RPG は Tutorial の後に読み込まれます）。ミュートされない人は、連携しているか（`/tutorial admin info`）と、その VC が人狼RPG の `voice-channels` に入っているかを確認します。

??? failure "VC にいても報酬が来ない"
    - 連携しているか（`/tutorial admin info`）
    - Bot 以外に `vc.min.members` 人以上いるか、スピーカーミュート・AFK チャンネルではないか
    - 今日の上限（`discord.vc-reward.daily-max`）に達していないか
    - サーバー Bot が接続しているか（起動ログ）

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Tutorial 概要へ](index.md){ .md-button }
