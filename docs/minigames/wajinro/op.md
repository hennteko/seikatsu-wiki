<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# WaJinro（人狼RPG）― OP・運営ガイド { .page-op #wajinro-op }

WaJinro（人狼RPG）の導入・地点/看板/ステージ設定・config・権限・管理コマンド・Discord連携をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | WaJinro |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/wajinro`（エイリアス `/wj`） |
| 依存プラグイン | なし（Discord用の JDA ライブラリは起動時に自動DL） |
| 設定ファイル | `plugins/WaJinro/config.yml` |

!!! note "対応環境"
    Paper 1.26.x（api-version `26.1.2` / Java 25）で動作します。プラグインが赤色で読み込まれない場合は、サーバーのJavaバージョンを確認してください（Paper 26.1 系は Java 25 が必要です）。Discord連携を使う場合、初回起動時に JDA ライブラリ（`net.dv8tion:JDA:5.2.1`）が Maven Central から自動ダウンロードされます（要ネット接続）。連携を使わない場合もダウンロードは行われますが、機能は無効のままです。

## 導入手順

1. ビルドした `WaJinro-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/WaJinro/config.yml` が自動生成される。
3. 後述の初期設定（地点・ステージ・看板）を行う。
4. 各設定コマンドは実行時に自動保存されます（`/wajinro save` はありません）。

!!! warning "config.yml の自動生成・自動追記について"
    本プラグインは初回起動時に `saveDefaultConfig()` で `config.yml` を生成しますが、**既存の `config.yml` に新しいキーを自動追記しません**（コード側に既定値があるため、キーが無くても動作はします）。バージョンアップ後に新しい設定キーを使いたい場合は、手動で追記するか、`config.yml` を退避して再生成してください。**再読み込み（reload）コマンドはありません** — `config.yml` を直接編集した場合はサーバー再起動が必要です。ゲーム内コマンドや管理本で変更した項目は即時保存・反映されます。

## 初期設定

### 1. 地点の設定

OP権限で、設定したい場所に **立った状態** で以下を実行します（実行位置が座標として保存されます）。

```text title="初期スポーン（離脱・切断・終了時の戻り先）"
/wajinro setstartspawn
```

```text title="ロビー（ゲーム開始前の待機場所）"
/wajinro setlobby
```

### 2. ステージの設定

このゲームは **ステージ（マップ）単位** でスポーン・フィールド・看板を管理します。`setspawn <ステージ>` を実行するとそのステージが作成されます。ステージ名は任意です（例: `village1`）。

```text title="ゲームスポーン（開始時のテレポート先）を現在地に設定"
/wajinro setspawn village1
```

```text title="フィールド範囲の角1（スケルトン出現範囲）を現在地に設定"
/wajinro setfield village1 1
```

```text title="フィールド範囲の角2を現在地に設定"
/wajinro setfield village1 2
```

```text title="ショップ村人の地点を現在地に追加（複数設置可）"
/wajinro setshop village1
```

```text title="最寄り（5ブロック以内）のショップ地点を解除"
/wajinro setshop delete
```

!!! tip "フィールド範囲とスケルトン"
    スケルトン（エメラルドの番人）は `setfield` で設定した角1・角2の範囲内にランダム出現します。フィールド範囲が未設定だとスケルトンが湧かず、エメラルドを稼げません。必ず2点とも設定してください。

### 3. 看板の設置

参加・離脱・開始・名前登録の看板を登録できます。看板を **見ながら（6ブロック以内）** 以下を実行します。テキストはプラグインが自動で書き込みます。

```text title="参加看板を登録（クリックで参加）"
/wajinro setsign join
```

```text title="離脱看板を登録（クリックで離脱）"
/wajinro setsign leave
```

```text title="開始看板を登録（クリックでそのステージを開始）"
/wajinro setsign start village1
```

```text title="名前看板を登録（占い・加護の対象指定に使用。最大人数分＝15枚推奨）"
/wajinro setsign name village1
```

```text title="視線先の看板の登録を解除（種類は自動判別・テキストも消去）"
/wajinro setsign delete
```

!!! warning "名前看板は人数分そろえる"
    名前看板は占い・騎士の加護の対象指定に使われます。**参加人数より少ないと、不足分のプレイヤーを占い対象にできません**（開始時に警告が出ます）。最大人数（15人）を想定して15枚設置しておくと安全です。開始時に名前看板とショップが不足していると警告が表示されます。

### 4. 設定状況の確認

```text title="地点・看板・ステージ・配役・Discordの設定状況を確認（全員可）"
/wajinro status
```

未設定の項目は赤色で表示されます。`setspawn`・`setfield`・`setstartspawn`・`setlobby` のいずれかが未設定だとゲームを開始できません。

## 配役・時間・人数の設定

config を直接編集しなくても、コマンドまたは管理本（`/wajinro book`）で変更できます。変更は即時保存されます。

```text title="配役を自動（人数に応じた推奨配役）にする（既定）"
/wajinro setrole auto
```

```text title="人狼の人数を手動設定（1〜3）。以降は手動モードになる"
/wajinro setrole wolf 2
```

```text title="共犯者の人数を手動設定（0〜1）"
/wajinro setrole accomplice 1
```

```text title="吸血鬼を手動設定（0 / random / 1）"
/wajinro setrole vampire random
```

```text title="狼憑きを手動設定（0 / random / 1）"
/wajinro setrole possessed 0
```

```text title="昼の長さ（秒）を設定（day / night / firstday）"
/wajinro settime day 120
```

```text title="夜の長さ（秒）を設定"
/wajinro settime night 120
```

```text title="初日の昼の長さ（秒）を設定"
/wajinro settime firstday 30
```

```text title="開始最低人数を設定（1〜15。テスト時は1に）"
/wajinro setmin 6
```

```text title="ゲーム管理本を入手（価格・配役・ver設定・タイマーをGUIで変更／OP専用）"
/wajinro book
```

!!! note "配役の自動表（6〜15人）"
    自動配役では次の内訳になります（村人／人狼／共犯者／吸血鬼／狼憑き）。6人=4/1/1/0/0、7人=4/2/1/0/0、8人=5/2/1/0/0、9人=6/2/1/0/0、10人=6/2/1/1/0、11人=7/2/1/1/0、12人=8/2/1/1/0、13人=8/3/1/1/0、14人=8/3/1/1/1、15人=9/3/1/1/1。最低人数を6人未満に下げてテストする場合、6人未満では人狼1人のみのフォールバック配役になります。

## config.yml 主要項目

同梱の `config.yml` の既定値と、各キーの意味は次のとおりです。

### 配役（`roles`）

```yaml
roles:
  mode: auto
  manual:
    wolf: 2
    accomplice: 1
    vampire: random
    possessed: "0"
