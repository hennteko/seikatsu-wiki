<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# どうぶつ積みバトル ― OP・運営ガイド { .page-op #tsumibattle-op }

どうぶつ積みバトル（TsumiBattle）の導入・アリーナ設定・看板・ピース定義・config・権限・管理コマンドをまとめます。地点・看板・アリーナはコマンドまたは管理メニューで登録すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | TsumiBattle |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/tsumi` |
| api-version | 26.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/TsumiBattle/config.yml`・`messages.yml`・`pieces.yml` |
| 記録ファイル | `plugins/TsumiBattle/`（勝敗・ベスト記録） |
| 権限ノード | `tsumibattle.admin`（既定OP） |

## 導入手順

1. ビルドした `TsumiBattle` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml`・`messages.yml`・`pieces.yml` が自動生成される（人数・時間などは初期値で動作）。
3. `/tsumi setstartspawn`・`/tsumi setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/tsumi create <アリーナ>` でアリーナを作り、**土台の中心に立って見せたい向きを向いて** `/tsumi setorigin <アリーナ>` を実行する。必要に応じて幅・土台幅・高さ上限・観戦席を調整する。
5. `/tsumi preview <アリーナ>` でプレイ面・土台・落下判定ライン・観戦席の位置を確認し、プレイ面と土台の下を空気にする。
6. 参加・離脱・開始・ランキングの看板を設置する。
7. `/tsumi status` で設定状況（開始可能かどうか）を確認する。

!!! note "アリーナに必須の設定"
    1つのアリーナを開始可能にするには、少なくとも **土台の中心（原点）** が必要です。原点を基準に、幅・土台幅・高さ上限（すべて初期値あり）でプレイ空間が決まります。**プレイ面（幅×高さ上限＋余白・奥行き3マス）と土台の下 `kill-depth`＋1 マスは空気** にしてください。ブロックがあると開始できません（`/tsumi preview` で空き状況を確認できます）。

## セットアップ手順（コマンド）

地点・原点・観戦席は **実行した位置** を使い、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `tsumibattle.admin` 権限が必要です。アリーナ名を省略すると、登録が1つのときはそれが対象になります。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/tsumi setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/tsumi setlobby
```

```text title="アリーナを作成"
/tsumi create <アリーナ>
```

```text title="土台の中心（原点）を設定（その場に立って、見せたい向きを向いて実行）"
/tsumi setorigin [アリーナ]
```

```text title="観戦席を設定（その場に立って実行／未設定なら原点から view-distance マス手前が自動で使われる）"
/tsumi setview [アリーナ]
```

```text title="プレイ面の幅を変更（マス）"
/tsumi setwidth [アリーナ] <マス>
```

```text title="土台の幅を変更（マス）"
/tsumi setbase [アリーナ] <マス>
```

```text title="高さの上限を変更（マス）"
/tsumi setmaxheight [アリーナ] <マス>
```

```text title="プレイ面・土台・落下判定ライン・観戦席をパーティクルで10秒表示"
/tsumi preview [アリーナ]
```

```text title="ピースの形を目の前に10秒表示して確認"
/tsumi piece <ピース名>
```

```text title="アリーナを削除（開始看板の登録も解除）"
/tsumi remove <アリーナ>
```

```text title="参加看板を登録（看板を見て実行）"
/tsumi setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/tsumi setsign leave
```

```text title="開始看板を登録（アリーナ指定・看板を見て実行）"
/tsumi setsign start <アリーナ>
```

```text title="ランキング看板を登録（順位指定・看板を見て実行）"
/tsumi setsign rank <順位>
```

```text title="視線先の看板の登録を解除"
/tsumi setsign delete
```

!!! note "幅・土台・高さの関係"
    プレイ面の幅（`setwidth`）はピースが動ける横幅、土台の幅（`setbase`）は中央に置かれる足場の幅で、土台はプレイ面より狭くします。高さの上限（`setmaxheight`）に届くと引き分けです。アリーナが試合で使用中のときは変更・削除できません。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール（ブレイズロッド相当のツール）** と **管理メニュー（GUI）** でも地点・アリーナ・看板を設定できます。どちらも `tsumibattle.admin` 権限が必要です。

```text title="管理ツールを受け取る"
/tsumi wand
```

```text title="管理メニュー（GUI）を開く"
/tsumi menu
```

- **管理メニュー** … 初期スポーン・ロビーの設定、アリーナの作成・選択・原点/観戦席の設定、幅・土台・高さの調整、プレビュー、看板モード、強制停止・設定状況の表示などを GUI からまとめて操作できます。
- 管理ツールは参加中は受け取れません。先に `/tsumi leave` で離脱してから実行してください。

## 運用コマンド

```text title="1人テスト（記録を残さず1人で試す／ロビー参加後に実行）"
/tsumi test [アリーナ]
```

```text title="試合を強制停止"
/tsumi stop [アリーナ]
```

```text title="config.yml / messages.yml / pieces.yml を再読み込み（試合中は不可）"
/tsumi reload
```

```text title="ロビー前の持ち物を手動復旧（force で退避データ破棄・ロック解除）"
/tsumi lobbyfix <プレイヤー> [force]
```

## ピースの定義（pieces.yml）

ピースは `plugins/TsumiBattle/pieces.yml` で定義します。1つのピースは次の項目を持ちます。

| 項目 | 説明 |
|---|---|
| `name` | 表示名（例: カメ） |
| `weight` | 出やすさ（大きいほど出やすい。0で出ない） |
| `depth` | 奥行き（1〜3。見た目だけで、崩れやすさには影響しない） |
| `shape` | 1文字＝1ブロックの形。「.」は空白。上の行がピースの上側 |
| `blocks` | 文字ごとのブロック（重力で落ちない固体ブロックのみ。砂・パウダー・松明・看板は不可） |

