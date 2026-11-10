<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ネコマス（Nekomasu） ― OP・運営ガイド { .page-op #nekomasu-op }

ネコマスの導入・初期スポーン/ロビー/盤面/看板の設定・管理ツール・ソロのレベル・難易度・config・権限・管理コマンドをまとめます。地点・盤面・看板はコマンドや管理メニューで設定すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Nekomasu |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/nekomasu`（別名 `/neko`） |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/Nekomasu/config.yml` |
| 権限ノード | `nekomasu.admin`（既定OP） |

## 導入手順

1. ビルドした `Nekomasu` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される（人数・時間・難易度は初期値で動作）。
3. `/nekomasu setstartspawn`・`/nekomasu setlobby` で初期スポーン（抜けた人の戻り先）とロビーを設定する。
4. `/nekomasu setboard <番号>`（対戦・協力用）・`/nekomasu setsoloboard <番号>`（ソロ用）で盤面を登録する。
5. 参加・離脱・ソロ・開始（対戦/協力）の看板を設置する。
6. `/nekomasu status` で設定状況を確認する。

!!! note "必要な盤面の数"
    **対戦** は1人1面を使うので、最大人数ぶん（最大4面・`/nekomasu setboard`）を登録します。登録した盤面の数より多い人数にはできません。**協力** は盤面1番だけを使います。**ソロ** 用の盤面は別に登録します（`/nekomasu setsoloboard`・最大4面＝同時に4人まで）。

## セットアップ手順（コマンド）

地点は **実行した位置** を使い、盤面は **立ち位置の前方** に建て、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `nekomasu.admin` 権限が必要です。

```text title="初期スポーン（抜けた人の戻り先／その場に立って実行）"
/nekomasu setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/nekomasu setlobby
```

```text title="対戦・協力用の盤面を登録（立ち位置の前方 11×11。1〜4）"
/nekomasu setboard 1
```

```text title="2面目の対戦・協力用の盤面を登録"
/nekomasu setboard 2
```

```text title="ソロ用の盤面を登録（1〜4）"
/nekomasu setsoloboard 1
```

```text title="盤面の登録を解除（board=対戦協力 / solo=ソロ）"
/nekomasu removeboard board 1
```

```text title="参加看板を登録（看板を見て実行）"
/nekomasu setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/nekomasu setsign leave
```

```text title="対戦の開始看板を登録（看板を見て実行）"
/nekomasu setsign start versus
```

```text title="協力の開始看板を登録（看板を見て実行）"
/nekomasu setsign start coop
```

```text title="ソロ看板を登録（看板を見て実行）"
/nekomasu setsign solo
```

```text title="視線先の看板の登録を解除"
/nekomasu setsign delete
```

!!! note "盤面を建てるときの注意"
    登録した人の **立ち位置の前方 11×11 ブロック（外枠込み）** を使い、その1段下が床になります。向いている方向（東西南北に丸める）に盤面が伸び、列は左右中央にそろいます。盤面の範囲とその1段上は試合中にプラグインが書き換え、終了時・停止時・次回起動時に元のブロックへ戻します（`board-backup.yml`）。**平らで何もない場所** にしてください。盤面どうしは **12ブロック以上** 離してください。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール（ブレイズロッド）** と **管理メニュー（GUI）** でも地点・盤面・看板を設定できます。どちらも `nekomasu.admin` 権限が必要です。

```text title="管理ツール（ブレイズロッド）を受け取る"
/nekomasu wand
```

```text title="管理メニュー（GUI）を開く"
/nekomasu menu
```

- 管理ツールを右クリック（統合版はタップ／長押し）で管理メニューを開きます。`/nekomasu menu` でも開けます。
- **数値** … ＋/−ボタンでライフ・ヒント回数・カウントダウン・対戦の制限時間・対戦の最低/最大人数・協力の制限時間・協力の最大人数を変更（即保存）。対戦の終わり方（1位で終了／全員完成まで）は時計のボタンで切り替え。
- **地点** … ボタンを押すと立ち位置に設定（ロビー・初期スポーン・対戦/協力の盤面1〜4・ソロの盤面1〜4）。
- **看板** … 看板を見ながら（5ブロック以内）押すと参加／離脱／開始（対戦・協力）／ソロ看板を登録、ハサミで登録解除。
- **運用** … 開始（対戦・協力）・設定状況・リロード・強制停止（強制停止は5秒以内に2回押し）。

!!! note "管理ツールの扱い"
    管理ツールはOP専用です。ロビーで待機している間も受け取れます（試合中・観戦中・ソロ中は受け取れません）。試合が始まると持ち物と一緒に回収され、ロビー待機中に受け取った場合はロビーから抜けたときに自動で手元に戻ります。捨てると消えるので、再度 `/nekomasu wand` で取得してください。

## ソロのレベル（puzzles.yml）

- `plugins/Nekomasu/puzzles.yml` に、上から順にレベル1, 2, 3 … の問題を書きます（初期は15問）。
- 1行1文字列で、同じ文字（A〜P）のマスが同じ色エリアです。大きさは 4〜9、エリアの数は大きさと同じ数にし、各エリアはつながっていることが条件です。
- 読み込み時に「答えが1つだけか」「エリアがつながっているか」を確認し、だめな問題は警告を出して飛ばします。
- 全部クリアした人は自動生成の「エンドレス」に進みます（3レベルごとに大きくなり、9×9 まで）。
- 編集したら `/nekomasu reload` で読み直します。記録（到達レベル・ベストタイム）は `stats.yml` に保存されます。

## 難易度（config.yml の `difficulty`）

| キー | 名前 | 大きさ | 問題の条件（`logic`） |
|---|---|---|---|
| `easy` | かんたん | 5×5 | LOGIC_ONLY（仮置きなしで解ける） |
| `normal` | ふつう | 6×6〜7×7 | LOGIC_ONLY（仮置きなしで解ける） |
| `hard` | むずかしい | 8×8 | ANY（条件なし） |
| `oni` | 鬼 | 9×9 | PREFER_HARD（仮置きが必要な問題を優先） |

- 大きさは `size`〜`size-max` の範囲で自動生成します（4〜9）。対戦・協力で使う難易度は `versus.difficulty` / `coop.difficulty`、または `/nekomasu start <モード> <難易度>` で指定します。

## config.yml 設定項目

コマンドで登録する地点・盤面・看板のデータ（`default-spawn`・`lobby-spawn`・`boards`・`solo-boards`・`signs`）は自動保存されます。手書き不要です。設定の変更は「次の試合から」反映されます（`/nekomasu reload` で再読み込み）。

### ゲーム全体（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `game.lives` | 3 | ライフ（間違えると1減り、0で失格） |
| `game.hints` | 3 | ヒント回数（正解のネコを1匹置く） |
| `game.countdown` | 5 | 対戦・協力の開始カウントダウン（秒） |
| `game.result-seconds` | 5 | 終了後、盤面を残しておく秒数 |
| `game.auto-mark-default` | false | 自動×の初期値（各自が道具で ON/OFF できる） |
| `game.click-range` | 6 | 盤面のマスを指せる距離（ブロック） |
| `game.keep-food` | true | 在籍中は満腹度を減らさない |
| `game.protect-lobby` | true | 在籍中はダメージを受けない |

### ロビー・各モード

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby.max-players` | 20 | ロビーに入れる人数 |
| `versus.min-players` | 2 | 対戦の最低人数 |
| `versus.max-players` | 4 | 対戦の最大人数（登録盤面の数より多くはできない） |
| `versus.time-limit` | 600 | 対戦の制限時間（秒） |
| `versus.end-mode` | first | first=1位で終了 / all=全員完成（または失格・時間切れ）まで |
| `versus.difficulty` | normal | 対戦の既定難易度 |
| `coop.max-players` | 4 | 協力の最大人数 |
| `coop.time-limit` | 900 | 協力の制限時間（秒） |
| `coop.difficulty` | hard | 協力の既定難易度 |
| `solo.menu-delay-seconds` | 3 | ソロ終了後にソロメニューを開き直すまでの秒数 |

### 盤面の見た目（`board`）

| キー | 既定値 | 説明 |
|---|---|---|
| `board.palette` | （色コンクリート12色） | 色エリアに使うブロック（上から順に。9色以上必要） |
| `board.border` | BLACK_CONCRETE | 外枠 |
| `board.mark` | POLISHED_BLACKSTONE_BUTTON | ×印（マスの上に置くボタン） |

### 表示（`messages`）

| キー | 既定値 | 説明 |
|---|---|---|
| `messages.prefix` | `&8[&dネコマス&8] &r` | チャットの接頭辞 |

## 試合の流れ・決着

- 人数が揃っても自動では始まりません。開始看板・`start` コマンド・管理メニューの開始ボタンで始めます。
- カウントダウン明けに、そのときロビーにいる人（ソロ中を除く）で参加者を確定します。定員を超えた人はロビーで待機します。
- **対戦** … 1位で終了／全員完成まで（管理メニューで切替）。自分以外が全員脱落したら残った人の勝ち、全員失格なら引き分け。時間切れは「完成 → ネコの数 → ミスの少なさ → 最後に置いた時刻」で順位。
- **協力** … 完成でクリア、共有ライフ0・時間切れで失敗。
- 結果の表示後（`game.result-seconds` 秒）、盤面を元のブロックに戻し、参加者をロビーへ戻します。在籍は続きます。
- 試合中の離脱・切断は脱落扱い。観戦するのは対戦で失格した人だけです。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/nekomasu setstartspawn` | 初期スポーン（抜けた人の戻り先）を設定 |
| `/nekomasu setlobby` | ロビー地点を設定 |
| `/nekomasu setboard <1-4>` | 対戦・協力用の盤面を登録（立ち位置の前方） |
| `/nekomasu setsoloboard <1-4>` | ソロ用の盤面を登録 |
| `/nekomasu removeboard <board\|solo> <番号>` | 盤面の登録を解除 |
| `/nekomasu setsign <join\|leave\|solo\|start <versus\|coop>\|delete>` | 看板を登録／解除（視線先） |
| `/nekomasu wand` | 管理ツール（ブレイズロッド）を受け取る |
| `/nekomasu menu` | 管理メニュー（GUI）を開く |
| `/nekomasu start <versus\|coop> [難易度] [seed:<値>]` | 開始（コンソール・コマンドブロック可。seed で同じ問題を再現） |
| `/nekomasu stop [versus\|coop\|solo]` | 強制停止（勝敗なし・在籍は維持。省略で全部止める） |
| `/nekomasu reload` | config.yml と puzzles.yml を再読み込み |
| `/nekomasu lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/nekomasu status` | 設定状況・現在の状況を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/nekomasu join` | 参加（ロビーへ） |
| `/nekomasu leave` | 離脱（初期地点へ。試合中は脱落扱い） |
| `/nekomasu solo` | ソロメニューを開く |
| `/nekomasu start <versus\|coop> [難易度]` | 対戦・協力を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/nekomasu status` | 設定・状況を確認（読み取り専用） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `nekomasu.admin` | OP | 看板設置・`setboard`・`setsoloboard`・`removeboard`・各 set 系・`wand`・`menu`・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/nekomasu join`・`/nekomasu leave`・`/nekomasu solo`・`/nekomasu start`・`/nekomasu status` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    対戦は盤面が人数分、協力は盤面1番が必要で、ロビーに最低人数（対戦は既定2人）が揃っている必要があります。人数が揃っても自動では始まりません。開始看板・`start` コマンド・管理メニューで開始してください。

??? failure "盤面を敷いたあとブロックが元に戻らない"
    盤面の範囲とその1段上の元のブロックは `board-backup.yml` に保存され、試合終了・強制停止・プラグイン停止で戻します。サーバーが落ちた場合も次の起動時に戻します。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/nekomasu lobbyfix <プレイヤー>` で復旧できます。退避データはファイルに保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ネコマス】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。試合結果も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない・ロックが残った場合はOPが `/nekomasu lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません）。地点・盤面・看板などのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。道具・×印・床の色は Java 版と同じように見え、登録看板は蝋引きして編集画面が開かないようにします。
- **観戦（GhostSpectator）** … 対戦で失格したプレイヤーは、まだ解いている人の視点を観戦します（スニークで対象切り替え）。統合版は観戦モードが扱いづらいため、方式を自動で切り替えます（`bedrock.spectator-style`／統合版は透明＋追従）。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | （使われません。未復元の退避データは期限切れで捨てず、次のログインで必ず返します） |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | ネコマス | 「【】」に入れる正式名 |
| `name-tag.color` | LIGHT_PURPLE | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.spectator-style` | AUTO | 観戦方式（AUTO＝統合版だけ透明＋追従／GHOST／VANILLA） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ネコマス（Nekomasu） 概要へ](index.md){ .md-button }
