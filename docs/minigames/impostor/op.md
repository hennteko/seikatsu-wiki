<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# アモアス ― OP・運営ガイド { .page-op #impostor-op }

アモアス（Impostor）の導入・マップ設定・看板・config・権限・管理コマンドをまとめます。地点・看板・マップはコマンド（または管理メニュー）で登録すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Impostor |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/imp`（別名 `/impostor`） |
| api-version | 26.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/Impostor/config.yml` |
| タスク定義 | `plugins/Impostor/tasks.yml` |
| メッセージ | `plugins/Impostor/messages.yml` |
| 権限ノード | `impostor.admin`（既定OP） |

## 導入手順

1. ビルドした `Impostor` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml`・`tasks.yml`・`messages.yml` が自動生成される。
3. `/imp setstartspawn`・`/imp setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/imp createmap <マップ>` でマップを作り、`setfield`・`setspawn`・`setseat`・`seteject`・`setbutton`・各種部屋/タスク/ベント/ドア/修理地点を設定する。
5. 参加・離脱・開始の看板を設置する。
6. `/imp check <マップ>` で設定の不足を確認し、`/imp status` で全体を確認する。

!!! note "マップを開始可能にするには"
    開始に最低限 **必要なのは開始地点（`setspawn`）** です。ただし実際に遊ぶには、**会議席・追放先・緊急ボタン・タスク地点** も設定しておくことを強く推奨します（タスク地点が足りないタスクは配られません）。サボタージュを使うには修理地点（`setfix lights` / `setfix reactor`）が、ドア封鎖には部屋とドア地点が必要です。`/imp check <マップ>` で不足を確認できます。

## セットアップ手順（コマンド）

地点は **実行した位置**、範囲は2点（対角）で指定し、看板系は看板を **見ながら** 実行します。マップ系はすべて対象マップ名を引数に取り、`impostor.admin` 権限が必要です。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/imp setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/imp setlobby
```

```text title="マップを作成"
/imp createmap <マップ>
```

```text title="マップを削除"
/imp deletemap <マップ>
```

```text title="試合範囲 角1・角2（任意・その場に立って実行）"
/imp setfield <マップ> <1|2>
```

```text title="開始地点を追加（現在地。複数登録して散らばらせる）"
/imp setspawn <マップ>
```

```text title="会議の席を追加（現在地。最大15）"
/imp setseat <マップ>
```

```text title="追放演出の表示先を設定（現在地）"
/imp seteject <マップ>
```

```text title="緊急ボタンを設定（ボタンを見て実行）"
/imp setbutton <マップ>
```

```text title="部屋の範囲を設定（部屋名・角1/角2）"
/imp setroom <マップ> <部屋名> <1|2>
```

```text title="タスク地点を追加（タスクID。手順の数だけ順に登録）"
/imp settask <マップ> <タスクID>
```

```text title="ベント（通気口）を追加（同じグループどうしがつながる）"
/imp setvent <マップ> <グループ>
```

```text title="ドア地点を追加（部屋名。ドア封鎖で閉じる位置）"
/imp setdoor <マップ> <部屋名>
```

```text title="サボタージュの修理地点を追加（lights=停電 / reactor=原子炉。reactorは2地点）"
/imp setfix <マップ> <lights|reactor>
```

```text title="視線先の登録地点を1つ削除"
/imp delpoint <マップ>
```

```text title="開始地点を全消去"
/imp clearspawns <マップ>
```

```text title="会議席を全消去"
/imp clearseats <マップ>
```

```text title="マップの設定不足を確認"
/imp check <マップ>
```

```text title="参加看板を登録（看板を見て実行）"
/imp setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/imp setsign leave
```

```text title="開始看板を登録（マップ指定・看板を見て実行）"
/imp setsign start <マップ>
```

```text title="視線先の看板の登録を解除"
/imp setsign delete
```

!!! note "タスク地点の手順"
    タスクは `tasks.yml` で手順（steps）が定義されています。`settask <マップ> <タスクID>` を手順の数だけ繰り返すと、1番目・2番目…の順に地点が登録されます。地点が手順数より少ないマップでは、そのタスクは配られません。修理地点の `reactor` は **2地点** 登録が必要です。

## 管理ツール・管理メニュー（コマンドを打たない設定）

```text title="管理ツール（ワンド）を受け取る"
/imp wand
```

```text title="管理メニュー（GUI）を開く"
/imp menu
```

管理メニューからは、数値設定（人数・役職・キル・会議・タスク数など）の増減、匿名投票・追放者の役職発表・タスクバー表示の切り替え、地点・看板の登録などをまとめて操作できます。

## config.yml 設定項目

コマンドで登録する地点・看板のデータ（`locations`・`signs`）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。変更は `/imp reload` で反映できます（試合中の変更は次の試合から）。

### 人数・進行

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 4 | 開始に必要な人数（4〜15） |
| `max-players` | 15 | 参加できる最大人数（4〜15） |
| `start-countdown` | 10 | 開始操作から試合開始までの秒数 |
| `end-delay` | 5 | 決着から後片付けまでの秒数 |

### 役職

| キー | 既定値 | 説明 |
|---|---|---|
| `impostor-count` | AUTO | インポスターの数。AUTO＝〜7人で1人・8〜11人で2人・12人〜で3人。数字で固定も可（参加者の半分未満に自動調整） |

### タスク

| キー | 既定値 | 説明 |
|---|---|---|
| `tasks-common` | 1 | 1人あたりの共通タスク数 |
| `tasks-short` | 3 | 1人あたりの短いタスク数 |
| `tasks-long` | 1 | 1人あたりの長いタスク数 |
| `task-range` | 2.5 | 待機型タスクで動ける範囲（マス） |
| `taskbar-update` | ALWAYS | タスクバーの更新（ALWAYS=随時 / MEETINGS=会議のときだけ / NEVER=表示しない） |

