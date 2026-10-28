<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# マインスイーパー ― OP・運営ガイド { .page-op #minesweeper-op }

マインスイーパー（Minesweeper）の導入・ステージ設定・盤面の敷き方・看板・config・権限・管理コマンドをまとめます。地点・ステージ・看板はコマンド（または管理メニュー）で登録すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Minesweeper |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/minesweeper`（別名 `/msw`） |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/Minesweeper/config.yml` |
| 権限ノード | `minesweeper.admin`（既定OP） |

## 導入手順

1. ビルドした `Minesweeper` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される（難易度・モード既定値などは初期値で動作）。
3. `/minesweeper setstartspawn`・`/minesweeper setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/minesweeper setstage <ステージ> <モード> <難易度>` でステージを登録する。
5. 盤面を敷きたい位置に立ち、敷く方向を向いて `/minesweeper setboard <ステージ>` を実行する。
6. 参加・離脱・開始の看板を設置する。
7. `/minesweeper status` で設定状況を確認する。

!!! note "ステージと盤面"
    1つのステージ＝1つのモード＋1つの難易度です。`setstage` でステージを作り、`setboard` で盤面（マスを敷く範囲とプレイヤーのスポーン）を登録します。**対戦モードは人数分の盤面** が要るので、`setboard <ステージ> <番号>` で番号を変えながら複数の盤面を敷きます（最大4つ＝最大人数）。難易度を変えて盤面サイズが変わったら、そのステージの `setboard` をやり直して敷き直してください。

## セットアップ手順（コマンド）

