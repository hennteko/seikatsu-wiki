<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ルーレット ― OP・運営ガイド { .page-op #roulette-op }

ルーレット（CasinoPlugin の roulette モジュール）の有効化・設定・盤の設置・看板・管理コマンド・権限をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `roulette` |
| メインコマンド | `/roulette`（エイリアス `/rl`） |
| 盤面 | ヨーロピアン（0〜36・37マス。`american` は将来対応予定） |
| 設定ファイル | `plugins/CasinoPlugin/modules/roulette.yml` |
| 通貨 | エメラルド銀行（`bank` モジュールに依存） |

## 有効化

CasinoPlugin の `config.yml` の `modules:` ブロックで `roulette` を有効にします（未記載でも既定で有効）。

```text title="config.yml（抜粋）"
modules:
  roulette:
    enabled: true
```

!!! info "CasinoPlugin のモジュールです"
    ルーレットは統合プラグイン **CasinoPlugin** の1モジュールです。賭け金・配当は `bank` モジュール（エメラルド銀行）を通じて処理されます。全体の導入・共通設定は [CasinoPlugin の概要ページ](../casino-plugin/op.md) を参照してください。

## セットアップ手順

マルチ卓を使う場合は、盤（疑似ホイールの基準点）と参加看板を設置します。ソロGUIのみなら盤の設置は不要です。

```text title="盤（疑似ホイール）の基準点を現在地に設定"
/roulette setwheel
```

```text title="参加看板を登録（看板を見ながら実行・複数設置可）"
/roulette setsign join
```

```text title="マルチ卓のスピンを手動開始（ベット受付を締め切って回す）"
/roulette start
```

```text title="設定を再読み込み"
/roulette reload
```

!!! success "看板は複数設置できます"
    参加看板（`join`）は複数拠点に設置できます（`join-signs` に追記）。`setwheel` で保存される盤の位置は `wheel-location`（`"world;x;y;z;yaw"`）として自動保存されます。いずれも手編集不要です。

## roulette.yml 設定項目

### 盤・マルチ卓

| キー | 既定値 | 説明 |
|---|---|---|
| `wheel` | european | 盤面種別（現状 `european` のみ。将来 `american` 追加予定） |
| `multi.enabled` | true | マルチ卓を有効化 |
| `multi.betting_seconds` | 30 | ベット受付時間（秒） |
| `multi.min_players` | 1 | 開始に必要な最少参加人数（1人でも回せる） |
| `multi.clear_inventory` | false | 卓参加時にインベントリを預かる（退場で復元）か |

### ベット

| キー | 既定値 | 説明 |
|---|---|---|
| `bet.amounts` | `[10, 50, 100, 500, 1000, 5000, 10000, 50000]` | 金額選択GUIに並ぶ候補（空なら共通デフォルト） |
| `bet.min_bet` | 10 | 最小ベット |
| `bet.max_bet` | 1000000 | 1種あたりの最大ベット |
| `bet.max_total_bet` | 1000000 | 1スピンの合計ベット上限 |
| `bet.reject_if_insufficient` | true | 残高不足のスピンは拒否（マイナス残高を作らない） |

### 配当（純倍率 x:1。当選時は 掛け金×(倍率+1) を払い戻し）

| キー | 既定値 | 賭け |
|---|---|---|
| `payouts.straight` | 35 | ストレート（1つの数字） |
| `payouts.red_black` | 1 | 赤／黒 |
| `payouts.odd_even` | 1 | 奇数／偶数 |
| `payouts.high_low` | 1 | ハイ(19-36)／ロー(1-18) |
| `payouts.dozen` | 2 | ダース（12個ずつ） |
| `payouts.column` | 2 | コラム（縦3列） |

### 演出・疑似ホイール

| キー | 既定値 | 説明 |
|---|---|---|
| `spin.animation_ticks` | 50 | スピン演出の長さ（20tick=1秒） |
| `display.enabled` | true | 疑似ホイール（表示エンティティ・リソースパック不要）を表示 |
| `display.radius` | 2.0 | 数字リングの半径（ブロック） |
| `display.height_offset` | 1.0 | `setwheel` 基準点からの高さ |
| `display.spins` | 4 | スピン時の周回数（演出） |

!!! note "自動生成される領域（手編集不要）"
    `wheel-location`（`/roulette setwheel`）・`join-signs`（`/roulette setsign join`）はコマンドで自動保存されます。

## 管理コマンド・権限

`/roulette` の `play`/`join`/`leave`/`status` は全員可、`start`/`setwheel`/`setsign`/`reload` は **OP（コード側で判定）** です。

| コマンド | 権限 | 説明 |
|---|---|---|
| `/roulette play` | 全員 | ソロGUIを開く |
| `/roulette join` / `leave` | 全員 | マルチ卓に参加／離脱 |
| `/roulette status` | 全員 | 状況確認 |
| `/roulette start` | OP | マルチ卓のスピンを手動開始 |
| `/roulette setwheel` | OP | 盤（疑似ホイール）の基準点を設定 |
| `/roulette setsign join` | OP | 参加看板を登録（複数可） |
| `/roulette reload` | OP | 設定を再読み込み |

## トラブルシューティング

??? failure "`/roulette` が「不明なコマンド」になる"
    `config.yml` の `modules.roulette.enabled` が `false` になっていないか確認してください。無効モジュールはコマンドが登録されません。

??? failure "マルチ卓が回らない・盤が表示されない"
    `/roulette setwheel` で盤の基準点が設定されているか確認してください。`display.enabled` が true で、基準点周辺に表示スペースがあると疑似ホイールが表示されます。

??? failure "ベットできない"
    エメラルド銀行（`bank` モジュール）が有効で、残高が足りているか確認してください。1スピンの合計ベットが `bet.max_total_bet` や残高を超えると受け付けられません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ルーレット 概要へ](index.md){ .md-button }
