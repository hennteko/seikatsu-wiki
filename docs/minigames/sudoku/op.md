<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ナンプレ（Sudoku） ― OP・運営ガイド { .page-op #sudoku-op }

ナンプレの導入・初期スポーン/ロビー/盤面/看板の設定・管理ツール・config・権限・管理コマンドをまとめます。地点・盤面・看板はコマンドや管理メニューで設定すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Sudoku |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/sudoku` |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/Sudoku/config.yml` |
| 権限ノード | `sudoku.admin`（既定OP） |

## 導入手順

1. ビルドした `Sudoku` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される（人数・時間・難易度は初期値で動作）。
3. `/sudoku setstartspawn`・`/sudoku setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/sudoku setboard <番号>` で盤面を建てる（モードに応じて必要な数を登録）。
5. 参加・離脱・ソロ・開始（モードごと）の看板を設置する。
6. `/sudoku status` で設定状況を確認する。

!!! note "モードと必要な盤面"
    **対戦** は人数分の盤面が必要です（最大8面）。登録した盤面の数が対戦の最大人数になります。**ソロ** も空いている盤面を使います。**協力・陣取り** は「共有盤面」（既定は1番・config の `game.shared-board`）を使うので、人が多くても動きやすい場所に建ててください。

## セットアップ手順（コマンド）

地点は **実行した位置** を使い、盤面は **立ち位置を基準** に建て、看板系は看板を **見ながら** 実行します。すべて `sudoku.admin` 権限が必要です。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/sudoku setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/sudoku setlobby
```

```text title="盤面を建てて登録（立ち位置の1ブロック先から、向いた方向へ 13×13 を建てる。同じ番号なら建て直し）"
/sudoku setboard 1
```

```text title="2面目の盤面を建てて登録（対戦は人数分の盤面が必要）"
/sudoku setboard 2
```

```text title="盤面を撤去して登録解除（建てる前のブロックに戻す）"
/sudoku delboard 1
```

```text title="参加看板を登録（看板を見て実行）"
/sudoku setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/sudoku setsign leave
```

```text title="ソロ看板を登録（看板を見て実行）"
/sudoku setsign solo
```

```text title="対戦の開始看板を登録（看板を見て実行）"
/sudoku setsign start versus
```

```text title="協力の開始看板を登録（看板を見て実行）"
/sudoku setsign start coop
```

```text title="陣取りの開始看板を登録（看板を見て実行）"
/sudoku setsign start territory
```

```text title="視線先の看板の登録を解除"
/sudoku setsign delete
```

!!! note "盤面を建てるときの注意"
    盤面を置きたい場所の **手前** に立ち、広げたい方向（東西南北）を向いて実行します。立ち位置の1ブロック先から、向いた方向へ 13×13 ブロックの盤面が建ちます（横方向は立ち位置が中心。立ち位置が試合開始時の移動先になります）。床（足元の1つ下）と額縁の段（足元の高さ）のブロックは上書きされ、建てる前のブロックは `boards-backup.yml` に控えられて `delboard` で元に戻ります。盤面どうしは **3ブロック以上** 離してください。試合で使用中の盤面には `setboard`・`delboard` はできません。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール（ブレイズロッド）** と **管理メニュー（GUI）** でも地点・盤面・看板を設定できます。どちらも `sudoku.admin` 権限が必要です。

```text title="管理ツール（ブレイズロッド）を受け取る"
/sudoku wand
```

```text title="管理メニュー（GUI）を開く"
/sudoku menu
```

- 管理ツールを右クリック（統合版はタップ／長押し）で管理メニューを開きます。`/sudoku menu` でも開けます。
- **数値** … ＋/−ボタンで最低開始人数・個人のミス上限・協力のミス上限・制限時間（対戦・協力・陣取り）・カウントダウン・共有盤面の番号を変更（即保存）。
- **対戦の終わり方** … 「1位で終了」「全員完成まで」のボタンで切り替え（即保存）。
- **難易度設定** … モードごとに初級・中級・上級のボタンで選ぶ。
- **地点** … ボタンを押すと立ち位置に設定（ロビー・初期スポーン）。
- **盤面設定** … 盤面1〜8を選び「ここに建てる」「同じ場所に建て直す」「撤去（5秒以内に2回押し）」。
- **看板** … 看板を見ながら（5ブロック以内）押すと参加／離脱／ソロ／開始看板を登録、ハサミで登録解除。
- **運用** … 開始・設定状況・リロード・問題プールを作り直す・ランキングを消す・強制停止（問題プール・ランキング・強制停止は5秒以内に2回押し）。

!!! note "管理ツールの扱い"
    管理ツールはOP専用です。ロビーで待機している間も受け取れます（試合中・観戦中・ソロ中は受け取れません）。試合が始まると持ち物と一緒に回収され、ロビー待機中に受け取った場合はロビーから抜けたときに自動で手元に戻ります。捨てると消えるので、再度 `/sudoku wand` で取得してください。

## 問題の作り方

問題は自動で作ります。**答えが1つに決まる** 問題だけを使い、人が使う解き方だけで解けるかどうかで難易度を決めています（仮定して試す解き方は不要）。

| 難易度 | 最初からある数字 | 必要な解き方 |
|---|---|---|
| 初級（easy） | 36〜40 | 1つしか入らないマス・1か所しか置けない数字だけ |
| 中級（normal） | 30〜35 | 上記＋ブロックと行/列の絞り込みまで |
| 上級（hard） | 24〜29 | 上記＋候補の組（ペア・トリプル）まで |

- 難易度ごとに作り置き（既定5問・`puzzle-pool.size-per-difficulty`）を `puzzles.yml` に保存し、使ったら裏で補充します。
- 出題時に数字の置き換えや行・列の入れ替え（`puzzle-pool.shuffle-on-use`）をかけるので、同じ問題に見えにくくなっています。

## config.yml 設定項目

コマンドで登録する地点・盤面・看板のデータ（`locations`・`boards`・`signs`）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。管理メニューで変えられる項目はそちらからでも変更できます。

### ゲーム全体（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `game.min-players` | 2 | ロビーから始める試合の最低人数（1 で練習用に1人開始可） |
| `game.max-lobby` | 16 | ロビーの定員 |
| `game.countdown-seconds` | 5 | 開始までのカウントダウン（秒。ソロは3秒固定） |
| `game.input-cooldown-ticks` | 10 | 陣取りの入力クールダウン（20=1秒。他モードは2tick） |
| `game.shared-board` | 1 | 協力・陣取りで使う盤面の番号 |
| `game.end-delay-seconds` | 6 | 結果表示からロビーへ戻すまでの秒数 |
| `game.protect-lobby` | true | 在籍中はダメージを受けない（奈落は除く） |

### モード（`modes.<モード>`）

| キー | 既定値 | 説明 |
|---|---|---|
| `modes.<モード>.difficulty` | normal（協力は hard） | 既定の難易度（easy / normal / hard） |
| `modes.<モード>.miss-limit` | 3（協力は5） | ミス上限（協力はチーム合計。0=無制限） |
| `modes.<モード>.time-limit-seconds` | 900（陣取りは600） | 制限時間（秒。ソロは無し） |
| `modes.<モード>.max-players` | 協力/陣取り8 | モードの最大人数 |
| `modes.versus.end-mode` | first-finish | first-finish=1位で終了 / all-finish=全員完成まで |

### ランキング・問題（`ranking` / `puzzle-pool`）

| キー | 既定値 | 説明 |
|---|---|---|
| `ranking.size` | 10 | `/sudoku top <難易度>` で表示する人数（3〜30） |
| `ranking.announce-first` | true | ソロで1位を更新したらサーバー全体に知らせる |
| `puzzle-pool.size-per-difficulty` | 5 | 難易度ごとの作り置きの問題数 |
| `puzzle-pool.shuffle-on-use` | true | 出題時に数字の置き換え・行列の入れ替えで見た目を変える |

### 盤面の見た目（`board`）

| キー | 既定値 | 説明 |
|---|---|---|
| `board.given-block` | QUARTZ_BLOCK | 最初からある数字のマスの床（試合中だけ） |
| `board.empty-block` | WHITE_CONCRETE | 空きマスの床 |
| `board.box-line-block` | BLACK_CONCRETE | 3×3 の区切り |
| `board.frame-block` | POLISHED_DEEPSLATE | 外枠 |
| `board.map-rotation-offset` | 0 | 数字が横向き・逆さに見えたら 1〜3 に変えて盤面を建て直す |

!!! note "データ保存用ファイル"
    `puzzles.yml`（問題の作り置き）、`stats.yml`（ソロの自己ベスト・ランキング）、`boards-backup.yml`（盤面を建てる前のブロック）に保存されます。地点・盤面・看板のデータは手書き不要です。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/sudoku setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/sudoku setlobby` | ロビー地点を設定 |
| `/sudoku setboard <番号>` | 立ち位置を基準に盤面を建てて登録（1〜8。同じ番号なら建て直し） |
| `/sudoku delboard <番号>` | 盤面を撤去して登録解除（建てる前のブロックに戻す） |
| `/sudoku setsign <join\|leave\|solo\|start <モード>\|delete>` | 看板を登録／解除（視線先） |
| `/sudoku refresh` | 近くの盤面（試合中は自分の盤面）の表示を送り直す |
| `/sudoku resettop <easy\|normal\|hard\|all>` | ソロのランキングを消す（元に戻せません） |
| `/sudoku wand` | 管理ツール（ブレイズロッド）を受け取る |
| `/sudoku menu` | 管理メニュー（GUI）を開く |
| `/sudoku stop [モード\|プレイヤー]` | 試合を強制停止（勝敗なし・在籍は維持。ソロはプレイヤー名で指定） |
| `/sudoku reload` | config.yml を再読み込み |
| `/sudoku lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/sudoku status` | 設定状況・現在の状況を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/sudoku join` | ロビーに参加（参加看板と同等） |
| `/sudoku leave` | ロビーから離脱（離脱看板と同等。試合中は脱落扱い） |
| `/sudoku start <versus\|coop\|territory> [難易度]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/sudoku solo` | ソロの難易度選択画面を開く |
| `/sudoku top [easy\|normal\|hard]` | ソロのランキング |
| `/sudoku refresh` | 近くの盤面の表示を送り直す |
| `/sudoku status` | 設定・状況を確認（読み取り専用） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `sudoku.admin` | OP | 看板設置・`setboard`・`delboard`・各 set 系・`resettop`・`wand`・`menu`・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/sudoku join`・`/sudoku leave`・`/sudoku start`・`/sudoku solo`・`/sudoku top`・`/sudoku refresh`・`/sudoku status` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/sudoku status` を確認してください。対戦は盤面が人数分、協力・陣取りは共有盤面が必要で、ロビーに最低開始人数（既定2人）が揃っている必要があります。人数が揃っても自動では始まりません。開始看板・`start` コマンド・管理メニューで開始してください。

??? failure "数字が横向き・逆さに見える"
    config の `board.map-rotation-offset` を 1〜3（90度刻み）に変え、管理メニューの「同じ場所に建て直す」を押してください。

??? failure "統合版でマスがオレンジのアイコンのまま"
    盤面のそばで `/sudoku refresh` を実行すると地図を送り直せます。試合開始の数秒後にも自動で送り直します。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/sudoku lobbyfix <プレイヤー>` で復旧できます。退避データはファイルに保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ナンプレ】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。試合結果も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイル（`lobby-inventory.yml`）に保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない・ロックが残った場合はOPが `/sudoku lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は旧ファイルを `config.yml.bak-日時` に退避）。地点・盤面・看板などのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも管理ツール・ソロメニューを使え、登録看板は蝋引きして編集画面が開かないようにします。数字は額縁の地図で表示し、届かなかった地図は自動で送り直します（`/sudoku refresh` でも送り直せます）。
- **観戦（GhostSpectator）** … 対戦・陣取りで脱落したプレイヤーは生き残っている人の視点を観戦します（スニークで対象切り替え）。統合版は観戦モードが扱いづらいため、方式を自動で切り替えます（`bedrock.spectator-style`）。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | false | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | （使われません。未復元の退避データは期限切れで捨てず、次のログインで必ず返します） |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | ナンプレ | 「【】」に入れる正式名 |
| `name-tag.color` | AQUA | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも管理ツール・ソロメニューを使える |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.spectator-style` | AUTO | 観戦方式（AUTO＝統合版だけゴースト観戦／GHOST／VANILLA） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ナンプレ（Sudoku） 概要へ](index.md){ .md-button }