```

| キー | 既定値 | 説明 |
|---|---|---|
| `roles.mode` | `auto` | `auto`＝人数に応じた推奨配役 / `manual`＝下記の手動設定 |
| `roles.manual.wolf` | 2 | 人狼の人数（1〜3にクランプ） |
| `roles.manual.accomplice` | 1 | 共犯者の人数（0〜1にクランプ） |
| `roles.manual.vampire` | `random` | 吸血鬼（`0` / `random`（50%） / `1`） |
| `roles.manual.possessed` | `0` | 狼憑き（`0` / `random`（50%） / `1`） |

### ゲーム設定（`settings`）

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.min-players` | 6 | 開始最低人数（1〜15。テスト時は1に） |
| `settings.first-day-seconds` | 30 | 初日の昼の長さ（秒／最小5） |
| `settings.day-seconds` | 120 | 昼の長さ（秒／最小10） |
| `settings.night-seconds` | 120 | 夜の長さ（秒／最小10） |
| `settings.skeleton.max-alive` | 10 | スケルトン同時出現の上限（1〜50） |
| `settings.skeleton.spawn-interval` | 8 | スケルトンの出現間隔（秒／最小1） |
| `settings.skeleton.emerald-chance` | 0.5 | スケルトン撃破時のエメラルド獲得確率（0.0〜1.0） |
| `settings.spectator-mode` | `spectator` | 死亡者の観戦方式。`spectator` / `adventure`（統合版で観戦が不安定な場合） |
| `settings.werewolf-axe-mode` | `ver2` | 人狼の斧。`ver1`＝昼の回数制限なし / `ver2`＝1回の昼に1本 |
| `settings.seer-mode` | `ver1` | 占い師の心。`ver1`＝1夜複数回可 / `ver2`＝1夜1回 |
| `settings.knight-prayer-mode` | `ver1` | 騎士の祈り。`disabled` / `ver1`＝夜間全ダメージ防御(1夜1回) / `ver2`＝弓斧を1回防ぐ(複数回可) / `ver3`＝弓斧を1回防ぐ(1夜1回) |
| `settings.grudge-spear-enabled` | `true` | 怨念の槍をショップに並べるか |
| `settings.swift-potion-enabled` | `true` | 俊敏ポーションをショップに並べるか |

