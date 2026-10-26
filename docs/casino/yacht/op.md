<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ヨット（ヤッツィー） ― OP・運営ガイド { .page-op #yacht-op }

ヨット（CasinoPlugin の yacht モジュール）の有効化・設定・看板・管理コマンド・権限をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `yacht` |
| メインコマンド | `/yacht` |
| 設定ファイル | `plugins/CasinoPlugin/modules/yacht.yml` |
| 通貨 | エメラルド銀行（`bank` モジュールに依存） |
| 現フェーズ | クイックヨット＋役比べ（マルチ2〜4人）＋ジャックポット（ソロ・スコアアタックは今後のフェーズ） |

## 有効化

CasinoPlugin の `config.yml` の `modules:` ブロックで `yacht` を有効にします（未記載でも既定で有効）。

```text title="config.yml（抜粋）"
modules:
  yacht:
    enabled: true
```

!!! info "CasinoPlugin のモジュールです"
    ヨットは統合プラグイン **CasinoPlugin** の1モジュールです。賭け金・配当は `bank` モジュール（エメラルド銀行）を通じて処理されます。全体の導入・共通設定は [CasinoPlugin の概要ページ](../casino-plugin/op.md) を参照してください。

## セットアップ手順

クイックヨットは看板またはコマンドのみで遊べます。役比べ（マルチ）を使う場合はロビー地点・戻り先と参加/開始看板を設置します。

```text title="ヨット看板を登録（看板を見ながら実行・種類を指定・複数設置可）"
/yacht setsign <join|leave|start|quick>
```

```text title="視線先のヨット看板を解除"
/yacht setsign delete
```

```text title="役比べのロビー地点を現在地に設定"
/yacht setlobby
```

```text title="卓を離れたとき・再接続時の戻り先を現在地に設定"
/yacht setstartspawn
```

```text title="ジャックポット額を確認／設定"
/yacht jackpot
/yacht jackpot set <額>
```

```text title="設定を再読み込み"
/yacht reload
```

## yacht.yml 設定項目

### サイコロ・賭け金

| キー | 既定値 | 説明 |
|---|---|---|
| `rolls-per-turn` | 3 | 役比べ（マルチ）の1手番の投数（1〜5） |
| `quick-rolls` | 2 | クイックの投数（2＝`payout`、3＝`payout-3roll` を使用） |
| `bet.min` | 100 | 最小ベット |
| `bet.max` | 10000 | 最大ベット |
| `bet.options` | `[100, 500, 1000, 5000, 10000]` | 金額選択の候補 |

### 配当（総返還倍率・賭け金込み。0で没収）

`quick.payout`（2投ルール）と `quick.payout-3roll`（3投ルール）で、役ごとの返還倍率を設定します。

| 役 | 2投（`payout`） | 3投（`payout-3roll`） |
|---|---|---|
| `YACHT` | 15.0 | 7.0 |
| `FOUR_OF_A_KIND` | 3.0 | 1.5 |
| `LARGE_STRAIGHT` | 3.0 | 1.5 |
| `FULL_HOUSE` | 1.5 | 1.0 |
| `SMALL_STRAIGHT` | 0.5 | 0.5 |
| `THREE_OF_A_KIND` / `TWO_PAIR` / `NONE` | 0.0 | 0.0 |

| キー | 既定値 | 説明 |
|---|---|---|
| `quick.result-seconds` | 3 | 【未使用】結果画面は「もう一回／閉じる」を選ぶ方式のため現在使われません |
| `quick.close-grace-seconds` | 60 | 未確定のまま画面を閉じたときの猶予秒数（猶予内なら `/yacht`・看板で続きから再開。超過で現在の出目で自動確定。0以下で閉じた瞬間に確定） |
| `quick.extra-roll.enabled` | false | 【未使用】4投目購入（未実装。true にしても変化なし） |
| `quick.extra-roll.cost-multiplier` | 1.0 | 【未使用】4投目購入のコスト倍率（未実装） |

### ジャックポット

| キー | 既定値 | 説明 |
|---|---|---|
| `jackpot.enabled` | true | ジャックポットの有効化 |
| `jackpot.seed` | 1000 | 初期値（放出後にこの額から積み上がる） |
| `jackpot.contribution-rate` | 0.01 | 各ゲームの賭け金から積み立てる割合 |
| `jackpot.solo-share` | 0.5 | 【未使用】ソロ・スコアアタック用の予約キー（クイックは全額放出、役比べは `multi.jackpot-share` を使う） |
| `jackpot.broadcast` | true | 当選を全体告知する |

### 役比べ（マルチ）

| キー | 既定値 | 説明 |
|---|---|---|
| `multi.min-players` | 2 | 開始に必要な最少人数（2以上） |
| `multi.max-players` | 4 | 卓の最大人数（2〜4） |
| `multi.entry-fee.amount` | 500 | 卓の参加費（全員同額。管理メニューで `min`〜`max` の範囲で変更可） |
| `multi.entry-fee.min` | 100 | 参加費の下限 |
| `multi.entry-fee.max` | 10000 | 参加費の上限 |
| `multi.house-rake-percent` | 5 | ポットからハウスが取る手数料（%）。勝者は「参加費合計 − 手数料」を受け取る |
| `multi.turn-timeout-seconds` | 30 | 手番の制限時間（秒）。振っていなければ自動で振り、今の目で確定 |
| `multi.jackpot-share` | 0.0 | ヨット達成者へ放出するジャックポットの割合（0.0〜1.0）。`0.0` = 役比べではジャックポット無効（積み立てもしない） |

## 管理コマンド・権限

`/yacht`・`/yacht quick`・役比べの `join`/`leave`/`start`/`action` は全員可、`setsign`・`setlobby`・`setstartspawn`・`stop`・`jackpot set`・`wand`・`menu`・`reload`・`lobbyfix` は **OP（`yacht.admin`）** です。

| コマンド | 権限 | 説明 |
|---|---|---|
| `/yacht` / `/yacht quick [金額]` | 全員 | クイックヨットのGUIを開く |
| `/yacht join` / `/yacht leave` | 全員 | 役比べのロビーに参加／退出 |
| `/yacht start` | 全員 | 役比べを開始（自動では始まらない） |
| `/yacht action` | 全員 | 自分の手番の画面／閉じたクイックを開き直す |
| `/yacht status` | 全員 | 看板の登録状況・現在のジャックポット額を確認 |
| `/yacht jackpot` | 全員 | ジャックポット額を確認 |
| `/yacht jackpot set <額>` | `yacht.admin` | ジャックポット額を設定 |
| `/yacht setsign <join\|leave\|start\|quick>` | `yacht.admin` | ヨット看板を登録（看板を見ながら実行） |
| `/yacht setsign delete` | `yacht.admin` | 視線先の看板を解除 |
| `/yacht setlobby` | `yacht.admin` | 役比べのロビー地点を設定 |
| `/yacht setstartspawn` | `yacht.admin` | 卓を離れたとき・再接続時の戻り先を設定 |
| `/yacht stop [multi\|solo [プレイヤー]]` | `yacht.admin` | 進行中の試合を強制終了（参加費は返金） |
| `/yacht join\|leave <プレイヤー>` | `yacht.admin` | 指定プレイヤーを役比べに参加／退出させる |
| `/yacht wand` / `/yacht menu` | `yacht.admin` | 管理ツールを取得／管理メニューを開く |
| `/yacht lobbyfix <プレイヤー> [force]` | `yacht.admin` | 預かり中の持ち物を手動で復元 |
| `/yacht reload` | `yacht.admin` | 設定を再読み込み |

## トラブルシューティング

??? failure "`/yacht` が「不明なコマンド」になる"
    `config.yml` の `modules.yacht.enabled` が `false` になっていないか確認してください。

??? failure "配当が想定と違う"
    `quick-rolls`（2か3）で使う倍率テーブル（`payout` / `payout-3roll`）が変わります。設定した投数と倍率を確認してください。

??? failure "ベットできない"
    エメラルド銀行（`bank` モジュール）が有効で、残高が足りているか確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ヨット 概要へ](index.md){ .md-button }