### キル・通報

| キー | 既定値 | 説明 |
|---|---|---|
| `kill-cooldown` | 25 | キルのクールタイム（秒） |
| `kill-range` | 3.0 | キルできる距離（マス） |
| `report-range` | 3.0 | 通報できる距離（マス） |
| `impostor-night-vision` | true | インポスターに暗視を付ける（停電中も見える） |

### サボタージュ

| キー | 既定値 | 説明 |
|---|---|---|
| `sabotage-cooldown` | 30 | サボタージュのクールタイム（秒） |
| `critical-seconds` | 45 | 原子炉などの制限時間（秒） |
| `door-seconds` | 10 | ドア封鎖の時間（秒） |
| `door-material` | IRON_BLOCK | ドア封鎖でドア位置に置くブロック |
| `blackout-effect-bedrock` | DARKNESS | 統合版の停電の効果（DARKNESS / BLINDNESS） |

### 会議・投票

| キー | 既定値 | 説明 |
|---|---|---|
| `emergency-per-player` | 1 | 1人が緊急ボタンを押せる回数 |
| `emergency-cooldown` | 15 | 試合開始・前回の会議から緊急ボタンが押せるまでの秒数 |
| `discussion-seconds` | 30 | 話し合いの時間（秒） |
| `voting-seconds` | 60 | 投票の時間（秒） |
| `anonymous-votes` | false | true で誰が誰に入れたかを隠す |
| `confirm-ejects` | true | true で追放された人の役職を発表する |

### チャット

| キー | 既定値 | 説明 |
|---|---|---|
| `chat-only-in-meeting` | true | 会議中だけ参加者のチャットを全体に出す |
| `blocked-commands` | msg, tell ほか | 試合中の参加者が使えないコマンド |

### 色（`colors`）

最大15色。各色は `id`（内部ID）・`name`（表示名）・`rgb`（革防具の色）・`wool`（投票GUIの羊毛）を持ちます。既定は赤・青・緑・ピンク・オレンジ・黄・黒・白・紫・茶・水色・黄緑・灰・マゼンタ・青緑の15色です。

### その他

| キー | 既定値 | 説明 |
|---|---|---|
| `debug` | false | デバッグログを出す |

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/imp setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/imp setlobby` | ロビー地点を設定 |
| `/imp createmap <マップ>` / `/imp deletemap <マップ>` | マップの作成／削除 |
| `/imp setfield <マップ> <1\|2>` | 試合範囲の角を設定（任意） |
| `/imp setspawn <マップ>` | 開始地点を追加 |
| `/imp setseat <マップ>` | 会議席を追加（最大15） |
| `/imp seteject <マップ>` | 追放演出の表示先を設定 |
| `/imp setbutton <マップ>` | 緊急ボタンを設定 |
| `/imp setroom <マップ> <部屋名> <1\|2>` | 部屋の範囲を設定 |
| `/imp settask <マップ> <タスクID>` | タスク地点を追加（手順の数だけ順に） |
| `/imp setvent <マップ> <グループ>` | ベント（通気口）を追加 |
| `/imp setdoor <マップ> <部屋名>` | ドア地点を追加 |
| `/imp setfix <マップ> <lights\|reactor>` | サボタージュの修理地点を追加 |
| `/imp delpoint <マップ>` | 視線先の登録地点を1つ削除 |
| `/imp clearspawns <マップ>` / `/imp clearseats <マップ>` | 開始地点／会議席を全消去 |
| `/imp check <マップ>` | 設定不足を確認 |
| `/imp maps` | 登録マップ一覧 |
| `/imp setsign <join\|leave\|start <マップ>\|delete>` | 看板を登録／解除 |
| `/imp wand` / `/imp menu` | 管理ツール／管理メニュー |
| `/imp stop` | 試合を強制停止 |
| `/imp reload` | 設定を再読み込み |
| `/imp lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/imp join` | ロビーに参加（参加看板と同等） |
| `/imp leave` | ロビーから離脱（離脱看板と同等。試合中は脱落扱い） |
| `/imp start [マップ]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/imp status` | 設定・状況を確認（読み取り専用） |
| `/imp vote [色\|skip]` | 会議中に投票（引数なしで投票画面） |
| `/imp stats [プレイヤー]` | 記録を確認 |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `impostor.admin` | OP | マップ・地点・看板の各 set/create/delete 系・`check`・`maps`・`wand`・`menu`・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/imp join`・`/imp leave`・`/imp start`・`/imp status`・`/imp vote`・`/imp stats` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/imp check <マップ>`・`/imp status` を確認してください。開始地点が登録済みで、ロビーに最低人数（既定4人）以上いる必要があります。会議席・追放先・緊急ボタン・タスク地点も実プレイには必要です。

??? failure "タスクが配られない／サボタージュが使えない"
    タスクは地点が手順数ぶん登録されていないと配られません（`settask` を手順の数だけ）。停電は `setfix lights`、原子炉は `setfix reactor` を2地点、ドア封鎖は部屋とドア地点が必要です。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/imp lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/Impostor/` 以下に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【インポスター】名前」になり、離脱・ログアウト・プラグイン停止で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない場合はOPが `/imp lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値付きで自動追記します（既存の値は変更しません）。地点・看板などのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でもアイテムを使え、登録看板は蝋引きして編集画面が開かないようにします。停電の暗転は統合版向けに `blackout-effect-bedrock` で効果を選べます。

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
| `name-tag.display-name` | インポスター | 「【】」に入れる正式名 |
| `name-tag.color` | GOLD | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でもアイテムを使える |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← アモアス 概要へ](index.md){ .md-button }