### アイテム価格（`prices`）

すべてエメラルド数。管理本（`/wajinro book`）からも変更できます（1〜64にクランプ）。

| キー | 既定値 | アイテム |
|---|---|---|
| `prices.bow` | 2 | 一撃弓 |
| `prices.arrow` | 2 | 矢 |
| `prices.stun-grenade` | 2 | スタングレネード |
| `prices.skeleton-sword` | 4 | スケ狩り剣 |
| `prices.grudge-spear` | 3 | 怨念の槍 |
| `prices.werewolf-axe` | 3 | 人狼の斧（人狼専用） |
| `prices.steak` | 1 | ステーキ（5個セット） |
| `prices.swift-potion` | 1 | 俊敏ポーション |
| `prices.invisibility-potion` | 3 | 透明化ポーション |
| `prices.seer-heart` | 5 | 占い師の心 |
| `prices.medium-ash` | 4 | 霊媒師の遺灰 |
| `prices.knight-prayer` | 4 | 騎士の祈り |
| `prices.knight-blessing` | 2 | 騎士の加護 |
| `prices.accomplice-eye` | 4 | 共犯者の目（共犯者専用） |
| `prices.holy-cross` | 2 | 聖なる十字架（吸血鬼のいる試合のみ販売） |
| `prices.providence-eye` | 2 | プロビデンスの眼光 |
| `prices.revelation-charm` | 2 | 天啓の呪符 |

### Discord連携（`discord`）

```yaml
discord:
  enabled: false
  bot-token: ""
  guild-id: ""
  link-channel-id: ""
  voice-channels: []
```

| キー | 既定値 | 説明 |
|---|---|---|
| `discord.enabled` | `false` | Discord連携（夜間VCミュート）の有効化 |
| `discord.bot-token` | （空） | Discord BOT のトークン |
| `discord.guild-id` | （空） | Discord サーバー（ギルド）のID |
| `discord.link-channel-id` | （空） | リンク用メッセージの投稿先チャンネルID（`/wajinro setlinkchannel` でも設定可） |
| `discord.voice-channels` | `[]` | ミュート対象VCのID一覧（`/wajinro setvc add` でも編集可） |

!!! note "地点・看板は config.yml 内に自動保存"
    初期スポーン・ロビー・各ステージのスポーン／フィールド／開始看板／名前看板／ショップ、参加看板・離脱看板の座標は、コマンド実行時に `config.yml`（`default-spawn` / `lobby-spawn` / `sign` / `leave-sign` / `stages.<ステージ>.*` など）へ自動保存されます。別ファイル（locations.yml 等）は使いません。看板のクリック判定は保存座標との一致で行うため、手書きの看板は機能しません。

