<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# UNO ― OP・運営ガイド { .page-op #uno-op }

UNO の導入・卓/席/看板の設定・CPU・config・権限・管理コマンドをまとめます。地点・卓・席・看板はコマンドや管理メニュー（`/uno menu`）で登録すると、即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Uno |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/uno` |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/Uno/config.yml` |
| 権限ノード | `uno.admin`（既定OP） |

## 導入手順

1. ビルドした `Uno` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される（人数・ルールなどは初期値で動作）。
3. `/uno setstartspawn`・`/uno setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/uno createtable <卓名>` で卓を作り、`/uno settable`（中央）・`/uno setseat`（席）を設定する。
5. 参加・離脱・開始の看板を設置する。
6. `/uno status` で設定状況（開始可能かどうか）を確認する。

!!! note "試合に必須の設定"
    1つの卓を開始可能にするには、**卓の中央（settable）** と **席を2つ以上（setseat）** が必要です。席を1つしか登録していないと開始できません。席を登録しないと、卓の中央を囲んで自動で並べます（`table.auto-radius`）。初期スポーンとロビーは全卓共通です。

## セットアップ手順（コマンド）

地点・中央・席は **実行した位置** を使い、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `uno.admin` 権限が必要です。卓の設定は、先に `/uno createtable <卓名>` で卓を作ってから行います。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/uno setstartspawn
```

```text title="受付ロビー（全卓共通／その場に立って実行）"
/uno setlobby
```

```text title="卓を作る"
/uno createtable <卓名>
```

```text title="卓の中央（その場に立って実行）"
/uno settable <卓名>
```

```text title="席を登録（1〜8／席に立って実行）"
/uno setseat <卓名> <1〜8>
```

```text title="席を削除"
/uno delseat <卓名> <1〜8>
```

```text title="参加看板を登録（看板を見て実行）"
/uno setsign join <卓名>
```

```text title="開始看板を登録（看板を見て実行）"
/uno setsign start <卓名>
```

```text title="離脱看板を登録（全卓共通・看板を見て実行）"
/uno setsign leave
```

```text title="視線先の看板の登録を解除"
/uno setsign delete
```

```text title="卓を削除（席・中央・この卓の看板も消える／5秒以内に2回で確定）"
/uno deletetable <卓名>
```

!!! note "卓名の省略について"
    プレイヤー向けコマンド（`join`・`start`・`status`）は、卓が1つしか無いときは卓名を省略できます。2つ以上あるときは卓の一覧を出して指定を求めます。卓名に「.」は使えません。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール（ブレイズロッド）** と **管理メニュー（GUI）** でも卓・席・中央・看板・CPU を設定できます。どちらも `uno.admin` 権限が必要です。

```text title="管理ツール（ブレイズロッド）を受け取る"
/uno wand
```

```text title="管理メニュー（GUI）を開く"
/uno menu [卓名]
```

- **管理メニュー** … 卓の選択、卓の中央・席の「現在地に設定」、参加／開始看板の登録・看板登録の解除、**CPU補充人数（0〜8）**・**CPU難易度**（かんたん／ふつう／むずかしい）の変更、全卓共通の設定（ロビー・初期スポーン・離脱看板・数値・ルール）、試合の開始・設定状況の表示・設定の再読み込み・管理ツールの受け取りを、GUI からまとめて操作できます。数値は＋／−ボタンで変えます（統合版でも同じ操作）。
- **管理ツール** … `/uno wand` で受け取り、**右クリック／タップ** で管理メニューを開きます。OP専用で、受け取り・使用の両方で `uno.admin` を確認します。試合中は受け取れません（ロビー待機中は受け取れます）。捨てると消えます（`/uno wand` で取り直せます）。

!!! note "設定の反映タイミング"
    数値・ルールなどの config は **次の試合から** 反映されます。`/uno reload` で再読み込みできますが、**試合中の卓があるとできません**。卓・席・中央・看板のデータはコマンド／メニューの実行で即保存されます。

## config.yml 設定項目

コマンドで登録する地点・卓・席・看板のデータ（`default-spawn`・`lobby-spawn`・`leave-signs`・`tables`）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。以下は人数・ルールなどの設定項目です。

### 人数・進行（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `game.min-players` | 2 | 開始に必要な人数（人間＋CPU） |
| `game.max-players` | 8 | 1卓に参加できる人間の上限（登録した席の数が上限） |
| `game.countdown-seconds` | 5 | 開始前のカウントダウン（秒） |
| `game.hand-size` | 7 | 最初に配る枚数 |
| `game.end-mode` | first | `first`＝1人上がったら終了（残りは手札の点数で順位）／`all`＝全員の順位が決まるまで |
| `game.result-seconds` | 3 | 試合終了後、結果を表示しておく秒数 |

### ルール（`rules`）

| キー | 既定値 | 説明 |
|---|---|---|
| `rules.stacking.enabled` | true | ドロー2/4 を重ねて次の人へ回せる |
| `rules.stacking.draw4-on-draw2` | true | ドロー2 にドロー4 を重ねられる |
| `rules.stacking.draw2-on-draw4` | false | ドロー4 にドロー2 を重ねられる |
| `rules.challenge` | true | ドロー4 を出された人がチャレンジできる |
| `rules.draw-then-play` | true | 引いたカードが出せればそのまま出せる |
| `rules.first-card-number-only` | true | 最初の場札は数字カードになるまでめくり直す |

### UNO 宣言（`uno-call`）

| キー | 既定値 | 説明 |
|---|---|---|
| `uno-call.enabled` | true | 宣言忘れを指摘されると penalty-cards 枚引く |
| `uno-call.penalty-cards` | 2 | 宣言忘れのペナルティ枚数 |
| `uno-call.catch-window-seconds` | 3 | 宣言忘れを指摘できる秒数 |

### 制限時間（`turn-timer`）

| キー | 既定値 | 説明 |
|---|---|---|
| `turn-timer.enabled` | true | ターンの制限時間を使う |
| `turn-timer.turn-seconds` | 30 | 通常のターン（時間切れ：1枚引いてパス。累積中なら累積分を引く） |
| `turn-timer.color-seconds` | 10 | 色選び（時間切れ：手札に一番多い色） |
| `turn-timer.challenge-seconds` | 10 | チャレンジするか選ぶ時間（時間切れ：受ける） |
| `turn-timer.drawn-card-seconds` | 10 | 引いたカードを出すか選ぶ時間（時間切れ：パス） |
| `turn-timer.warn-seconds` | 5 | 残りこの秒数で効果音 |

### CPU（`cpu`）

| キー | 既定値 | 説明 |
|---|---|---|
| `cpu.enabled` | true | CPU補充を有効にする |
| `cpu.allow-solo` | true | 人間1人でもCPUと練習できる（補充人数が0でも1体入る） |
| `cpu.fill-to` | 4 | 卓ごとの設定が無いときの既定：合計がこの人数になるまでCPUを入れる（0＝補充しない） |
| `cpu.difficulty` | normal | 卓ごとの設定が無いときの既定：easy／normal／hard |
| `cpu.levels.<id>.think-ticks-min` / `max` | 難易度ごと | CPUの考える時間（20＝1秒。easy 30〜50／normal 20〜40／hard 15〜30） |

卓ごとの CPU補充人数・難易度は管理メニューで設定でき、`tables.<卓>.cpu-fill-to`・`cpu-difficulty` に保存されます（全卓共通の既定より優先）。

### 卓・環境（`table` ほか）

| キー | 既定値 | 説明 |
|---|---|---|
| `table.freeze-players` | true | 試合中は自分の席から動けないようにする |
| `table.name-height` | 2.3 | 席の上に出す名前表示の高さ |
| `table.auto-radius` | 2.5 | 席が未登録のとき、卓の中央を囲んで自動で並べる円の半径 |
| `table.color-center-block` | true | 卓の中央の足元ブロックを場の色のコンクリートにする（試合後に元へ戻す） |
| `keep-food` | true | ロビー在籍中は満腹度を減らさない |
| `protect-lobby` | true | ロビー在籍中はダメージを受けない |
| `messages.prefix` | `&8[&cUNO&8] &r` | メッセージの接頭辞 |

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/uno createtable <卓名>` | 卓を作る |
| `/uno deletetable <卓名>` | 卓を削除（席・中央・この卓の看板も解除。5秒以内に2回で確定） |
| `/uno setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/uno setlobby` | ロビー地点を設定（全卓共通） |
| `/uno settable <卓名>` | 卓の中央を設定 |
| `/uno setseat <卓名> <1〜8>` | 席を登録 |
| `/uno delseat <卓名> <1〜8>` | 席を削除 |
| `/uno setsign <join <卓名>\|start <卓名>\|leave\|delete>` | 看板を登録／解除 |
| `/uno wand` | 管理ツール（ブレイズロッド）を受け取る |
| `/uno menu [卓名]` | 管理メニュー（GUI）を開く |
| `/uno stop [卓名]` | 試合を強制停止 |
| `/uno reload` | config を再読み込み（試合中の卓があるとできません） |
| `/uno leave <プレイヤー>` | 指定したプレイヤーを離脱させる |
| `/uno lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/uno status [卓名]` | 設定状況・現在の状況を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/uno join [卓名]` | 卓に参加（参加看板と同等。卓が1つなら卓名省略可） |
| `/uno leave` | 卓から離脱（離脱看板と同等。試合中は脱落扱い） |
| `/uno start [卓名]` | 試合を開始（開始看板と同等） |
| `/uno hand` | 手札の画面を開く |
| `/uno status [卓名]` | 設定・状況を確認（読み取り専用） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `uno.admin` | OP | 卓の作成・セットアップ・看板設置・管理ツール・管理メニュー・`stop`・`reload`・`leave <プレイヤー>`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/uno join`・`/uno leave`（自分）・`/uno start`・`/uno hand`・`/uno status` は権限チェックがなく、全プレイヤーが使えます。`/uno leave <プレイヤー>` で他人を指定できるのは `uno.admin` を持つOPだけです。