!!! note "ピース定義のルール"
    すべてのマスが上下左右でつながっていること、最大 8×8（config の `max-piece-size`）であることが必要です。既定のピースは更新時に「まだ無いもの」だけ自動追加されます。既定のピースを消すときは `pieces` から消したうえで `removed-pieces` に名前を書いてください（書かないと次の起動でまた追加されます）。`/tsumi piece <ピース名>` で形を目の前に表示して確認できます。

## config.yml 設定項目

コマンドで登録する地点・看板・アリーナのデータ（`lobby-spawn`・`default-spawn`・各 `*-sign`・`rank-signs`・`arenas`）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。以下は数値などの設定項目です。

### 人数・進行

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 2 | 開始に必要な人数（2〜4） |
| `max-players` | 4 | 最大人数（ロビーの定員も兼ねる。2〜4） |
| `start-countdown` | 5 | 開始操作から試合開始までの秒数 |
| `turn-seconds` | 20 | 1手の持ち時間（秒。0で無制限） |
| `fall-ticks-per-block` | 2 | 落下の速さ（1マス進むのにかかる tick。小さいほど速い） |
| `spawn-gap` | 4 | タワーの一番上からピースが出現するまでの高さ（マス） |
| `kill-depth` | 6 | 土台からこれだけ下に落ちたら「落ちた」扱い（マス） |
| `stability-margin` | 0.0 | 安定判定の余裕（マス。大きいほど崩れやすい。0.0〜0.5） |
| `max-repeat` | 2 | 同じピースが続いてよい回数 |
| `max-piece-size` | 8 | ピースの最大の縦横（マス） |
| `collapse-show-seconds` | 2 | 崩れてから結果発表までの秒数 |
| `end-show-seconds` | 5 | 結果発表からロビーへ戻すまでの秒数 |

### 新しいアリーナの初期値

| キー | 既定値 | 説明 |
|---|---|---|
| `default-width` | 21 | 新しいアリーナのプレイ面の幅（マス） |
| `default-base-width` | 9 | 新しいアリーナの土台の幅（マス） |
| `default-max-height` | 60 | 新しいアリーナの高さの上限（マス） |
| `view-distance` | 8 | 観戦席からプレイ面までの距離（`setview` を使っていないアリーナで使う） |
| `base-block` | SMOOTH_STONE | 土台のブロック |

### 記録・その他

| キー | 既定値 | 説明 |
|---|---|---|
| `ranking-size` | 10 | `/tsumi top` とランキング看板の件数 |
| `debug` | false | 安定判定の計算結果（重心・支えの範囲）をログに出す |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `tsumibattle.admin` | OP | `wand`・`menu`・`setstartspawn`・`setlobby`・`create`・`remove`・`setorigin`・`setview`・`setwidth`・`setbase`・`setmaxheight`・`preview`・`piece`・`setsign`・`test`・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/tsumi join`・`/tsumi leave`・`/tsumi start`・`/tsumi status`・`/tsumi stats`・`/tsumi top` は権限チェックがなく、全プレイヤーが使えます。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/tsumi setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/tsumi setlobby` | ロビー地点を設定 |
| `/tsumi create <アリーナ>` | アリーナを作成 |
| `/tsumi remove <アリーナ>` | アリーナを削除（開始看板の登録も解除） |
| `/tsumi setorigin [アリーナ]` | 土台の中心（原点）と向きを設定 |
| `/tsumi setview [アリーナ]` | 観戦席を設定 |
| `/tsumi setwidth [アリーナ] <マス>` | プレイ面の幅を変更 |
| `/tsumi setbase [アリーナ] <マス>` | 土台の幅を変更 |
| `/tsumi setmaxheight [アリーナ] <マス>` | 高さの上限を変更 |
| `/tsumi preview [アリーナ]` | プレイ面・土台・判定ライン・観戦席を10秒表示 |
| `/tsumi piece <ピース名>` | ピースの形を目の前に10秒表示 |
| `/tsumi setsign <join\|leave\|start <アリーナ>\|rank <順位>\|delete>` | 看板を登録／解除 |
| `/tsumi wand` | 管理ツールを受け取る |
| `/tsumi menu` | 管理メニュー（GUI）を開く |
| `/tsumi test [アリーナ]` | 1人テスト（記録を残さない） |
| `/tsumi stop [アリーナ]` | 試合を強制停止 |
| `/tsumi reload` | 設定ファイルを再読み込み |
| `/tsumi lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧 |

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/tsumi status` と `/tsumi preview` を確認してください。アリーナの原点が設定済みで、プレイ面と土台の下が空気になっており、ロビーに最低人数（既定2人）がいる必要があります。プレイ面にブロックがあると開始できません。

??? failure "アリーナの設定を変更できない"
    そのアリーナが試合で使用中（進行中・カウントダウン中）だと変更・削除できません。試合の終了を待つか `/tsumi stop` で停止してください。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/tsumi lobbyfix <プレイヤー>` で復旧できます。退避データはファイルに保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【どうぶつ積みバトル】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。試合結果も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、**サーバークラッシュ後の再ログインでも復元** します（試合終了時は復元せず、ロビー在籍中はずっと空のまま）。異常で持ち物が戻らない・ロックが残った場合はOPが `/tsumi lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません）。地点・看板・アリーナなどのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも操作アイテムを使え、登録看板は蝋引きして編集画面が開かないようにします。

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
| `name-tag.display-name` | どうぶつ積みバトル | 「【】」に入れる正式名 |
| `name-tag.color` | GOLD | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも操作アイテムを使える |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← どうぶつ積みバトル 概要へ](index.md){ .md-button }
