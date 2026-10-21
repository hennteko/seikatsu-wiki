<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 椅子取りゲーム（MusicChair） ― OP・運営ガイド { .page-op #musicchair-op }

椅子取りゲーム（MusicChair）の導入・曲の追加・ステージ設定・看板・config・権限・管理コマンドをまとめます。地点・椅子・看板・ステージはコマンドか管理メニューで設定すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | MusicChair |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/musicchair`（別名 `/mchair`） |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/MusicChair/config.yml` |
| メッセージ | `plugins/MusicChair/messages.yml` |
| 曲フォルダ | `plugins/MusicChair/songs/`（`.nbs` を置く） |
| 権限ノード | `musicchair.admin`（既定OP） |

## 導入手順

1. ビルドした `MusicChair` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml`・`messages.yml`・`songs/` フォルダが自動生成される。
3. `plugins/MusicChair/songs/` に **`.nbs`（Note Block Studio）ファイル** を1つ以上入れ、`/mchair reload` で読み込む。
4. `/mchair create <識別子>` でステージを作る。
5. `/mchair setspawn <識別子>` でゲーム開始位置を、`/mchair addchair <識別子>` で椅子を人数分（最大参加者 − 1脚以上）登録する。
6. `/mchair setstartspawn`・`/mchair setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
7. 参加・離脱・開始の看板を設置する。
8. `/mchair status` で開始可能か確認する。

!!! note "試合に必須の設定"
    1つのステージを開始可能にするには、**ゲーム開始位置（`setspawn`）と椅子（1脚以上）** が必要です。さらに開始時には **参加者が `min-players` 以上** で、**椅子の数が「参加者 − 1」以上** 登録されている必要があります。曲（`.nbs`）が1つも無い場合はメトロノームで代用されますが、必ず1曲以上入れてください。フィールド範囲（`setfield`）は任意です。

!!! tip "曲（.nbs）の追加"
    曲は `plugins/MusicChair/songs/` に `.nbs` ファイルを置くだけで増やせます。ファイル名（拡張子を除く）が曲の識別子になり、ファイル内のタイトル・作者が表示に使われます。追加・削除したら `/mchair reload` または管理メニューの「設定・曲を再読み込み」で反映します。読み込めなかったファイルはスキップされ、起動ログに警告が出ます。

## セットアップ手順（コマンド）

地点・椅子・フィールドは **実行した位置／視線** を使い、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `musicchair.admin` 権限が必要です。マップ系の設定は、ステージの識別子を引数で指定します（ステージが1つだけなら省略可）。

```text title="ステージを作成する"
/mchair create <識別子>
```

```text title="ゲーム開始位置を設定（その場に立って実行・椅子の円の外側に）"
/mchair setspawn <識別子>
```

```text title="椅子を登録（視線先5ブロック以内のマス／無ければ足元・向いた方向が座る向き）"
/mchair addchair <識別子>
```

```text title="椅子の登録を解除（視線先または足元の椅子）"
/mchair removechair <識別子>
```

```text title="登録済みの椅子を一覧表示"
/mchair chairs <識別子>
```

```text title="初期スポーン（途中抜け・離脱時の戻り先／全ステージ共通・その場に立って実行）"
/mchair setstartspawn
```

```text title="受付ロビー（全ステージ共通・その場に立って実行）"
/mchair setlobby
```

```text title="フィールド範囲 角1（任意・その場に立って実行）"
/mchair setfield 1 <識別子>
```

```text title="フィールド範囲 角2（任意・その場に立って実行）"
/mchair setfield 2 <識別子>
```

```text title="参加看板を登録（看板を見て実行）"
/mchair setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/mchair setsign leave
```

```text title="開始看板を登録（識別子指定・看板を見て実行）"
/mchair setsign start <識別子>
```

```text title="視線先の看板の登録を解除"
/mchair setsign delete
```

```text title="曲（.nbs）の一覧をチャットに表示"
/mchair songs
```

```text title="設定・曲を再読み込み（試合中は不可）"
/mchair reload
```

!!! note "椅子とフィールドについて"
    椅子は、視線先（5ブロック以内）に階段・ハーフブロック・カーペットなどがあればそのマス、普通のブロックなら見ている面の隣のマス、何も見ていなければ足元のマスに登録されます。向いている方向が座る向きになります。椅子は毎ラウンド「生存者 − 1脚」だけランダムに選ばれて使われるので、**想定する最大人数 − 1脚** 以上を広めに配置してください。フィールド範囲（`setfield`）を設定すると、範囲外に出た生存者は開始位置へ戻されます（任意）。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール（ブレイズロッド）** と **管理メニュー（GUI）** でも地点・椅子・看板・演出の切り替えができます。どちらも `musicchair.admin` 権限が必要です。

```text title="管理ツール（ブレイズロッド）を受け取る"
/mchair wand
```

```text title="管理メニュー（GUI）を開く"
/mchair menu
```

- **管理ツール** … ブレイズロッドです。**右クリック／タップで管理メニューが開きます**（`/mchair menu` と同じ）。捨てると消え、`/mchair wand` で取り直せます。
- **管理メニュー** … 編集するステージの切り替え、初期スポーン／ロビー／開始位置／フィールド角の「現在地を設定」、椅子の登録・解除・一覧・位置表示、参加／離脱／開始／解除の看板登録、フェイクストップ・加速のON/OFF切り替え、次の曲の固定・曲の一覧、試合の開始・強制停止（2回押し）・ステージ削除（2回押し）・設定状況の表示・設定/曲の再読み込みを、GUIからまとめて操作できます。
- **椅子の位置を表示**（スパイグラス）… 椅子・向き・開始位置・フィールドの角をパーティクルで表示して確認できます。

!!! note "管理ツールの注意"
    管理ツールはOP専用で、受け取り時・使用時の両方で `musicchair.admin` を確認します（非OPが入手しても使えません）。椅子取りゲームに参加中は受け取れないので、先に `/mchair leave` で離脱してから実行してください。不要になったら管理メニューの「管理ツールを回収」で片付けられます。

## config.yml 設定項目

コマンドや管理メニューで登録する地点・椅子・看板・ステージのデータ（`default-spawn`・`lobby-spawn`・各 `*-sign`・`stages`）は自動保存されます。手書き不要です。**設定の変更は「次の試合から」反映されます。** 以下は数値・演出などの設定項目です。

### 人数

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 3 | 開始に必要な人数（下限2） |
| `max-players` | 16 | 最大参加人数（上限50） |

### 時間（秒）

| キー | 既定値 | 説明 |
|---|---|---|
| `countdown-seconds` | 10 | 開始前のカウントダウン |
| `music-min-seconds` | 8 | 曲が止まるまでの最短時間 |
| `music-max-seconds` | 20 | 曲が止まるまでの最長時間 |
| `sit-time-limit-seconds` | 5 | 曲が止まってから座るまでの制限時間 |
| `round-interval-seconds` | 3 | 次のラウンドまでの間隔 |
| `result-seconds` | 5 | 優勝発表のあと、ロビーへ戻るまでの秒数 |

### 椅子

| キー | 既定値 | 説明 |
|---|---|---|
| `waiting-block` | WHITE_CARPET | 曲の間、椅子の位置に置く目印ブロック |
| `chair-block` | OAK_STAIRS | 曲が止まったときに置く椅子ブロック（階段系） |
| `seat-y-offset` | 0.3 | 座ったときの高さ調整（床からのずれ。見た目が浮く・沈むときに変更） |

### 椅子の近くで待つ対策

| キー | 既定値 | 説明 |
|---|---|---|
| `push-strength` | 0.8 | 弾き飛ばす強さ |
| `inner-zone-margin` | 1.5 | 立入禁止エリアの余白（ブロック）。椅子の中心から一番遠い椅子までの距離＋この値の円の内側に入ると弾かれる |
| `idle-warn-seconds` | 3 | 曲の間に立ち止まっていると警告するまでの秒数 |
| `idle-push-seconds` | 5 | 曲の間に立ち止まっていると弾き飛ばすまでの秒数（0で無効） |

### 音楽

| キー | 既定値 | 説明 |
|---|---|---|
| `volume` | 1.0 | 曲の音量（0.1〜2.0） |
| `sound-category` | RECORDS | 曲の音の種類（RECORDS＝ジュークボックス/音符ブロック、MASTER＝全体）。プレイヤーの音量設定のどれに従うか |

### 演出

| キー | 既定値 | 説明 |
|---|---|---|
| `fake-stop.enabled` | true | フェイクストップ（曲が一瞬止まってまた流れる・椅子は出ない） |
| `fake-stop.chance` | 0.1 | 1ラウンドでフェイクが起きる確率（0〜1） |
| `fake-stop.ticks` | 10 | 止まる長さ（20 = 1秒。4〜40に制限） |
| `speedup.enabled` | true | ラウンドが進むほど曲を速く・短くする |
| `speedup.per-round` | 0.05 | 1ラウンドごとのテンポの上がり幅（0.05 = 5%ずつ速く） |
| `speedup.max` | 1.5 | テンポの上限（1.5 = 1.5倍速まで） |
| `speedup.music-time-shrink-per-round` | 1 | 1ラウンドごとに「曲が止まるまでの最長時間」を何秒短くするか（最短時間は下回らない） |
| `bossbar` | true | 曲が止まったら、座るまでの残り時間をボスバーで表示 |
| `first-sit-message` | true | 最初に座った人に「一番乗り！」と表示 |
| `show-song-title` | true | ラウンド開始時に曲名と作者をアクションバーに表示 |

### 環境

| キー | 既定値 | 説明 |
|---|---|---|
| `keep-food` | true | 在籍中は満腹度を減らさない |
| `debug` | false | デバッグログを出す |

### 報酬（`rewards`）

ロビーを出た後に **コンソールから** 実行されるコマンドのリストです。`%player%` はプレイヤー名に置き換わります。既定は空（報酬なし）。

| キー | 既定値 | 説明 |
|---|---|---|
| `rewards.win-commands` | `[]` | 優勝者に実行するコマンド |
| `rewards.participation-commands` | `[]` | 参加者全員（優勝者を含む）に実行するコマンド |

!!! note "報酬の渡し方"
    報酬は試合終了の瞬間ではなく、プレイヤーが **ロビーを出て持ち物が元に戻った後** に渡されます（保留された報酬があれば、離脱・再ログイン時に安全なタイミングで実行されます）。アイテム報酬でも持ち物の退避と競合しません。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/mchair create <識別子>` | ステージを作成 |
| `/mchair delete <識別子>` | ステージを削除（確認のため10秒以内に2回実行。椅子・開始看板の登録も消える） |
| `/mchair setspawn [識別子]` | ゲーム開始位置を設定 |
| `/mchair addchair [識別子]` | 椅子を登録（視線先／足元） |
| `/mchair removechair [識別子]` | 椅子の登録を解除 |
| `/mchair chairs [識別子]` | 登録済みの椅子を一覧表示 |
| `/mchair setstartspawn` | 初期スポーン（途中抜けの戻り先・全ステージ共通）を設定 |
| `/mchair setlobby` | ロビー地点（全ステージ共通）を設定 |
| `/mchair setfield <1\|2> [識別子]` | フィールド範囲の角を設定（任意・2点） |
| `/mchair setsign <join\|leave\|start <識別子>\|delete>` | 看板を登録／解除 |
| `/mchair songs` | 曲（.nbs）の一覧を表示 |
| `/mchair wand` | 管理ツール（ブレイズロッド）を受け取る |
| `/mchair menu` | 管理メニュー（GUI）を開く |
| `/mchair stop [識別子]` | 試合を強制停止（勝敗をつけずにロビーへ戻す） |
| `/mchair reload` | 設定・曲を再読み込み（試合中は不可） |
| `/mchair lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |

!!! note "識別子（ステージ）について"
    ステージの識別子は、登録済みステージが **1つだけなら省略可**（自動でそれが選ばれます）。複数ある場合は指定が必要です。`/mchair setsign start` だけは識別子を省略できません。

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/mchair join` | ロビーに参加（参加看板と同等） |
| `/mchair leave` | ロビーから離脱（離脱看板と同等。試合中は脱落扱い） |
| `/mchair start [識別子]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/mchair status` | 設定・状況を確認（読み取り専用） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `musicchair.admin` | OP | ステージの作成・削除、`setspawn`・`addchair`・`removechair`・`chairs`・`setstartspawn`・`setlobby`・`setfield`・`setsign`・`songs`・`wand`・`menu`・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/mchair join`・`/mchair leave`・`/mchair start`・`/mchair status` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/mchair status` を確認してください。対象ステージに **開始位置（`setspawn`）と椅子** が設定され、ロビーに **`min-players` 以上** の人がいて、**椅子の数が「参加者 − 1」以上** 必要です。椅子のワールドが読み込まれていない場合も開始できません。

??? failure "曲が流れない／メトロノームになる"
    `plugins/MusicChair/songs/` に `.nbs` ファイルが無いか、読み込みに失敗しています。ファイルを置いて `/mchair reload` を実行し、起動/再読み込みログに「曲を N 曲読み込みました」と出るか確認してください。読み込めないファイルはスキップされ警告が出ます。

??? failure "ステージの設定を変更できない"
    そのステージが試合で使用中（進行中・カウントダウン中）だと変更・削除できません。試合の終了を待つか `/mchair stop` で停止してください。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/mchair lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/MusicChair/lobby-inventory.yml` に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【椅子取りゲーム】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。試合結果も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます（「他のミニゲーム ○○ に参加中です」）。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品を `plugins/MusicChair/lobby-inventory.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない・ロックが残った場合はOPが `/mchair lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は旧ファイルを `config.yml.bak-日時` に退避）。地点・椅子・看板・ステージなどのデータ領域は補完対象外です。読み込めない config は `config.yml.broken.bak` に退避して初期値で起動します。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも管理ツールを使え、登録看板は蝋引きして編集画面が開かないようにします。
- **観戦（GhostSpectator）** … 脱落したプレイヤーは生存者の視点を観戦します（スニークで対象切り替え）。統合版は観戦モードが扱いづらいため、方式を自動で切り替えます（`bedrock.spectator-style`）。

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
| `name-tag.display-name` | 椅子取りゲーム | 「【】」に入れる正式名 |
| `name-tag.color` | AQUA | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも管理ツールを使える |
| `bedrock.spectator-style` | AUTO | 観戦方式（AUTO＝統合版だけゴースト観戦／GHOST＝全員ゴースト／VANILLA＝全員観戦モード） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 椅子取りゲーム（MusicChair） 概要へ](index.md){ .md-button }