## ゲーム管理本（設定メニューGUI）

config.yml を直接編集しなくても、ゲーム内の **チェスト型GUI** で価格・配役・バリエーション・タイマーをまとめて変更できる管理者向けアイテムです。**設定はクリックした瞬間に `config.yml` へ保存**され、reload は不要です（`config.yml` を手書きで編集した場合のみサーバー再起動が必要）。

### 入手と使い方

```text title="ゲーム管理本を入手（OP＝wajinro.admin 専用）"
/wajinro book
```

入手できるのは **エンチャントの本（見た目）** で、名前は金色の「ゲーム管理本」、説明文に「右クリックで設定メニューを開く／管理者専用」と入ります。手に持って **右クリック**（空中・ブロックどちらでも可。ただし看板を右クリックした場合は占い・加護の処理が優先され開きません）すると設定メニューが開きます。ゲーム進行中でなくても使用できます。

!!! warning "OP専用（入手と操作で条件が異なる）"
    `/wajinro book` でのアイテム入手には `wajinro.admin`（既定でOP）が必要です。加えて **本を右クリックしてメニューを開く操作・メニュー内のクリック操作は「OP（`isOp`）であること」が条件** です。OPでないプレイヤーが本を持って右クリックすると「管理者専用アイテムです。」と表示され、メニューは開きません。本を配布する際は取り扱いに注意してください。

!!! note "なぜ本のページGUIでなくチェストGUIなのか"
    設定メニューは通常の「本を開く」ページGUIではなく、**チェスト型のインベントリGUI** で実装されています。これは **統合版（Bedrock）／Geyser 経由の接続でも確実に動作させるため** です。同じ理由で、死亡者の観戦方式にも統合版向けの `adventure` フォールバックが用意されています（後述のバリエーション設定）。

### メニュー構成

右クリックで最初に開く画面（タイトル「ゲーム管理本」）から、4種類の設定画面へ分岐します。各設定画面の右下スロットの **「戻る」** でメイン画面へ、メイン画面の **「閉じる」** でGUIを閉じます。ページ送りではなく、メイン → 各設定画面の2階層構成です。

| メイン画面のボタン | 開く設定画面 | 変更対象のconfigキー |
|---|---|---|
| 価格設定 | 購入アイテム17種の価格 | `prices.*` |
| 配役設定 | 人狼・共犯者・吸血鬼・狼憑き | `roles.*` |
| バリエーション設定 | 斧 / 占い / 騎士の祈り / 槍 / 俊敏 / 観戦方式 | `settings.*` |
| タイマー設定 | 初日昼・昼・夜の秒数・最低人数 | `settings.*` |

### ① 価格設定（`prices.*`）

購入アイテム17種が並び、各アイテムを **左クリックで +1／右クリックで −1** エメラルド。価格は **1〜64** にクランプされ、0以下・65以上にはできません。既定価格は次のとおりです（同梱 `config.yml`）。

| アイテム | configキー | 既定価格 |
|---|---|---|
| 一撃弓 | `prices.bow` | 2 |
| 矢 | `prices.arrow` | 2 |
| スタングレネード | `prices.stun-grenade` | 2 |
| スケ狩り剣 | `prices.skeleton-sword` | 4 |
| 怨念の槍 | `prices.grudge-spear` | 3 |
| 人狼の斧（人狼専用） | `prices.werewolf-axe` | 3 |
| ステーキ | `prices.steak` | 1 |
| 俊敏ポーション | `prices.swift-potion` | 1 |
| 透明化ポーション | `prices.invisibility-potion` | 3 |
| 占い師の心 | `prices.seer-heart` | 5 |
| 霊媒師の遺灰 | `prices.medium-ash` | 4 |
| 騎士の祈り | `prices.knight-prayer` | 4 |
| 騎士の加護 | `prices.knight-blessing` | 2 |
| 共犯者の目（共犯者専用） | `prices.accomplice-eye` | 4 |
| 聖なる十字架 | `prices.holy-cross` | 2 |
| プロビデンスの眼光 | `prices.providence-eye` | 2 |
| 天啓の呪符 | `prices.revelation-charm` | 2 |

