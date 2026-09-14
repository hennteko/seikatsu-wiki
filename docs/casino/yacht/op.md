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
| 現フェーズ | クイックヨット＋ジャックポット（ソロ・対戦は今後のフェーズ） |

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

```text title="ヨット看板を登録（看板を見ながら実行・複数設置可）"
/yacht setsign
```

```text title="視線先のヨット看板を解除"
/yacht setsign delete
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
| `rolls-per-turn` | 3 | サイコロの投数（ソロ/対戦用・将来実装で使用） |
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
| `quick.result-seconds` | 3 | 結果表示の秒数 |
| `quick.extra-roll.enabled` | false | 4投目購入オプション（ONにする前にシミュレーションで倍率調整） |
| `quick.extra-roll.cost-multiplier` | 1.0 | 4投目購入のコスト倍率 |

### ジャックポット

| キー | 既定値 | 説明 |
|---|---|---|
| `jackpot.enabled` | true | ジャックポットの有効化 |
| `jackpot.seed` | 1000 | 初期値（放出後にこの額から積み上がる） |
| `jackpot.contribution-rate` | 0.01 | 各ゲームの賭け金から積み立てる割合 |
| `jackpot.solo-share` | 0.5 | ソロ/対戦でヨット達成時に放出する割合（今後のフェーズ用。クイックは全額放出） |
| `jackpot.broadcast` | true | 当選を全体告知する |

## 管理コマンド・権限

`/yacht`・`/yacht quick` は全員可、`setsign`・`reload`・`jackpot set` は **OP（`yacht.admin`）** です。

| コマンド | 権限 | 説明 |
|---|---|---|
| `/yacht` / `/yacht quick [金額]` | 全員 | クイックヨットのGUIを開く |
| `/yacht jackpot` | 全員 | ジャックポット額を確認 |
| `/yacht jackpot set <額>` | `yacht.admin` | ジャックポット額を設定 |
| `/yacht setsign [delete]` | `yacht.admin` | ヨット看板を登録／解除 |
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
