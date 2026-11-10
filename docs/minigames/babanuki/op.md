<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ババ抜き（BabaNuki） ― OP・運営ガイド { .page-op #babanuki-op }

ババ抜きの導入・席とロビーの設定・看板・config・権限・管理コマンドをまとめます。地点・席・看板はコマンドや管理メニューで設定すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | BabaNuki |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/babanuki`（別名 `/baba`） |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/BabaNuki/config.yml` |
| 権限ノード | `babanuki.admin`（既定OP） |

## 導入手順

1. ビルドした `BabaNuki` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される（人数・時間は初期値で動作）。
3. `/baba setstartspawn`・`/baba setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/baba setspawn <1〜6>` で卓の席（プレイヤーが座る位置）を設定する（席が未設定でも、ロビーを中心に自動で円状に並びます）。
5. 参加・離脱・開始の看板を設置する。
6. `/baba status` で設定状況を確認する。

!!! note "席について"
    席（`/baba setspawn 1`〜`6`）は必須ではありません。設定しておくと試合でその位置を優先して使い、未設定のぶんはロビーを中心に半径 `table.auto-radius`（既定3.0）の円状へ自動配置します。`table.freeze-players` が true のときは、試合中は自分の席から動けなくなります。

## セットアップ手順（コマンド）

地点・席は **実行した位置** を使い、看板系は看板を **見ながら** 実行します。すべて `babanuki.admin` 権限が必要です。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/baba setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/baba setlobby
```

```text title="卓の席を設定（席番号 1〜6・その場に立って実行）"
/baba setspawn 1
```

```text title="参加看板を登録（看板を見て実行）"
/baba setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/baba setsign leave
```

```text title="開始看板を登録（形式を指定・看板を見て実行）"
/baba setsign start normal
```

```text title="視線先の看板の登録を解除"
/baba setsign delete
```

!!! note "開始看板の形式"
    開始看板は形式ごとに登録します（`normal` / `cpu`）。識別子に「.」は使えません。`/baba start`（引数なし）や引数なしの開始看板では、config の `default-mode` の形式で始まります。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール** と **管理メニュー（GUI）** でも席・地点・看板を設定できます。どちらも `babanuki.admin` 権限が必要です。

```text title="管理ツールを受け取る"
/baba wand
```

```text title="管理メニュー（GUI）を開く"
/baba menu
```

## config.yml 設定項目

コマンドで登録する地点・席・看板のデータ（`default-spawn`・`lobby-spawn`・`seats`・`signs`）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。設定の変更は「次の試合から」反映されます（`/baba reload` で再読み込み）。

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `max-lobby` | 12 | ロビーに入れる人数（出場できない人は待機） |
| `max-players` | 6 | 1試合に出場できる人数（2〜6・参加順に出場） |
| `countdown-seconds` | 5 | 開始前のカウントダウン（秒） |
| `result-seconds` | 8 | 試合終了後、結果を表示しておく秒数 |
| `default-mode` | normal | 形式省略時の形式（normal=対人戦 / cpu=CPU練習） |

### ルール（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `game.turn-seconds` | 0 | 1枚引くまでの制限時間（秒）。時間切れはランダムに1枚引く（0=無制限） |
| `game.turn-gap-ticks` | 20 | 引いたあと次の人の番になるまでの間（20=1秒） |
| `game.joker-position` | true | ジョーカーを持っている人が、引かれる位置（引く人から見て左から何枚目）を手札画面で選べる。`false` で毎回ランダム |

### CPU（`cpu`）

| キー | 既定値 | 説明 |
|---|---|---|
| `cpu.fill-to` | 4 | CPU練習で、合計人数がこの人数になるまでCPUを入れる（最低1体・2〜6） |
| `cpu.think-ticks` | 30 | CPUが引くまでの時間（20=1秒・少しランダムに揺らす） |
| `cpu.end-when-no-human` | true | 人間の出場者が全員抜けたら、CPU同士で続けず試合を終える |

### 卓（`table`）・環境

| キー | 既定値 | 説明 |
|---|---|---|
| `table.freeze-players` | true | 試合中は自分の席から動けないようにする |
| `table.name-height` | 2.3 | 席の上に出す名前表示の高さ |
| `table.auto-radius` | 3.0 | 席が未設定のとき、ロビーを中心に自動で並べる円の半径 |
| `keep-food` | true | ロビー在籍中は満腹度を減らさない |
| `protect-lobby` | true | ロビー在籍中はダメージを受けない |

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/baba setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/baba setlobby` | ロビー地点を設定 |
| `/baba setspawn <1-6>` | 卓の席を設定 |
| `/baba setsign <join\|leave\|start <形式>\|delete>` | 看板を登録／解除 |
| `/baba wand` | 管理ツールを受け取る |
| `/baba menu` | 管理メニュー（GUI）を開く |
| `/baba stop` | 試合を強制停止（勝敗なし・在籍は維持） |
| `/baba reload` | 設定を再読み込み |
| `/baba leave <プレイヤー>` | 指定した人を離脱させる |
| `/baba lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/baba status` | 設定状況・現在の状況を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/baba join` | ロビーに参加（参加看板と同等） |
| `/baba leave` | ロビーから離脱（離脱看板と同等。試合中は脱落扱い） |
| `/baba start [normal\|cpu]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/baba hand` | 手札の画面を開く |
| `/baba status` | 設定・状況を確認（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `babanuki.admin` | OP | `setstartspawn`・`setlobby`・`setspawn`・`setsign`・`wand`・`menu`・`stop`・`reload`・`leave <プレイヤー>`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/baba join`・`/baba leave`（自分）・`/baba start`・`/baba hand`・`/baba status` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。`/baba leave <プレイヤー>` で他人を指定できるのは `babanuki.admin` を持つ場合だけです。

## トラブルシューティング

??? failure "ゲームが開始できない"
    対人戦（`normal`）はロビーに2人以上必要です。1人で遊ぶときは `cpu`（CPU練習）で開始してください。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/baba lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/BabaNuki/` に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ババ抜き】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。試合結果も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない・ロックが残った場合はOPが `/baba lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません）。地点・席・看板などのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも手札の本・管理ツールを使え、登録看板は蝋引きして編集画面が開かないようにします。カードのスート（マーク）はアイテムの種類と色で見分けられるようにしています。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | （使われません。2026-11-08 から、未復元の退避データは期限切れで捨てず、次のログインで必ず返します） |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | ババ抜き | 「【】」に入れる正式名 |
| `name-tag.color` | LIGHT_PURPLE | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも手札の本・管理ツールを使える |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ババ抜き（BabaNuki） 概要へ](index.md){ .md-button }
