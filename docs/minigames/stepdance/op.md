<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ステップダンス（StepDance） ― OP・運営ガイド { .page-op #stepdance-op }

ステップダンスの導入・アリーナ（座席と壁）設定・看板・曲（.nbs）の追加・config・権限・管理コマンドをまとめます。地点・ブース・看板はコマンド（または管理メニュー）で登録すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | StepDance |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/stepdance`（別名 `/sdance`） |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/StepDance/config.yml` |
| 曲フォルダ | `plugins/StepDance/songs/`（`.nbs` を置く） |
| 権限ノード | `stepdance.admin`（既定OP） |

## 導入手順

1. ビルドした `StepDance` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` と `songs/` フォルダが自動生成される（内蔵のテスト譜面だけでも動作）。
3. `/sdance setstartspawn`・`/sdance setlobby` で初期スポーン（途中抜けの戻り先）と共通ロビーを設定する。
4. 曲を鳴らすブースを置きたい方向を向いて座席にしたい場所に立ち、`/sdance addbooth <識別子>` でアリーナとブースを作る（向いた方向の先に壁が自動生成）。
5. `plugins/StepDance/songs/` に `.nbs` ファイルを置き、`/sdance reload` で読み込む。
6. 参加・離脱・開始の看板を設置する。
7. `/sdance status` で設定状況を確認する。

!!! note "アリーナ・ブース・壁の関係"
    1つの **アリーナ** に **ブース（座席）** を最大4つまで置けます。ブースは `/sdance addbooth` を実行した **立ち位置** が座席になり、実行時に **向いていた方向（東西南北に丸め）** の先に壁が自動で作られます（座席から既定8ブロック先・幅9・高さ10）。壁の形（高さ・座席からの距離）を変えたら、試合をしていない間に `/sdance rebuild` か管理メニューの「壁を作り直す」を実行してください。

## セットアップ手順（コマンド）

地点・ブースは **実行した位置** を使い、看板系は看板を **見ながら**（6ブロック以内）実行します。すべて `stepdance.admin` 権限が必要です。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/sdance setstartspawn
```

```text title="共通ロビー（その場に立って実行）"
/sdance setlobby
```

```text title="アリーナ別ロビー（そのアリーナ専用のロビー。その場に立って実行）"
/sdance setlobby <識別子>
```

```text title="ブースを追加（壁を出したい方向を向き、座席にしたい場所に立って実行）"
/sdance addbooth <識別子>
```

```text title="ブースを削除（番号は 1〜4）"
/sdance delbooth <識別子> <番号>
```

```text title="壁を作り直す（壁の高さ・距離を変えたあとに実行。識別子を省くと全アリーナ）"
/sdance rebuild [識別子]
```

```text title="参加看板を登録（看板を見て実行）"
/sdance setsign join <識別子>
```

```text title="離脱看板を登録（看板を見て実行・アリーナ不問）"
/sdance setsign leave
```

```text title="開始看板を登録（曲はおまかせ・看板を見て実行）"
/sdance setsign start <識別子>
```

```text title="視線先の看板の登録を解除"
/sdance setsign delete
```

```text title="アリーナを削除（ブース・看板の登録も解除。壁のブロックは残る）"
/sdance delarena <識別子>
```

```text title="設定と曲を再読み込み（試合中は不可）"
/sdance reload
```

!!! note "識別子（アリーナ名）について"
    識別子は英数字・`_`・`-` で16文字以内です（`.` は使えません）。アリーナが1つだけのときは、`join`・`start`・`stop` などで識別子を省略できます。

## 曲（.nbs）の追加

ステップダンスの曲は **Note Block Studio の `.nbs` ファイル** を使います。

```text title="曲を置く場所"
plugins/StepDance/songs/ に .nbs ファイルを置く
```

```text title="置いたあとに読み込む"
/sdance reload
```

- 曲 **ID** はファイル名から作られます（小文字化し、空白と `.` を `_` に置換）。`test-` で始まる名前は使えません。
- 譜面は `.nbs` の音から **自動生成** されます（同じ tick の音を代表音にまとめ、難易度の最小間隔で間引き、音の高さでレーン1〜4に振り分け、同じレーンの連続・詰まりを直す）。曲 ID をシードに使うので、同じ曲・同じ設定なら毎回同じ譜面になります。
- 音符の無い `.nbs`・読めないファイルは読み込み時にスキップされ、件数が通知されます。曲が1つも無いときは内蔵のテスト譜面で動作します。
- 読み込んだ曲は `/sdance songs` で一覧表示できます。

## 管理メニュー・管理ツール（コマンドを打たない設定）

コマンドを打たずに、**管理ツール（ブレイズロッド）** と **管理メニュー（GUI）** でも設定できます。どちらも `stepdance.admin` 権限が必要です。

```text title="管理ツール（ブレイズロッド）を受け取る"
/sdance wand
```

```text title="管理メニュー（GUI）を開く"
/sdance menu
```

- **管理メニュー** … 編集するアリーナの選択、初期スポーン／共通ロビー／アリーナ別ロビーの「立ち位置に設定」、ブースの追加・削除、参加／離脱／開始看板の登録・解除、曲を選んで開始（おまかせも可）・設定状況・リロード・壁の作り直し・強制停止、各数値（人数・時間・壁・ノーツの速さ・統合版補正）の増減、計測ログ・ping補正のON/OFF をまとめて操作できます。
- 数値ボタンは **＋／−** を別に置いており、変更は即保存され **次の曲から** 反映されます。壁の高さ・距離を変えると壁が自動で作り直されます。
- **強制停止・ブース削除・アリーナ削除** は、誤操作防止のため **5秒以内に2回押し** で確定します。
- 管理ツールを使い終えたら、管理メニューの「管理ツールを回収」で片付けられます。

## config.yml 設定項目

地点・ブース・看板のデータ（`default-spawn`・`lobby-spawn`・`arenas`・`signs`）はコマンドで登録すると自動保存されます（手書き不要）。以下は数値・表示などの設定項目です。数値の変更は **次の曲から** 反映されます。

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 1 | 開始に必要な人数（1で練習用の1人プレイ可。0人では開始しない） |
| `max-players` | 4 | 1曲に参加できる最大人数（ブース数とどちらか小さいほうが上限） |
| `lobby-max` | 12 | ロビーに入れる人数。超えた人は次の曲を待つ（遊んだ人は列の後ろへ） |
| `countdown-seconds` | 10 | 開始前のカウントダウン（秒） |
| `result-seconds` | 8 | 結果を表示してからロビーへ戻すまで（秒） |
| `count-in-beats` | 4 | 曲の前に鳴らすカウント音の回数（10tick間隔） |

### 難易度

| キー | 既定値 | 説明 |
|---|---|---|
| `default-difficulty` | NORMAL | 遊ぶ難易度（EASY / NORMAL / HARD） |
| `easy-min-interval` | 8 | EASY のノーツ最小間隔（tick。20tick = 1秒） |
| `normal-min-interval` | 4 | NORMAL のノーツ最小間隔（tick） |
| `hard-min-interval` | 2 | HARD のノーツ最小間隔（tick） |
| `same-lane-min-interval` | 4 | 同じレーンのノーツ最小間隔（tick）。ping が大きい参加者がいると自動で広げる |

### 壁（ノーツ表示）

| キー | 既定値 | 説明 |
|---|---|---|
| `wall-height` | 10 | 壁の行数（一番下の行が判定ライン） |
| `wall-distance` | 8 | 座席から壁までのブロック数 |
| `wall-bottom-offset` | 0 | 判定ラインの高さ（座席のブロックからの差） |
| `row-ticks` | 2 | ノーツが1行進む tick 数（小さいほど速い）。出現から判定まで wall-height × row-ticks tick |
| `lane-blocks` | 赤・青・黄緑・黄のコンクリート | レーン1〜4のノーツのブロック |
| `lane-target-blocks` | 赤・青・黄緑・黄の色付きガラス | 判定ラインの空きマス |
| `wall-background-block` | BLACK_CONCRETE | 壁の背景 |
| `judge-line-block` | SEA_LANTERN | 判定ラインの縁取り |
| `judge-flash.perfect/great/good/miss` | 金/ダイヤ/鉄/レッドストーン | 判定が出た瞬間に判定マスを光らせるブロック |
| `judge-flash-ticks` | 3 | 判定の光らせる長さ（tick） |

### 判定（tick単位。1tick = 50ms）

| キー | 既定値 | 説明 |
|---|---|---|
| `perfect-window` | 1 | PERFECT の判定幅（±tick） |
| `great-window` | 2 | GREAT の判定幅（±tick） |
| `good-window` | 3 | GOOD の判定幅（±tick）。これを過ぎて押さなければ MISS |
| `points.perfect` | 100 | PERFECT の点数 |
| `points.great` | 70 | GREAT の点数 |
| `points.good` | 40 | GOOD の点数 |
| `empty-press-penalty` | 20 | 空押し（範囲にノーツが無いのに押した）1回ごとの減点（0で無し） |
| `use-ping-compensation` | true | ping（往復）の分だけ入力を早めて判定する |
| `max-ping-compensation` | 6 | ping 補正の上限（tick） |
| `bedrock-offset` | 1 | 統合版で補正に足す tick（Geyser 経由の遅れ。実機で決める） |
| `bedrock-resend-slot` | true | 統合版は押した後に9番の選択を送り直す（戻らない場合の保険） |

### 音・環境・計測

| キー | 既定値 | 説明 |
|---|---|---|
| `volume` | 1.0 | BGM の音量（0.0〜1.0） |
| `keep-food` | true | 試合中は満腹度を減らさない |
| `debug` | false | true で判定のずれの平均・ばらつき・補正値を結果とコンソールに出す |
| `messages.prefix` | `&8[&dStepDance&8] &r` | メッセージの接頭辞 |

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/sdance setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/sdance setlobby [識別子]` | 共通ロビー（識別子を付けるとアリーナ別ロビー）を設定 |
| `/sdance addbooth <識別子>` | ブース（座席と壁）を追加。未登録ならアリーナを作成 |
| `/sdance delbooth <識別子> <番号>` | ブースを削除（番号は 1〜4） |
| `/sdance delarena <識別子>` | アリーナを削除（ブース・看板の登録も解除／壁は残る） |
| `/sdance rebuild [識別子]` | 壁を作り直す（識別子省略で全アリーナ） |
| `/sdance setsign <join <識別子>\|leave\|start <識別子>\|delete>` | 看板を登録／解除 |
| `/sdance stop [識別子]` | 試合（カウントダウン／演奏）を強制停止 |
| `/sdance reload` | 設定と曲を再読み込み（試合中は不可） |
| `/sdance lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/sdance wand` | 管理ツール（ブレイズロッド）を受け取る |
| `/sdance menu` | 管理メニュー（GUI）を開く |
| `/sdance status` | 設定状況・現在の状況を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/sdance join [識別子]` | ロビーに参加（参加看板と同等） |
| `/sdance leave` | ロビーから離脱（離脱看板と同等） |
| `/sdance start [識別子] [曲ID]` | 曲を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/sdance status` | 設定・状況を確認（読み取り専用） |
| `/sdance songs` | 入っている曲の一覧 |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `stepdance.admin` | OP | セットアップ（`setstartspawn`・`setlobby`・`addbooth`・`delbooth`・`delarena`・`rebuild`・`setsign`）・`wand`・`menu`・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/sdance join`・`/sdance leave`・`/sdance start`・`/sdance status`・`/sdance songs` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "曲が開始できない"
    `/sdance status` を確認してください。アリーナにブースがあり、ロビーに最低開始人数（既定1人）以上いる必要があります。曲が進行中のアリーナは終了を待つか `/sdance stop` で停止してください。

??? failure "壁の形を変えたのに反映されない"
    壁の高さ・距離を変えたら、試合をしていない間に `/sdance rebuild`（または管理メニューの「壁を作り直す」）を実行してください。試合中のアリーナは飛ばされるので、終了後に実行します。

??? failure "曲が一覧に出ない"
    `.nbs` を `plugins/StepDance/songs/` に置いて `/sdance reload` しましたか。ファイル名が `test-` で始まる・音符が無い・読めない形式のファイルはスキップされます。読めなかった件数はリロード時に通知されます。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/sdance lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/StepDance/lobby-inventory.yml` に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

??? failure "統合版の入力がうまく判定されない"
    統合版は Geyser 経由で入力が少し遅れます。`bedrock-offset`（既定1tick）を実機で調整してください。`debug` を true にすると判定のずれの平均・補正値が結果とコンソールに出るので、値決めの参考になります。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ステップダンス】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。演奏中のサイドバー（スコア表示）にも反映されます。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。曲の結果（順位）も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます（「他のミニゲーム ○○ に参加中です」）。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品を `plugins/StepDance/lobby-inventory.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない・ロックが残った場合はOPが `/sdance lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は旧ファイルを `config.yml.bak-日時` に退避）。地点・ブース・看板などのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも管理ツールを使え、登録看板は蝋引きして編集画面が開かないようにします。入力の遅れは `bedrock-offset` で補正し、押した後は9番の選択を送り直します（`bedrock-resend-slot`）。

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
| `name-tag.display-name` | ステップダンス | 「【】」に入れる正式名 |
| `name-tag.color` | GOLD | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも管理ツールを使える |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ステップダンス（StepDance） 概要へ](index.md){ .md-button }