### ② 配役設定（`roles.*`）

各項目は **クリックするたびに値が循環** します（この画面では左右クリックの区別はありません）。**人狼・共犯者・吸血鬼・狼憑きのいずれかを操作すると、自動的に `roles.mode` が `manual`（手動）へ切り替わります**。「配役モード」ボタンで `auto`（人数に応じた推奨配役）へ戻せます。

| 項目 | クリック時の循環 | configキー | 既定値 | 取り得る値 |
|---|---|---|---|---|
| 配役モード | 自動 ⇔ 手動 | `roles.mode` | `auto` | `auto` / `manual` |
| 人狼 | 1 → 2 → 3 → 1 | `roles.manual.wolf` | 2 | 1〜3 |
| 共犯者 | 0 ⇔ 1 | `roles.manual.accomplice` | 1 | 0 / 1 |
| 吸血鬼 | 0 → 50%（random）→ 1 → 0 | `roles.manual.vampire` | `random` | `0` / `random`（50%）/ `1` |
| 狼憑き | 0 → 50%（random）→ 1 → 0 | `roles.manual.possessed` | `0` | `0` / `random`（50%）/ `1` |

!!! note "手動モード時のみ反映"
    人狼・共犯者・吸血鬼・狼憑きの人数は **`roles.mode` が `manual` のときの割り当て** に使われます。`auto` のままだと表の値は使われず、下の「自動配役表」に従います。GUIで人数を触ると自動的に手動へ切り替わる点に注意してください。

### ③ バリエーション設定（`settings.*`）

ルールの細部を切り替えます。「人狼の斧」「占い師の心」「観戦方式」は2択トグル、「騎士の祈り」は4段階循環、「怨念の槍」「俊敏ポーション」は有効/無効トグルです（いずれもクリックで切替、左右の区別なし）。

| 項目 | クリック時の循環 | configキー | 既定値 | 各選択肢の意味 |
|---|---|---|---|---|
| 人狼の斧 | ver1 ⇔ ver2 | `settings.werewolf-axe-mode` | `ver2` | `ver1`＝昼の使用回数制限なし／`ver2`＝1回の昼に1人1本（夜は無制限） |
| 占い師の心 | ver1 ⇔ ver2 | `settings.seer-mode` | `ver1` | `ver1`＝残回数分、1夜に複数回占える／`ver2`＝1夜に1回のみ |
| 騎士の祈り | disabled → ver1 → ver2 → ver3 → disabled | `settings.knight-prayer-mode` | `ver1` | `disabled`＝無効／`ver1`＝使った夜は永続・全ダメージ防御（1夜1回）／`ver2`＝1回ごとに弓/斧を1回防御・1夜に複数回可／`ver3`＝1夜1回・弓/斧を1回防御 |
| 怨念の槍 | 有効 ⇔ 無効 | `settings.grudge-spear-enabled` | `true` | 有効時のみショップに並ぶ |
| 俊敏ポーション | 有効 ⇔ 無効 | `settings.swift-potion-enabled` | `true` | 有効時のみショップに並ぶ |
| 観戦方式 | spectator ⇔ adventure | `settings.spectator-mode` | `spectator` | `spectator`＝スペクテイターモード／`adventure`＝透明＋飛行（統合版で観戦が不安定な場合のフォールバック） |

### ④ タイマー設定（`settings.*`）

昼夜の長さと開始最低人数を調整します。時間は **左クリックで +10秒／右クリックで −10秒**、最低人数は **左クリックで +1／右クリックで −1**。

