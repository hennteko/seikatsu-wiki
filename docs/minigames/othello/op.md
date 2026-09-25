<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# オセロ（Othello） ― OP・運営ガイド { .page-op #othello-op }

オセロ（実物大オセロ）の導入・盤／スポーン／看板設定・盤の敷設・config・権限・管理コマンドをまとめます。地点・盤・看板はコマンドで登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Othello |
| メインコマンド | `/othello`（エイリアス `/oth`） |
| api-version | 26.1.2 |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Othello/config.yml` |
| 権限ノード | `othello.admin`（既定OP） |

## 導入手順

1. ビルドした `Othello` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される。
3. `/othello setstartspawn`・`/othello setlobby` で初期スポーンとロビーを設定する。
4. 盤の基準点（a1マス）を `/othello setboard` で設定し、`/othello buildboard` で盤の床を敷く。
5. 黒・白の観戦（着席）位置を `/othello setspawn black`・`setspawn white` で設定する。
6. 参加・離脱・開始の看板を設置し、`/othello status` で確認する。

## セットアップ手順（コマンド）

地点系は **実行した位置**、看板系は看板を **見ながら** 実行します。すべて `othello.admin` 権限が必要です。

```text title="初期スポーン（途中抜けの戻り先）"
/othello setstartspawn
```

```text title="受付ロビー"
/othello setlobby
```

```text title="黒／白のプレイ位置を設定"
/othello setspawn black
/othello setspawn white
```

```text title="盤の基準点（a1マス）を現在地に設定"
/othello setboard
```

```text title="盤の床を敷き直す（cell-size・床素材の変更後にも実行）"
/othello buildboard
```

```text title="参加／離脱／開始の看板を登録（看板を見て実行）"
/othello setsign join
/othello setsign leave
/othello setsign start classic
```

!!! note "盤の敷設（buildboard）"
    `/othello setboard` で盤の基準点（a1）を決めたあと、`/othello buildboard` を実行すると **8×8の市松模様の床が自動で敷かれます**。1マスのブロック数（`board.cell-size`）や床素材（`floor-a`/`floor-b`）を変更したら、再度 `buildboard` で敷き直してください。

## config.yml 設定項目

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `max-lobby` | 16 | ロビーに入れる人数（出場者以外は観戦） |
| `countdown-seconds` | 3 | 開始カウントダウン |
| `turn-seconds` | 30 | 1手の制限時間（秒） |
| `timeout-action` | random | 時間切れ時：`random`＝ランダムに置く／`forfeit`＝負け |
| `result-seconds` | 8 | 終局後、盤面を残す秒数 |

### 盤面（`board`）

| キー | 既定値 | 説明 |
|---|---|---|
| `board.cell-size` | 2 | 1マスのブロック数（1〜4）。変更したら `buildboard` で敷き直す |
| `board.floor-a` | GREEN_CONCRETE | 市松模様の床A |
| `board.floor-b` | LIME_CONCRETE | 市松模様の床B |
| `board.black-stone` | BLACK_CONCRETE | 黒石の素材 |
| `board.white-stone` | WHITE_CONCRETE | 白石の素材 |
| `board.stone-margin` | 0.1 | 石の余白（マスに対する割合） |
| `board.stone-thickness` | 0.15 | 石の厚み（ブロック） |

### 操作・表示・待機列・CPU

| キー | 既定値 | 説明 |
|---|---|---|
| `click-range` | 32 | 盤を指せる距離（ブロック） |
| `hints` | true | 置けるマスを本人にだけ緑の粒で表示 |
| `flip-animation` | true | 石をめくるアニメーション |
| `glow-last-move` | true | 直前に置いた石を光らせる |
| `queue.winner-stays` | true | 対人戦の勝者を次の対局にも続けて出す（勝ち残り） |
| `cpu.think-ticks` | 20 | CPUが打つまでの最低待ち時間（20＝1秒） |
| `cpu.player-color` | random | CPU戦のプレイヤーの色（`random`/`black`＝先手/`white`＝後手） |

### 環境・ロビー共通インベントリ

| キー | 既定値 | 説明 |
|---|---|---|
| `keep-food` | true | ロビー在籍中は満腹度を減らさない |
| `protect-lobby` | true | ロビー在籍中はダメージを受けない |
| `lobby-inventory.*` | ― | ロビー入退室時の所持品退避・復元（`enabled`/`gamemode`/`clear-effects`/`reset-health`/`reset-exp`/`expire-days`） |

!!! note "自動生成される領域（手動編集不要）"
    `default-spawn` / `lobby-spawn` / `spawns`（black/white）/ `board.origin` / `signs`（看板）はコマンドで自動保存されます。設定変更は次の対局から反映されます（`/othello reload` で再読み込み）。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/othello setstartspawn` | 初期スポーンを設定 |
| `/othello setlobby` | ロビーを設定 |
| `/othello setspawn <black\|white>` | 黒／白のプレイ位置を設定 |
| `/othello setboard` | 盤の基準点（a1）を設定 |
| `/othello buildboard` | 盤の床を敷く／敷き直す |
| `/othello setsign <join\|leave\|start <モード>\|delete>` | 看板を設定／解除 |
| `/othello stop` | 対局を強制終了 |
| `/othello reload` | config を再読み込み |
| `/othello lobbyfix <プレイヤー> [force]` | ロビー退避データを手動で復旧 |
| `/othello status` | 設定状況・現在の状況を確認（全員可） |
| `/othello top` / `stats` | ランキング／成績を表示（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `othello.admin` | OP | セットアップ・盤敷設・看板設置・強制停止・lobbyfix など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/othello join`・`/othello leave`・`/othello start <モード>`・`/othello resign`・`/othello status`・`/othello top`・`/othello stats` は権限チェックがなく、全プレイヤーが使えます。

## トラブルシューティング

??? failure "対局が開始できない"
    `/othello status` を確認してください。盤（`setboard`＋`buildboard`）・黒/白のプレイ位置・ロビーが設定されている必要があります。

??? failure "石を置いても反応しない"
    `click-range`（既定32）以内から、置けるマス（緑の粒が出る合法手）をクリックしているか確認してください。自分の手番であることも必要です。

??? failure "盤の床がずれている・マスが合わない"
    `board.cell-size` を変えたら必ず `/othello buildboard` で敷き直してください。`setboard` の基準点（a1マス）の位置も確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← オセロ 概要へ](index.md){ .md-button }
