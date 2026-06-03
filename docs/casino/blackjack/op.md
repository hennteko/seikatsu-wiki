<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ブラックジャック ― OP・運営ガイド { .page-op #blackjack-op }

ブラックジャック（CasinoPlugin の blackjack モジュール）の有効化・地点セットアップ・看板の作り方・権限・管理コマンドをまとめます。CasinoPlugin 全体の導入手順は [CasinoPlugin OP ページ](../casino-plugin/op.md) を参照してください。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `blackjack` |
| 親プラグイン | CasinoPlugin（`jp.casinoplugin.CasinoPlugin`） |
| メインコマンド | `/blackjack <join\|leave\|bet\|start\|stop\|setup>` |
| 設定ファイル | `plugins/CasinoPlugin/modules/blackjack.yml` |
| 有効化フラグ | `plugins/CasinoPlugin/config.yml` の `modules.blackjack.enabled` |
| 依存モジュール | `bank`（エメラルド口座・共通通貨。必須） |
| 最大参加人数 | 6人（看板参加時の上限） |

!!! info "blackjack は CasinoPlugin のモジュールです"
    ブラックジャックは旧 BlackJack2 プラグインを CasinoPlugin に統合した **blackjack モジュール** です。専用 jar の導入は不要で、CasinoPlugin 本体に内蔵されています。賭け金処理は `bank` モジュールが提供するエメラルド口座（`EmeraldAPI`）を経由するため、`bank` モジュールが有効である必要があります。

## 有効化

ブラックジャックを使うには、CasinoPlugin の `config.yml` でモジュールを有効化します。

1. CasinoPlugin を導入してサーバーを一度起動し、`plugins/CasinoPlugin/config.yml` を生成する。
2. `config.yml` の `modules:` ブロックで `blackjack` を有効にする。

    ```yaml
    modules:
      bank:
        enabled: true     # 共通通貨。必須
      blackjack:
        enabled: true     # ブラックジャックを有効化
    ```

3. サーバーを再起動（または CasinoPlugin をリロード）すると、`plugins/CasinoPlugin/modules/blackjack.yml` が自動生成される。

!!! warning "bank モジュールが前提です"
    blackjack モジュールは起動時に `EmeraldAPI` が利用可能かを確認します。`bank` モジュールが無効だと、blackjack の起動は失敗します（`EmeraldAPI not bound; bank module required before blackjack`）。`modules.bank.enabled` は必ず `true` にしてください。専用 jar の追加導入は不要です。

## blackjack.yml 設定項目

`plugins/CasinoPlugin/modules/blackjack.yml` には **地点情報（spawn / lobby）** が保存されます。これらは手書きせず、後述の `/blackjack setup` コマンドで設定してください（コマンド実行で自動的に書き込まれます）。

| キー | 内容 |
|---|---|
| `locations.spawn.world` | spawn 地点のワールド名 |
| `locations.spawn.x` / `y` / `z` | spawn 地点の座標 |
| `locations.spawn.yaw` / `pitch` | spawn 地点の向き |
| `locations.lobby.world` | lobby 地点のワールド名 |
| `locations.lobby.x` / `y` / `z` | lobby 地点の座標 |
| `locations.lobby.yaw` / `pitch` | lobby 地点の向き |

| 地点 | 役割 |
|---|---|
| `spawn` | ロビー退出時・ゲーム中の途中離脱時のテレポート先 |
| `lobby` | 看板でのロビー参加時、およびゲーム終了後のテレポート先 |

!!! note "地点はコマンドで設定します"
    `blackjack.yml` の初期状態では `locations` の中身はコメントアウトされた空の状態です。`/blackjack setup spawn` / `/blackjack setup lobby` を実行すると、その地点が自動的にファイルへ書き込まれます。座標を手書きする必要はありません。賭け金・配当などの数値設定はモジュール側で固定で、`blackjack.yml` に項目はありません。

## セットアップ手順

1. **モジュールを有効化** — 上記「有効化」の手順で `modules.blackjack.enabled: true` にする。
2. **spawn 地点を設定** — 退出・離脱時に戻したい場所に立ち、`/blackjack setup spawn` を実行する。
3. **lobby 地点を設定** — ロビー参加者を集めたい場所に立ち、`/blackjack setup lobby` を実行する。
4. **看板を設置する** — 会場に参加用の看板を設置する（下記「看板の作り方」を参照）。
5. **動作確認** — `/blackjack join` でロビー参加、`/blackjack bet` でベット、`/blackjack start` で開始してテストする。

!!! tip "地点設定はその場の座標が保存されます"
    `/blackjack setup spawn` / `setup lobby` は、**コマンドを実行したプレイヤーの現在位置・向き** をそのまま地点として保存します。設定したい場所に正しい向きで立ってから実行してください。

### 看板の作り方

看板を設置して以下のように入力すると、自動で色付け・整形されます。看板の作成には OP 権限（または `blackjack.setup`）が必要です。

| 看板の種類 | 1行目 | 2行目 | 3行目 | はたらき |
|---|---|---|---|---|
| 参加看板（lobby） | `[BlackJack]` | `lobby` | （空欄） | クリックでロビー参加。lobby 地点へテレポート。人数表示（`0/6人`）が自動付与 |
| 退出看板（leave） | `[BlackJack]` | `leave` | （空欄） | クリックでロビー退出。spawn 地点へテレポート |
| ベット看板（bet） | `[BlackJack]` | `bet` | `100`（金額） | クリックでロビー参加者全員のベット額を変更 |

!!! note "看板の挙動メモ"
    参加看板（lobby）は4行目に現在の参加人数を表示し、人数が変わると自動更新されます。看板からの参加は **最大6人** で、満員時はクリックしても参加できません。ベット看板は3行目に数字（ベット額）を入力します。看板タグは大文字小文字を区別しません（`[BlackJack]`）。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/blackjack start` | OP または `blackjack.start` | 待機中のゲームを開始する。ロビーに1人以上必要 |
| `/blackjack stop` | OP または `blackjack.stop` | 進行中のゲームを強制終了する |
| `/blackjack setup spawn` | OP または `blackjack.setup` | 現在地を spawn 地点に設定する |
| `/blackjack setup lobby` | OP または `blackjack.setup` | 現在地を lobby 地点に設定する |
| `/blackjack join [プレイヤー名]` | OP（`blackjack.start`/`stop`/`setup` のいずれか） | ロビーに参加（プレイヤーは `[BlackJack]` 看板から参加） |
| `/blackjack leave [プレイヤー名]` | OP（同上） | ロビーから退出（プレイヤーは `[BlackJack] leave` 看板から退出） |
| `/blackjack bet <金額>` | OP（同上） | ロビー参加者全員のベット額を設定する（プレイヤーは `[BlackJack] bet` 看板から設定） |

!!! note "ゲーム開始時の挙動"
    `/blackjack start` を実行すると、ロビー参加者ごとに「ベット額が設定されているか」「口座残高が足りているか」を確認します。未設定・残高不足のプレイヤーは自動的にロビーから外され、有効な参加者の賭け金がポット（サーバー口座）へ集められてゲームが始まります。有効な参加者が0人の場合はゲームが中止されます。

## 権限ノード

`plugin.yml` で定義されている blackjack 関連の権限です。いずれも既定は **op**。

| 権限 | 既定 | 用途 |
|---|---|---|
| `blackjack.start` | op | ブラックジャックを開始する |
| `blackjack.stop` | op | ブラックジャックを強制終了する |
| `blackjack.setup` | op | ブラックジャックの地点設定・看板作成を行う |

!!! note "/blackjack は原則 OP 専用"
    `/blackjack` 系のサブコマンド（`join` / `leave` / `bet` / `start` / `stop` / `setup`）は、すべて `blackjack.start` / `blackjack.stop` / `blackjack.setup` のいずれか、もしくは OP 権限を持っていないと実行できません。プレイヤーは `[BlackJack]` 看板（`lobby` / `leave` / `bet`）を経由して参加・退出・ベットを行います。

## トラブルシューティング

??? failure "`/blackjack` コマンドが「不明なコマンド」になる"
    blackjack モジュールが無効化されている可能性があります。`config.yml` の `modules.blackjack.enabled` が `true` になっているか確認してください。無効なモジュールはコマンドもリスナーも登録されません。

??? failure "起動ログに「EmeraldAPI not bound」と出てモジュールが起動しない"
    blackjack は `bank` モジュール（エメラルド口座）に依存します。`modules.bank.enabled` が `true` になっているか確認してください。bank が無効だと blackjack の起動は失敗します。

??? failure "看板を設置しても自動整形されない / 「権限がありません」と出る"
    看板の作成には OP 権限または `blackjack.setup` が必要です。また、1行目は `[BlackJack]`、2行目は `lobby` / `leave` / `bet` のいずれかを正しく入力してください。bet 看板は3行目に有効な数字（正の整数）が必要です。

??? failure "看板や `/blackjack join` でロビーに参加できない"
    ゲームが進行中（IN_GAME）の場合、ロビーへの新規参加はできません。`/blackjack stop` で現在のゲームを終了させてから参加してください。また、看板参加は最大6人で、満員時は参加できません。

??? failure "`/blackjack start` でゲームが始まらない"
    ロビーに有効な参加者が1人以上必要です。各参加者は **ベット額が設定済み** かつ **口座残高がベット額以上** である必要があります。条件を満たさないプレイヤーは開始時に自動でロビーから外され、全員外れるとゲームは中止されます。

??? failure "退出・終了時にプレイヤーがテレポートされない"
    spawn 地点 / lobby 地点が未設定の可能性があります。`/blackjack setup spawn` と `/blackjack setup lobby` で両方を設定してください。地点が未設定の場合はテレポートがスキップされます（ログ・チャットに通知されます）。

??? failure "賭け金や配当が口座に反映されない"
    賭け金処理は `bank` モジュールの `EmeraldAPI` を経由します。`bank` モジュールが有効か確認してください。賭け金はゲーム開始時にサーバー口座へ集められ、終了時に勝者へ支払われます（端数はサーバー側に残ります）。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ブラックジャック概要へ](index.md){ .md-button }