## トラブルシューティング

??? failure "試合が開始できない"
    `/uno status` を確認してください。卓の **中央** が設定済みで、**席が2つ以上** 登録されている必要があります。席が1つだけだと開始できません。人間が1人だけでも、CPU補充（`cpu.enabled`・`cpu.allow-solo`）が有効なら相手が入って始められます。

??? failure "設定を変えても反映されない"
    数値・ルールなどの config は **次の試合から** 反映されます。`/uno reload` で再読み込みできますが、**試合中の卓があるとできません**。すべての卓の試合が終わってから実行してください。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/uno lobbyfix <プレイヤー>` で復旧できます。退避データはファイルに保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。先にその人を在籍から外す必要がある場合は `/uno leave <プレイヤー>` を実行してください。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【UNO】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。あだ名プラグイン（Nickname）を入れている場合、チャット・Tab・参加告知はあだ名で表示されます。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます（「他のミニゲーム ○○ に参加中です」）。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、サーバークラッシュ後の再ログインでも復元します。異常で持ち物が戻らない・ロックが残った場合はOPが `/uno lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は旧ファイルを `config.yml.bak` に退避）。地点・卓・席・看板などのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも手札の本・UNO!・指摘・管理ツールを使え、登録看板は蝋引きして編集画面が開かないようにします。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | false | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | 未復元の退避データの保持日数 |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | UNO | 「【】」に入れる正式名 |
| `name-tag.color` | RED | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも手札の本・UNO!・指摘・管理ツールを使える |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← UNO 概要へ](index.md){ .md-button }
