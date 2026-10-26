<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ブラックジャック ― OP・運営ガイド { .page-op #blackjack-op }

ブラックジャック（CasinoPlugin の blackjack モジュール）の有効化・地点セットアップ・看板の作り方・権限・管理コマンドをまとめます。CasinoPlugin 全体の導入手順は [CasinoPlugin OP ページ](../casino-plugin/op.md) を参照してください。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `blackjack` |
| 親プラグイン | CasinoPlugin（`jp.casinoplugin.CasinoPlugin`） |
| メインコマンド | `/blackjack [help\|join\|leave\|bet <金額>\|start\|status\|action\|doubleup\|solo [卓ID\|stats [名前]\|ranking]]`<br>OP: `stop [multi\|solo [プレイヤー]]\|setsign\|setstartspawn\|setlobby\|setsolospawn\|setfield <1\|2>\|bet <金額> <プレイヤー>\|join/leave <プレイヤー>\|wand\|menu\|reload\|lobbyfix <プレイヤー> [force]` |
| 設定ファイル | `plugins/CasinoPlugin/modules/blackjack.yml` |
| 有効化フラグ | `plugins/CasinoPlugin/config.yml` の `modules.blackjack.enabled` |
| 依存モジュール | `bank`（エメラルド口座・共通通貨。必須） |
| 参加人数 | マルチ卓は `settings.min-players`〜`settings.max-players`（既定 1〜6、上限は 1〜10 で設定可）／ソロ卓は1人 |

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

`plugins/CasinoPlugin/modules/blackjack.yml` には **一般設定（settings）**・**ソロ卓設定（solo）** のほか、**地点情報（spawn / lobby / field1 / field2）** と **看板登録情報（signs.registry / signs.solo-registry）** が保存されます。地点・看板は手書きせず、後述の地点設定コマンド・看板登録コマンドで設定してください（コマンド実行で自動的に書き込まれます）。

### 一般設定（settings）・ソロ卓設定（solo）

| キー | 既定 | 内容 |
|---|---|---|
| `settings.min-players` | 1 | マルチ卓の最低開始人数（1 = 1人でも開始可） |
| `settings.max-players` | 6 | マルチ卓の最大参加人数（1〜10 の範囲） |
| `solo.menu-delay` | 3 | ハンド終了からメニューを開くまでの秒数 |
| `solo.decks` | 6 | シューのデッキ数（1〜8） |
| `solo.penetration` | 0.75 | シューをこの割合まで配ったら次のハンド前にシャッフル |
| `solo.dealer-hits-soft-17` | false | true = ソフト17でもディーラーが引く（プレイヤーにやや不利） |
| `solo.blackjack-payout` | 1.5 | ブラックジャックの配当（1.5 = 3:2） |
| `solo.allow-double` | true | 最初の2枚でダブルダウン可 |
| `solo.allow-split` | false | スプリットは未対応（true にしても無効・警告） |
| `solo.action-timeout-seconds` | 60 | 操作が無いと自動で STAND |
| `solo.dealer-draw-ticks` | 20 | ディーラーが1枚引く間隔（tick） |
| `solo.action-gui-all-players` | false | true = Java 版にも自分の番でアクションGUIを自動で開く（統合版は常に開く） |
| `solo.stats-save-interval-seconds` | 60 | 戦績（`blackjack_solo_stats.yml`）の保存間隔 |
| `solo.tables.<ID>.display` / `.min-bet` / `.max-bet` | ロー 10〜500 / ミドル 100〜5,000 / ハイ 1,000〜50,000 | ソロ卓の表示名とベット範囲（卓ID に「.」は使えません） |

### 地点・看板（自動保存）

| キー | 内容 |
|---|---|
| `locations.spawn.world` | spawn 地点のワールド名 |
| `locations.spawn.x` / `y` / `z` | spawn 地点の座標 |
| `locations.spawn.yaw` / `pitch` | spawn 地点の向き |
| `locations.lobby.world` | lobby 地点のワールド名 |
| `locations.lobby.x` / `y` / `z` | lobby 地点の座標 |
| `locations.lobby.yaw` / `pitch` | lobby 地点の向き |
| `locations.field1` / `field2` | ゲームエリアの対角2点（`/blackjack setfield <1\|2>` で設定） |
| `bet_options` | （任意）掛け金変更GUIに並べる金額リスト。未設定時は既定の18段階（1〜1,000,000）を使用 |
| `signs.registry` | 登録済み看板のリスト（`"world;x;y;z;種類"` 形式）。`/blackjack setsign` で自動登録・解除。手書き不要 |
| `signs.solo-registry` | ソロ卓看板のリスト（`/blackjack setsign solo [卓ID]` で自動登録）。手書き不要 |

| 地点 | 役割 |
|---|---|
| `spawn` | ロビー退出時・ゲーム中の途中離脱時のテレポート先 |
| `lobby` | 看板でのロビー参加時、およびゲーム終了後のテレポート先 |
| `field1` / `field2` | ゲームエリア（卓）の範囲を表す対角2点 |

!!! note "地点はコマンドで設定します"
    `blackjack.yml` の初期状態では `locations` の中身はコメントアウトされた空の状態です。`/blackjack setstartspawn` / `/blackjack setlobby` / `/blackjack setsolospawn` / `/blackjack setfield <1|2>` を実行すると、その地点が自動的にファイルへ書き込まれます。座標を手書きする必要はありません。参加人数（`settings`）やソロ卓の数値（`solo`：デッキ数・配当・卓ごとのベット範囲など）は上の設定表のとおり `blackjack.yml` で調整できます。

!!! tip "掛け金GUIの金額を変えるには bet_options"
    `bet` 看板をクリックすると共通の **掛け金変更GUI** が開きます。並べる金額を変えたい場合は `blackjack.yml` に `bet_options`（数値リスト）を追加してください。未設定なら既定の18段階（1 / 5 / 10 / 25 / 50 / 100 / 250 / 500 / 1000 / 2500 / 5000 / 10000 / 25000 / 50000 / 100000 / 250000 / 500000 / 1000000）が使われます。

## セットアップ手順

**① モジュールを有効化** — 上記「有効化」の手順で `modules.blackjack.enabled: true` にする。

**② spawn / lobby / field 地点を設定** — 設定したい場所に立って以下を実行する。

```text title="spawn 地点（退出・離脱時の戻り先）を現在地に設定"
/blackjack setstartspawn
```

```text title="lobby 地点（参加者の集合場所）を現在地に設定"
/blackjack setlobby
```

```text title="ゲームエリアの角1を現在地に設定"
/blackjack setfield 1
```

```text title="ゲームエリアの角2を現在地に設定"
/blackjack setfield 2
```

```text title="ソロ卓（ディーラー戦）の地点を現在地に設定（未設定ならその場でメニューを開く）"
/blackjack setsolospawn
```

**③ 看板を設置する** — 会場に参加用の看板を設置する（下記「看板の作り方」を参照）。

**④ 動作確認** — `/blackjack join` でロビー参加、`/blackjack bet` でベット、`/blackjack start` で開始してテストする。

!!! tip "地点設定はその場の座標が保存されます"
    `/blackjack setstartspawn` / `setlobby` / `setfield <1|2>` は、**コマンドを実行したプレイヤーの現在位置・向き** をそのまま地点として保存します。設定したい場所に正しい向きで立ってから実行してください。

### 看板の作り方

看板を設置し、看板を見ながら（5ブロック以内）以下のコマンドを実行すると、自動で色付け・整形されます。看板の作成には OP 権限（または `blackjack.admin`）が必要です。

```text title="参加看板（join / lobby どちらでも可）"
/blackjack setsign join
```

```text title="退出看板"
/blackjack setsign leave
```

```text title="掛け金変更GUI看板"
/blackjack setsign bet
```

```text title="開始看板"
/blackjack setsign start
```

```text title="ソロ卓看板（卓IDを付けるとその卓を選んだ状態で開く）"
/blackjack setsign solo [卓ID]
```

```text title="看板の設定を解除"
/blackjack setsign delete
```

| 看板の種類 | はたらき |
|---|---|
| 参加看板（join / lobby） | クリックでロビー参加。lobby 地点へテレポート。人数表示（`0/6人`）が自動付与 |
| 退出看板（leave） | クリックでロビー退出。spawn 地点へテレポート |
| 掛け金看板（bet） | クリックで掛け金変更GUIを開き、ロビー参加者全員のベット額を変更 |
| 開始看板（start） | クリックでゲームを開始（**参加者なら誰でも可**。ロビー全員がベット済みのとき） |
| ソロ卓看板（solo） | クリックでソロ卓（ディーラー戦）のメニューを開く。卓ID 付きならその卓を選んだ状態 |

!!! note "bet 看板は掛け金変更GUIになりました"
    `setsign bet` は金額引数を取りません。設置した `bet` 看板をクリックすると共通の **掛け金変更GUI**（金額一覧）が開き、選んだ金額がロビー全員のベット額になります（旧仕様の `setsign bet <金額>` ・ `setstart` は廃止）。GUI に並ぶ金額は `blackjack.yml` の `bet_options` で変更できます。

!!! note "看板の挙動メモ"
    参加看板（join / lobby）は4行目に現在の参加人数を表示し、人数が変わると自動更新されます。看板からの参加は **最大6人** で、満員時はクリックしても参加できません。看板タグは大文字小文字を区別しません（`[BlackJack]`）。

## ゲーム運営コマンド

```text title="ゲームを開始（ロビーに1人以上必要）"
/blackjack start
```

```text title="進行中のゲームを強制終了（stop=全部／stop multi=マルチのみ／stop solo [名前]=ソロ）"
/blackjack stop [multi|solo [プレイヤー]]
```

```text title="ロビー全員のベット額を設定（例: 100エメラルド）"
/blackjack bet 100
```

```text title="プレイヤーをロビーに参加させる（名前省略時は自分）"
/blackjack join <プレイヤー名>
```

```text title="プレイヤーをロビーから退出させる（名前省略時は自分）"
/blackjack leave <プレイヤー名>
```

## 管理コマンド一覧

| コマンド | 権限 | 説明 |
|---|---|---|
| `/blackjack status` | 誰でも可（読み取り専用） | 地点設定（spawn / lobby / field）・看板設定の有無と現在のロビー人数を表示する |
| `/blackjack action` | 誰でも可（プレイヤーのみ） | 自分の番の HIT / STAND 操作画面（GUI）を開く。棒が使えない統合版などの代替 |
| `/blackjack start` | 全員可（看板と同じ） | 待機中のゲームを開始する。ロビーに1人以上必要。非 OP は全参加者がベット済みのときのみ可（OP は未ベット者を外して開始） |
| `/blackjack solo [卓ID\|stats [名前]\|ranking]` | 誰でも可 | ソロ卓（ディーラー戦）のメニュー・戦績・ランキング |
| `/blackjack doubleup` | 誰でも可 | ソロ卓で勝ったあとのダブルアップ画面を開き直す |
| `/blackjack stop [multi\|solo [プレイヤー]]` | OP | 進行中のゲームを強制終了する（引数なし=全部／`multi`=マルチのみ／`solo [名前]`=ソロ全部または指定者） |
| `/blackjack setstartspawn` | OP | 現在地を spawn 地点に設定する |
| `/blackjack setlobby` | OP | 現在地を lobby 地点に設定する |
| `/blackjack setsolospawn` | OP | 現在地をソロ卓の地点に設定する（未設定ならその場でメニュー） |
| `/blackjack setfield <1\|2>` | OP | ゲームエリアの角1/角2を現在地に設定する |
| `/blackjack join [プレイヤー名]` | 自分対象は誰でも可／他人指定は OP | ロビーに参加（プレイヤーは `[BlackJack]` 看板から参加） |
| `/blackjack leave [プレイヤー名]` | 自分対象は誰でも可／他人指定は OP | ロビーから退出（プレイヤーは `[BlackJack] leave` 看板から退出） |
| `/blackjack bet <金額> [プレイヤー]` | 自分/全員ぶんは誰でも可／他人指定は OP | ロビー参加者全員のベット額を設定する（プレイヤーは `[BlackJack] bet` 看板から設定） |
| `/blackjack setsign <join\|leave\|bet\|start\|solo [卓ID]\|delete>` | OP | 視線先の看板を指定種別の看板として登録/解除する（`bet` は掛け金変更GUI看板、金額引数なし。`delete` で設定解除。`remove` も同義） |
| `/blackjack wand` / `/blackjack menu` | OP | 管理ツールの取得／管理メニューを開く |
| `/blackjack reload` | OP | `blackjack.yml` を再読み込みする |
| `/blackjack lobbyfix <プレイヤー> [force]` | OP | ロビーで預かった持ち物を手動で復元する |

!!! note "ゲーム開始時の挙動"
    `/blackjack start` を実行すると、ロビー参加者ごとに「ベット額が設定されているか」「口座残高が足りているか」を確認します。未設定・残高不足のプレイヤーは自動的にロビーから外され、有効な参加者の賭け金がポット（サーバー口座）へ集められてゲームが始まります。有効な参加者が0人の場合はゲームが中止されます。

## 権限ノード

`plugin.yml` で定義されている blackjack 関連の権限です。

| 権限 | 既定 | 用途 |
|---|---|---|
| `blackjack.admin` | op | ブラックジャックの看板・地点・強制停止・管理ツール/メニュー・lobbyfix など管理系の全権限 |

!!! note "権限ノードは `blackjack.admin` の1本"
    管理系の可否判定はすべて「OP または `blackjack.admin`」の1本に統一されています（旧 `blackjack.start` / `blackjack.stop` / `blackjack.setup` は廃止・削除済みで、コードからは参照されません）。`stop` やセットアップ系（`setsign` ・ `set*`）、`bet <金額> <プレイヤー>`、`join` / `leave` での他プレイヤー指定は OP／`blackjack.admin` が必要です。一方 `/blackjack join` ・ `/blackjack leave`（自分対象）・ `/blackjack bet <金額>`（ロビー在籍者が全員ぶん設定）・ `/blackjack start` ・ `/blackjack status` ・ `/blackjack action` ・ `/blackjack solo` ・ `/blackjack doubleup` は **一般プレイヤーでも実行できます**。

## トラブルシューティング

??? failure "`/blackjack` コマンドが「不明なコマンド」になる"
    blackjack モジュールが無効化されている可能性があります。`config.yml` の `modules.blackjack.enabled` が `true` になっているか確認してください。無効なモジュールはコマンドもリスナーも登録されません。

??? failure "起動ログに「EmeraldAPI not bound」と出てモジュールが起動しない"
    blackjack は `bank` モジュール（エメラルド口座）に依存します。`modules.bank.enabled` が `true` になっているか確認してください。bank が無効だと blackjack の起動は失敗します。

??? failure "看板を設置しても自動整形されない / 「権限がありません」と出る"
    看板の作成には OP 権限または `blackjack.admin` が必要です。視線先（5ブロック以内）に看板がある状態で `/blackjack setsign <join|leave|bet|start|solo [卓ID]>` を実行してください。`bet` 看板は金額引数を取らず、クリックすると掛け金変更GUIが開きます。

??? failure "看板や `/blackjack join` でロビーに参加できない"
    ゲームが進行中（IN_GAME）の場合、ロビーへの新規参加はできません。`/blackjack stop` で現在のゲームを終了させてから参加してください。また、看板参加は最大6人で、満員時は参加できません。

??? failure "`/blackjack start` でゲームが始まらない"
    ロビーに有効な参加者が1人以上必要です。各参加者は **ベット額が設定済み** かつ **口座残高がベット額以上** である必要があります。条件を満たさないプレイヤーは開始時に自動でロビーから外され、全員外れるとゲームは中止されます。

??? failure "退出・終了時にプレイヤーがテレポートされない"
    spawn 地点 / lobby 地点が未設定の可能性があります。`/blackjack setstartspawn` と `/blackjack setlobby` で両方を設定してください。地点が未設定の場合はテレポートがスキップされます（ログ・チャットに通知されます）。

??? failure "賭け金や配当が口座に反映されない"
    賭け金処理は `bank` モジュールの `EmeraldAPI` を経由します。`bank` モジュールが有効か確認してください。賭け金はゲーム開始時にサーバー口座へ集められ、終了時に勝者へ支払われます（端数はサーバー側に残ります）。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ブラックジャック概要へ](index.md){ .md-button }