| 項目 | 操作 | configキー | 既定値 | 下限 / 上限 |
|---|---|---|---|---|
| 初日の昼 | 左 +10秒 / 右 −10秒 | `settings.first-day-seconds` | 30秒 | 下限5秒／上限なし |
| 昼の長さ | 左 +10秒 / 右 −10秒 | `settings.day-seconds` | 120秒 | 下限10秒（表示値）／上限なし |
| 夜の長さ | 左 +10秒 / 右 −10秒 | `settings.night-seconds` | 120秒 | 下限10秒（表示値）／上限なし |
| 最低人数 | 左 +1 / 右 −1 | `settings.min-players` | 6人 | 1〜15 |

!!! info "秒数の下限について"
    書き込み時の下限は3項目とも5秒ですが、`day-seconds`・`night-seconds` は読み出し時に最低10秒として扱われるため、GUI上は10秒を下回って表示されません（`first-day-seconds` は5秒まで下げられます）。テストプレイでは最低人数を1に下げると1人でも開始できます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `wajinro.admin` | OP | セットアップ・管理コマンドすべて／管理本の使用 |

!!! note "プレイヤー向けコマンドは権限不要"
    `/wajinro join`・`leave`・`start`・`status`・`link`・`unlink` は **権限不要で全プレイヤーが使えます**（`join`・`leave`・`start` は看板と同等）。それ以外の設定・管理コマンドは `wajinro.admin`（OP）が必要です。

## 管理コマンド一覧

| コマンド | 権限 | 説明 |
|---|---|---|
| `/wajinro join` / `leave` / `start [ステージ]` / `status` | 全員 | 参加 / 離脱 / 開始 / 設定状況の確認 |
| `/wajinro link` / `unlink` | 全員 | Discord連携 / 解除（連携有効時のみ） |
| `/wajinro setstartspawn` | admin | 初期スポーン（戻り先）を現在地に設定 |
| `/wajinro setlobby` | admin | ロビー地点を現在地に設定 |
| `/wajinro setspawn <ステージ>` | admin | ゲームスポーンを設定（ステージ作成を兼ねる） |
| `/wajinro setfield <ステージ> <1\|2>` | admin | フィールド範囲の角を設定 |
| `/wajinro setshop <ステージ\|delete>` | admin | ショップ地点を追加 / 最寄りを解除 |
| `/wajinro setsign <join\|leave\|start <ス>\|name <ス>\|delete>` | admin | 視線先の看板を各用途で登録 / 解除 |
| `/wajinro setrole <auto\|wolf\|accomplice\|vampire\|possessed> [値]` | admin | 配役を設定（auto以外は手動モードに） |
| `/wajinro settime <day\|night\|firstday> <秒>` | admin | 各フェーズの時間を設定 |
| `/wajinro setmin <人数>` | admin | 開始最低人数を設定（1〜15） |
| `/wajinro book` | admin | ゲーム管理本を入手（GUIで価格・配役・ver・時間を変更） |
| `/wajinro stop` | admin | ゲームを強制終了する |
| `/wajinro setvc <add\|remove\|list> [ID]` | admin | ミュート対象VCの追加 / 削除 / 一覧 |
| `/wajinro setlinkchannel <ID>` | admin | リンク用メッセージの投稿先チャンネルを設定 |
| `/wajinro linkpost` | admin | リンク用のボタン付きメッセージを投稿する |
| `/wajinro unmuteall` | admin | 登録VCの全員のミュートを解除（保険） |

!!! note "reload / save コマンドはありません"
    設定コマンドは実行時に自動保存されるため `/wajinro save` はありません。また設定の再読み込み（`/wajinro reload`）もありません。`config.yml` を直接編集した場合はサーバーを再起動してください。

## Discord連携（夜間VCミュート）

夜になると、連携済みプレイヤーの Discord VC を自動でミュートし、朝に解除します。