地点・盤面は **実行した位置・向き** を使い、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `minesweeper.admin` 権限が必要です。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/minesweeper setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/minesweeper setlobby
```

```text title="ステージを登録（モード：solo/versus/coop/turn・難易度：easy/normal/hard）"
/minesweeper setstage <ステージ> <solo|versus|coop|turn> <easy|normal|hard>
```

```text title="盤面を敷く（立ち位置＝スポーン、向いた方向へ盤面。番号は対戦の盤面用・省略で1）"
/minesweeper setboard <ステージ> [番号]
```

```text title="ステージを削除（5秒以内に2回実行で確定）"
/minesweeper delstage <ステージ>
```

```text title="参加看板を登録（看板を見て実行）"
/minesweeper setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/minesweeper setsign leave
```

```text title="開始看板を登録（ステージ指定・看板を見て実行）"
/minesweeper setsign start <ステージ>
```

```text title="視線先の看板の登録を解除"
/minesweeper setsign delete
```

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール（ワンド）** と **管理メニュー（GUI）** でもステージごとの値（人数・ライフ・制限時間など）や地点・看板を設定できます。どちらも `minesweeper.admin` 権限が必要です。

```text title="管理ツール（ワンド）を受け取る"
/minesweeper wand
```

```text title="管理メニュー（GUI）を開く"
/minesweeper menu
```

- 管理メニューからは、ステージごとの最低/最大人数・協力のライフ・交代制の持ち時間・制限時間などを変更できます（`stages.<ステージ>` に保存）。

## config.yml 設定項目

コマンドで登録する地点・看板・ステージのデータ（`lobby-spawn`・`default-spawn`・`signs`・`stages`）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。変更は「次の試合から」反映されます（`/minesweeper reload` で再読み込み）。

### 難易度（`difficulties`）

盤面サイズ（マス）と地雷数。地雷数は「マス数 − 9」までに自動で抑えられます（初手の周囲9マスを空けるため）。サイズを変えたら `setboard` で敷き直してください。

| 難易度 | width | height | mines | time-limit-minutes |
|---|---|---|---|---|
| `easy` | 9 | 9 | 10 | 5 |
| `normal` | 16 | 16 | 40 | 10 |
| `hard` | 30 | 16 | 99 | 20 |

### モードごとの既定値（`modes`）

ステージ登録時の初期値（ステージごとの値は管理メニューで変更可）。

| モード | 既定値 |
|---|---|
| `solo` | min-players 1 / max-players 1 / max-minutes 60 |
| `versus` | min-players 2 / max-players 4 |
| `coop` | min-players 2 / max-players 8 / team-lives 1 |
| `turn` | min-players 2 / max-players 6 / turn-seconds 20 |

### 進行

| キー | 既定値 | 説明 |
|---|---|---|
| `countdown-seconds` | 5 | 開始前のカウントダウン（秒） |
| `result-seconds` | 5 | 決着後、地雷の位置（答え）を見せておく秒数 |
| `lobby-max-players` | 16 | ロビーの最大人数 |
| `chord-open` | true | 数字マスをシャベルで押すとコード開き |
| `reach-distance` | 6 | マスを操作できる距離（ブロック） |
| `click-interval-ms` | 200 | 同じ人の操作間隔（ミリ秒） |
| `same-cell-interval-ms` | 500 | 同じマスへの連続操作の間隔（ミリ秒） |
| `show-number-text` | true | 数字の TextDisplay を出す（色ブロックだけでも遊べる） |
| `number-text-billboard` | false | true で数字が床に寝ず、常に自分の方を向いて浮かぶ |
| `keep-food` | true | 在籍中は満腹度を減らさない |
| `protect-lobby` | true | 在籍中はダメージを受けない（奈落は除く） |

### ソロメニュー（`solo-menu`）

| キー | 既定値 | 説明 |
|---|---|---|
| `solo-menu.lobby-item` | true | ロビー待機中はネザースターを持たせる（右クリック/タップでソロメニュー） |
| `solo-menu.lobby-item-slot` | 4 | ネザースターを置くホットバーの位置（0〜8） |
| `solo-menu.reopen-after-solo` | true | ソロの試合が終わってロビーに戻ったらソロメニューを開く |

### 連鎖開封の演出（`reveal-animation`）

| キー | 既定値 | 説明 |
|---|---|---|
| `reveal-animation.enabled` | true | 波紋の演出を出す |
| `reveal-animation.ticks-per-step` | 2 | 波紋が1マス広がる間隔（tick） |
| `reveal-animation.sound` | true | 広がるたびに小さな音を鳴らす |

### ブロック（`blocks`）

| キー | 既定値 | 説明 |
|---|---|---|
| `blocks.hidden-a` / `hidden-b` | GRASS_BLOCK / MOSS_BLOCK | 未開封マスの市松模様の2色 |
| `blocks.open-zero` | SMOOTH_SANDSTONE | 周囲に地雷がない（0）開封マス |
| `blocks.numbers` | 青/緑/赤/紫/茶/シアン/黒/薄灰のコンクリート | 数字1〜8のマスの色 |
| `blocks.flag` | RED_BANNER | 旗 |
| `blocks.mine` | TNT | 地雷（結果表示で見せる） |
| `blocks.wrong-flag` | COAL_BLOCK | 外れていた旗（結果表示） |

### ランキング・表示

| キー | 既定値 | 説明 |
|---|---|---|
| `ranking-size` | 10 | `/minesweeper top` の表示件数 |
| `messages.prefix` | `[マインスイーパー]` | メッセージの接頭辞 |

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/minesweeper setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/minesweeper setlobby` | ロビー地点を設定 |
| `/minesweeper setstage <ステージ> <solo\|versus\|coop\|turn> <easy\|normal\|hard>` | ステージを登録 |
| `/minesweeper setboard <ステージ> [番号]` | 盤面を敷く（立ち位置＝スポーン、向いた方向へ盤面） |
| `/minesweeper delstage <ステージ>` | ステージを削除（5秒以内に2回） |
| `/minesweeper setsign <join\|leave\|start <ステージ>\|delete>` | 看板を登録／解除 |
| `/minesweeper wand` | 管理ツールを受け取る |
| `/minesweeper menu` | 管理メニュー（GUI）を開く |
| `/minesweeper stop [ステージ]` | 試合を強制停止 |
| `/minesweeper reload` | 設定を再読み込み |
| `/minesweeper lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/minesweeper resettop [難易度]` | ソロのランキングを初期化（難易度省略で全難易度・確認付き） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/minesweeper join` | ロビーに参加（参加看板と同等） |
| `/minesweeper leave` | ロビーから離脱（離脱看板と同等。試合中は脱落扱い） |
| `/minesweeper start [ステージ]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/minesweeper solo` | ソロの難易度を選んですぐ開始（メニュー） |
| `/minesweeper status` | 設定・状況を確認（読み取り専用） |
| `/minesweeper top [easy\|normal\|hard]` | ソロのランキング |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `minesweeper.admin` | OP | ステージ・盤面・看板・地点の各 set 系・`delstage`・`wand`・`menu`・`stop`・`reload`・`lobbyfix`・`resettop` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/minesweeper join`・`/minesweeper leave`・`/minesweeper start`・`/minesweeper solo`・`/minesweeper status`・`/minesweeper top` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/minesweeper status` を確認してください。ステージが登録済みで、盤面（対戦は人数分）が敷かれ、ロビーにそのモードの最低人数以上いる必要があります。

??? failure "盤面のサイズが合っていない／数字がずれる"
    難易度を変えて盤面サイズが変わったときは、そのステージの `/minesweeper setboard` をやり直して敷き直してください。立ち位置がスポーン、向いた方向へ盤面が展開します。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/minesweeper lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/Minesweeper/` 以下に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【マインスイーパー】名前」になり、離脱・ログアウト・プラグイン停止で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます（サーバー共通のスコアボードタグで所有権を管理）。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない場合はOPが `/minesweeper lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません）。地点・看板・ステージなどのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。マスを見て空中タップ（腕振り）でも操作でき、登録看板は蝋引きして編集画面が開かないようにします。
- **観戦（GhostSpectator）** … 脱落したプレイヤーは観戦に回ります。統合版は観戦モードが扱いづらいため、方式を自動で切り替えます（`bedrock.spectator-style`）。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | 未復元の退避データの保持日数 |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | マインスイーパー | 「【】」に入れる正式名 |
| `name-tag.color` | GREEN | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.swing-to-use` | true | 統合版はマスを見て空中タップ（腕振り）でも操作できる |
| `bedrock.spectator-style` | AUTO | 観戦方式（AUTO＝統合版だけ透明＋追従TP／GHOST＝全員／VANILLA＝全員観戦モード） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← マインスイーパー 概要へ](index.md){ .md-button }
