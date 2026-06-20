<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ポーカー ― OP・運営ガイド { .page-op #poker-op }

ポーカー（poker モジュール）の有効化・`poker.yml` 設定・会場のセットアップ・管理コマンド・権限をまとめます。ポーカーは CasinoPlugin に統合された poker モジュールのため、専用 jar の導入は不要です。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `poker` |
| ゲーム種別 | テキサスホールデム |
| メインコマンド | `/poker`（エイリアス `/pk`） |
| 設定ファイル | `plugins/CasinoPlugin/modules/poker.yml` |
| 有効化フラグ | `plugins/CasinoPlugin/config.yml` の `modules.poker.enabled` |
| 依存モジュール | `bank` モジュール（エメラルドの口座機能。必須） |
| 人数 | 2〜6人（最少2人で開始、看板の上限6人） |

!!! info "CasinoPlugin のモジュールです"
    ポーカーは CasinoPlugin の poker モジュールとして動作します。専用 jar はありません。CasinoPlugin 本体の導入・共通設定は [CasinoPlugin の OP ページ](../casino-plugin/op.md) を参照してください。poker モジュールは起動時に bank（エメラルド銀行）の API が利用可能であることを必要とし、bank が未起動だと poker モジュールは起動に失敗します。

## 有効化

ポーカーを使うには、CasinoPlugin の `config.yml` で poker モジュールを有効にします。専用 jar の追加は不要です。

```yaml
# plugins/CasinoPlugin/config.yml
modules:
  bank:
    enabled: true   # 必須。エメラルドの口座機能
  poker:
    enabled: true   # ポーカーを有効化
```

- `modules.poker.enabled` を `true` にすると、サーバー起動時に poker モジュールが起動し、`/poker` コマンドと看板リスナーが登録されます。
- `false` の場合、コマンド・リスナーは一切登録されません。
- `bank` モジュールが無効だと、poker モジュールは起動に失敗します（賭け金の処理に銀行の口座機能が必要なため）。
- 初回起動時、`plugins/CasinoPlugin/modules/poker.yml` が自動生成されます。

## poker.yml 設定項目

個別設定は `plugins/CasinoPlugin/modules/poker.yml` にあります。座標や看板の位置はコマンドから自動で書き込まれるため、手動編集は基本的に `settings` 部分のみで十分です。

### settings（一般設定）

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.maxRaiseAmount` | `1000` | 1ハンドあたりの合計ベット額の上限（レイズ上限）。`/poker raisemoney` でも変更でき、変更内容はこのキーに保存される |

| `bet_options` | （任意）SB掛け金変更GUIに並べる金額リスト。未設定時は既定の18段階（1〜1,000,000）を使用 |

### signs（公開札看板の位置）

`signs.sign1` 〜 `signs.sign5` に、公開カードを表示する5枚の看板の座標が保存されます。`/poker setsign card1`〜`/poker setsign card5` コマンドで設定され、各エントリは `world` / `x` / `y` / `z` を持ちます。

### locations（誘導用の座標）

`locations.lobby` / `locations.spawn` と、ゲームエリアの対角2点 `locations.field1` / `locations.field2` に、プレイヤー誘導用の座標が保存されます。`/poker setlobby` / `setstartspawn` / `setfield <1|2>` コマンドで設定され、各エントリは `world` / `x` / `y` / `z` / `yaw` / `pitch` を持ちます。

| 座標 | 役割 |
|---|---|
| `locations.lobby` | 参加看板クリック時とゲーム終了時に転送されるロビー |
| `locations.field1` / `field2` | ゲームエリア（対戦卓）の範囲を表す対角2点 |
| `locations.spawn` | 離脱時に転送されるスポーン地点 |

!!! note "座標・看板は手動編集しない"
    `signs` と `locations` のブロックは、後述のセットアップコマンドを実行した場所の座標が自動で書き込まれます。手動で書く必要はありません。書き込む場合は poker.yml 冒頭のコメントにある形式に従ってください。

## セットアップ手順

ポーカー会場を作るには、座標3地点と公開札看板5枚を設定します。OP 権限（`poker.admin.setup`）を持った状態で、以下を順に実行します（座標系はその場に立って、看板系は看板を見ながら実行）。

```text title="① ロビーを現在地に設定"
/poker setlobby
```

```text title="② ゲームエリアの角1を現在地に設定"
/poker setfield 1
```

```text title="② ゲームエリアの角2を現在地に設定"
/poker setfield 2
```

```text title="③ 離脱スポーンを現在地に設定"
/poker setstartspawn
```

公開札看板を5枚設置し、それぞれの看板を見ながら1枚ずつ実行します。

```text title="④ 公開札看板 1枚目"
/poker setsign card1
```

```text title="公開札看板 2枚目"
/poker setsign card2
```

```text title="公開札看板 3枚目"
/poker setsign card3
```

```text title="公開札看板 4枚目"
/poker setsign card4
```

```text title="公開札看板 5枚目"
/poker setsign card5
```

```text title="⑤ スモールブラインド額を設定（例: 10）"
/poker bet 10
```

```text title="レイズ上限を設定（例: 1000）"
/poker raisemoney 1000
```

参加・離脱用の看板を設置し、看板を見ながら登録します（テキストは自動整形）。

```text title="⑥ 参加看板を登録"
/poker setsign join
```

```text title="離脱看板を登録"
/poker setsign leave
```

```text title="⑦ 設定状況を確認（3座標・ベット額・待機人数）"
/poker status
```

!!! tip "公開札看板の役割"
    `card1`〜`card5`（poker.yml では `signs.sign1`〜`sign5`）の看板には、フロップ・ターン・リバーで公開されるカードが順に表示されます。5枚すべてが設定されていないとゲームを開始できません（`/poker start` 実行時にチェックされます）。

!!! note "看板の種類とフォーマット"
    看板は視線先（6ブロック以内）を見ながら `/poker setsign <join|leave|bet|start|card1〜5>` で登録します。種別ごとの動作は次のとおりです。

    - `join`（`lobby` でも可）: 参加看板。クリックで待機リストに追加＆ロビーへ転送。参加人数（最大6人）を自動表示
    - `leave`: 離脱看板。クリックで待機リスト／ゲームから離脱
    - `bet`: SB掛け金変更看板。クリックで **SB掛け金変更GUI** が開く（金額の変更はOPのみ）。GUIに並ぶ金額は poker.yml の `bet_options` で変更可能
    - `start`: 開始看板。クリックでゲーム開始（OP専用）
    - `card1`〜`card5`: 公開札看板

    手書きの場合は、参加看板は1行目を `[Poker]` / `[Poker参加]` / `[PokerJoin]` のいずれかに、離脱看板は `[PokerLeave]` / `[Poker離脱]` のいずれかにして設置できます（設置時に自動整形）。

## ゲーム運営コマンド

```text title="ゲームを開始（最少2人・公開札看板5枚が必要）"
/poker start
```

```text title="進行中のゲームを強制終了"
/poker stop
```

```text title="現在のポット額を表示"
/poker money
```

```text title="現在のレイズ上限を表示"
/poker getraisemoney
```

## 管理コマンド一覧

| コマンド | 必要権限 | 説明 |
|---|---|---|
| `/poker setlobby` | `poker.admin.setup` | 現在地をロビー座標に設定 |
| `/poker setfield <1\|2>` | `poker.admin.setup` | 現在地をゲームエリアの角1/角2に設定 |
| `/poker setstartspawn` | `poker.admin.setup` | 現在地を離脱スポーン座標に設定 |
| `/poker setsign card<1-5>` | `poker.admin.setup` | 見ている看板を公開札看板に登録（poker.yml の `signs.sign1`〜`sign5`） |
| `/poker setsign <join\|leave\|bet\|start>` | `poker.admin.setup` | 見ている看板を参加／離脱／SB掛け金GUI／開始看板として登録（自動整形） |
| `/poker bet <額>` | `poker.admin.setup` | スモールブラインド（SB）額を設定（BB はその2倍。ゲーム進行中は変更不可） |
| `/poker raisemoney <額>` | `poker.admin.setup` | レイズ上限を設定（poker.yml に保存される） |
| `/poker getraisemoney` | なし | 現在のレイズ上限を表示 |
| `/poker start` | `poker.admin.control` | ゲームを開始（最少2人・看板5枚が必要） |
| `/poker stop` | `poker.admin.control` | 進行中のゲームを強制終了 |
| `/poker status` | なし | 設定状況・待機人数・ゲーム状態を表示 |
| `/poker money` | なし | 現在のポット額を表示 |
| `/poker join [プレイヤー名]` | `poker.admin.control` または OP | プレイヤーを待機リストに追加（プレイヤーは `[Poker]` 看板から参加） |
| `/poker leave [プレイヤー名]` | `poker.admin.control` または OP | プレイヤーを待機リスト／ゲームから離脱させる |
| `/poker debug hand <カード7枚>` | `poker.admin.debug` | 7枚のカードで役判定をテスト（例: `SA SK SQ SJ ST H2 D3`） |

!!! note "start / stop はコンソールからも実行可能"
    `/poker start` と `/poker stop` は、コマンドブロックからも実行できます（コマンドブロック実行時は権限チェックが省略されます）。プレイヤー・コンソールから実行する場合は `poker.admin.control` 権限が必要です。`/poker bet` はゲーム進行中は実行できません。

## 権限ノード

`plugin.yml` で定義されている poker 関連の権限です。

| 権限 | 既定 | 用途 |
|---|---|---|
| `poker.admin` | OP | ポーカー管理コマンドの全権限（下記3つを子に持つ） |
| `poker.admin.setup` | OP | セットアップ系コマンド（setup / sign / bet / raisemoney） |
| `poker.admin.control` | OP | ゲーム制御系コマンド（start / stop） |
| `poker.admin.debug` | OP | デバッグ系コマンド（debug hand） |

`poker.admin` を付与すると、子権限の `poker.admin.setup` / `poker.admin.control` / `poker.admin.debug` がすべて有効になります。`/poker status` / `/poker money` / `/poker getraisemoney` など情報系のコマンドは権限不要で、全員が実行できます。

## トラブルシューティング

??? failure "/poker コマンドが「不明なコマンド」になる"
    poker モジュールが無効化されている可能性があります。`config.yml` の `modules.poker.enabled` が `true` か確認してください。無効モジュールはコマンド・リスナーが一切登録されません。

??? failure "起動ログに poker モジュールの起動失敗が出る"
    poker モジュールは起動時に bank（エメラルド銀行）の API を必要とします。`modules.bank.enabled` が `true` で、bank モジュールが正常に起動しているか確認してください。bank が未起動だと poker は `EmeraldAPI not bound` で起動に失敗します。

??? failure "/poker start でゲームが始まらない"
    開始には次の条件が必要です。いずれかが不足するとエラーメッセージが表示されます。
    
    - 待機中のプレイヤーが **2人以上** いること。
    - 公開札看板 `card1`〜`card5`（poker.yml の `sign1`〜`sign5`）の **5枚すべて** が設定済みであること。
    - 既にゲームが進行中でないこと。
    
    `/poker status` で待機人数を、看板設定は `/poker setsign card1`〜`card5` の実行履歴を確認してください。

??? failure "/poker setsign で「看板を見ながら実行」と出る"
    `/poker setsign <種別>` は、看板ブロックを見ながら（6ブロック以内）実行する必要があります。看板を設置してから、その看板に視点を合わせてコマンドを実行してください。

??? failure "ゲーム開始時にプレイヤーが転送されない"
    ゲームエリア（`locations.field1` / `field2`）が未設定の可能性があります。`/poker setfield 1` と `/poker setfield 2` で設定してください。ゲーム終了時の転送先はロビー（`/poker setlobby`）です。

??? failure "公開カードが看板に表示されない"
    `card1`〜`card5` に登録した座標に **看板ブロックが実在する** か確認してください。看板が破壊・移動されていると表示が更新されません。再設置後、`/poker setsign card1`〜`card5` で登録し直してください。

??? failure "RAISE の上限を変えたい / ベット額を変えたい"
    レイズ上限は `/poker raisemoney <額>`（poker.yml の `settings.maxRaiseAmount` に保存）、スモールブラインド額は `/poker bet <額>` で変更します。`/poker bet` はゲーム進行中は使えないため、ゲーム終了後に実行してください。

??? failure "途中でプレイヤーが抜けてゲームが止まる"
    プレイヤーが離脱・切断すると自動でフォールド扱いになります。アクティブな参加者が1人になった時点でそのプレイヤーの勝利としてゲームが終了します。意図せず停止した場合は `/poker stop` で強制終了できます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ポーカー 概要へ](index.md){ .md-button }