1. Discord Developer Portal で BOT を作成し、トークンを取得する。
2. BOT に「メンバーをミュート」「チャンネルを見る」「メッセージを送信」権限を付与し、サーバーへ招待する（Privileged Intent は不要）。
3. `config.yml` の `discord.enabled: true`／`bot-token`／`guild-id` を設定してサーバーを再起動する。
4. `/wajinro setlinkchannel <チャンネルID>` で投稿先を設定し、`/wajinro linkpost` でボタン付きメッセージを投稿する（1回でOK）。
5. `/wajinro setvc add <VCチャンネルID>` でミュート対象のVCを登録する（複数可）。
6. プレイヤーは `/wajinro link` で表示される6桁コードを、Discordのリンクメッセージのボタンから5分以内に入力して連携する。

!!! tip "ミュートが残ってしまったとき"
    何らかの理由でミュートが解除されない場合は `/wajinro unmuteall` で登録VCの全員のミュートを一括解除できます。

## 運営の流れ

1. プレイヤーが `/wajinro join` か参加看板でロビーに参加するのを待つ（既定で最低6人）。
2. `/wajinro start <ステージ>`（または開始看板）でゲームを開始する。役職割り当て・フィールドへのテレポート・ショップ設置・名前看板の受付開始が自動で行われます。
3. 開始したら各プレイヤーに名前看板への登録を促す（未登録者は初日の夜に自動割当）。
4. 昼夜サイクルが自動進行し、勝敗が決まると役職公開のあと約8秒でロビーへ戻ります。
5. 異常時は `/wajinro stop` で強制終了できます。

## トラブルシューティング

??? failure "「未設定のため開始できません」と出る"
    `/wajinro status` で赤い（未設定の）項目を確認してください。ゲームスポーン（`setspawn`）・フィールド範囲（`setfield`）・ロビー（`setlobby`）・初期スポーン（`setstartspawn`）がすべて設定済みである必要があります。

??? failure "最低人数に達していない／16人以上で開始できない"
    参加者が `settings.min-players`（既定6人）以上か確認してください。テスト時は `/wajinro setmin 1` で下げられます。**最大は15人** で、16人以上には対応していません。

??? failure "スケルトン（エメラルドの番人）が出現しない"
    `/wajinro setfield <ステージ> 1` と `2` の両方が設定されているか確認してください。フィールド範囲が未設定だとスケルトンは湧きません。スケルトンは **夜のみ** 出現します。

??? failure "占い・加護が使えない／対象を選べない"
    名前看板が `/wajinro setsign name <ステージ>` で人数分設置されているか確認してください。プレイヤーは名前看板を右クリックして名前を登録する必要があります（未登録者は初日の夜に自動登録）。占いは夜のみ使用できます。

??? failure "ショップが開けない・アイテムが並ばない"
    ショップ地点が `/wajinro setshop <ステージ>` で設定されているか確認してください（未設定だと開始時に警告が出ます）。聖なる十字架は吸血鬼のいる試合のみ、怨念の槍・俊敏ポーションは対応する設定が有効な場合のみ並びます。

??? failure "Discord BOTが接続しない・ミュートされない"
    `config.yml` の `bot-token`／`guild-id` とコンソールの警告を確認してください。ミュートされないプレイヤーは `/wajinro link` で連携済みか確認します（開始時に未連携者が警告表示されます）。ミュートが残った場合は `/wajinro unmuteall`。

??? failure "プラグインが赤色（読み込み失敗）で表示される"
    サーバーのJavaバージョンを確認してください。Paper 26.1 系は Java 25 が必要です。

??? failure "config を書き換えたのに反映されない"
    本プラグインに reload コマンドはありません。`config.yml` を直接編集した場合はサーバーを再起動してください。ゲーム内コマンドや管理本での変更は即時反映されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← WaJinro 概要へ](index.md){ .md-button }
