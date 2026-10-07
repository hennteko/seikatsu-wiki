<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 一本柱PvP ― OP・運営ガイド { .page-op #pillarpvp-op }

一本柱PvP の導入・地点設定・看板・config・権限・管理コマンドをまとめます。地点・看板・各種設定はコマンドで登録すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | PillarPvP |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/pillar`（エイリアス `/ipp`） |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/PillarPvP/config.yml` |
| 権限ノード | `pillar.admin`（既定OP） |

## 導入手順

1. ビルドした `PillarPvP` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される。
3. `/pillar setstartspawn`・`/pillar setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/pillar setfield 1`・`2` で **柱を生成するフィールド範囲** を設定する（柱はこの範囲の中心に自動生成）。
5. 参加・離脱・開始の看板を設置する。
6. `/pillar status` で設定状況を確認する。

!!! warning "フィールド範囲は専用の空間に"
    試合の **前後にフィールド範囲内のブロックをすべて空気にします**。既存の建築が消えるので、必ず試合用の専用スペースを `setfield` で指定してください。範囲の **下端Y** が柱の底面になります。

## セットアップ手順（コマンド）

地点系は **実行した位置**、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `pillar.admin` 権限が必要です。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/pillar setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/pillar setlobby
```

```text title="フィールド範囲の角1（その場に立って実行）"
/pillar setfield 1
```

```text title="フィールド範囲の角2（その場に立って実行）"
/pillar setfield 2
```

```text title="参加看板を登録（看板を見て実行）"
/pillar setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/pillar setsign leave
```

```text title="開始看板を登録（看板を見て実行）"
/pillar setsign start
```

```text title="視線先の看板の登録を解除"
/pillar setsign delete
```

## ゲーム設定コマンド

```text title="制限時間（秒）を設定（10〜3600）"
/pillar settime 300
```

```text title="ランダムアイテムの配布間隔（秒）を設定（1〜60）"
/pillar setinterval 3
```

```text title="柱の頂上からの積み上げ上限（ブロック）を設定（1〜64）"
/pillar setheight 15
```

```text title="最大参加人数を設定（1〜100）"
/pillar setmax 16
```

```text title="最低開始人数を設定（0〜100）"
/pillar setmin 2
```

```text title="試合の強制停止"
/pillar stop
```

```text title="設定の再読み込み（config を最新版へ自動更新も実施）"
/pillar reload
```

```text title="ロビー前の持ち物が戻らない時の復旧"
/pillar lobbyfix <プレイヤー>
```

```text title="退避データを破棄してロックだけ解除（最終手段）"
/pillar lobbyfix <プレイヤー> force
```

!!! note "設定の反映タイミング"
    `settime`・`setinterval`・`setmax`・`setmin` などは試合中に変更しても現在の試合には影響せず、**次の試合から反映** されます。地点・フィールド・看板の変更は **待機中（試合中・片付け中でない）** のときのみ可能です。

## config.yml 設定項目

### 人数・時間（`settings`）

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 16 | 最大参加人数（`/pillar setmax`） |
| `settings.min-players` | 2 | 最低開始人数（0人開始のみ常に拒否・`/pillar setmin`） |
| `settings.countdown-seconds` | 10 | 柱へ移動してから試合開始までのカウントダウン（秒） |
| `settings.time-limit-seconds` | 300 | 制限時間（秒）。時間切れ時は生存者中の最多キルが勝利、同数は引き分け（`/pillar settime`） |
| `settings.result-seconds` | 10 | 結果発表からロビー復帰までの秒数 |

### ランダムアイテム（`items`）

| キー | 既定値 | 説明 |
|---|---|---|
| `items.interval-seconds` | 3 | 配布間隔（秒）。生存者全員に別々のランダムアイテムを1個ずつ（`/pillar setinterval`） |
| `items.show-next-timer` | true | アクションバーに「次のアイテムまで N秒」を表示 |
| `items.exclude-spawn-eggs` | true | スポーンエッグを抽選から外す（false にすると生まれたモブは試合終了時に消去） |
| `items.excluded-items` | `BEDROCK` / `END_PORTAL_FRAME` / `ELYTRA` | 抽選から外す Material 名のリスト |

!!! note "常に除外される管理者向けアイテム"
    コマンドブロック系・ストラクチャー/ジグソーブロック・バリア・ライト・デバッグ棒・知識の本・スポナー・樹輪(Vault)・バンドル・プレイヤーヘッド・地図・署名済みの本などは、`excluded-items` に書かなくても **常に除外** されます。その世界で無効な実験的アイテムも自動的に抽選から外れます。

### 柱とフィールド（`pillar`）

| キー | 既定値 | 説明 |
|---|---|---|
| `pillar.material` | BEDROCK | 柱の素材 |
| `pillar.height` | 20 | 柱の高さ（ブロック）。底面はフィールド下端Y |
| `pillar.radius` | 10 | フィールド中心から柱までの距離（ブロック） |
| `pillar.min-gap` | 4 | 隣り合う柱の最小間隔（足りなければ自動で半径を広げる） |
| `pillar.build-height-limit` | 15 | 柱の頂上から何ブロック上まで置けるか（`/pillar setheight`） |
| `pillar.fall-line-offset` | 0 | 落下ラインの調整。足の高さが「下端Y + 1 + この値」より下で脱落 |
| `pillar.clear-blocks-per-tick` | 20000 | フィールドを空気へ戻すとき1tickで処理するブロック数（重ければ下げる） |

### 戦闘・キル判定（`combat`）

| キー | 既定値 | 説明 |
|---|---|---|
| `combat.kill-credit-seconds` | 10 | 脱落時、この秒数以内に最後に攻撃した参加者のキルにする |
| `combat.allow-item-drop` | false | true で試合中に配られたアイテムを捨てられる（参加者同士は拾える） |
| `combat.no-hunger` | false | true で試合中は満腹度が減らない |

!!! note "自動保存される領域（手動編集不要）"
    `locations`（初期スポーン・ロビー）・`arena`（フィールド範囲・片付けフラグ）・`signs`（看板）はコマンドで自動保存されます。手書きは不要です。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/pillar setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/pillar setlobby` | ロビー地点を設定 |
| `/pillar setfield <1\|2>` | フィールド範囲の角を設定（柱はこの範囲の中心に自動生成） |
| `/pillar setsign <join\|leave\|start\|delete>` | 看板を登録／解除 |
| `/pillar settime <秒>` | 制限時間を設定（10〜3600） |
| `/pillar setinterval <秒>` | アイテム配布間隔を設定（1〜60） |
| `/pillar setheight <ブロック>` | 柱の頂上からの積み上げ上限を設定（1〜64） |
| `/pillar setmax <数>` / `setmin <数>` | 最大・最低人数を設定 |
| `/pillar stop` | 試合を強制停止 |
| `/pillar reload` | 設定を再読み込み |
| `/pillar lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/pillar status` | 設定状況・現在の状況を確認（全員可） |
| `/pillar stats [名前]` | 成績を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/pillar join` | ロビーに参加（参加看板と同等） |
| `/pillar leave` | ロビーから離脱（離脱看板と同等） |
| `/pillar start` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/pillar status` | 設定・状況を確認（読み取り専用） |
| `/pillar stats [名前]` | 成績（キル・デス・勝利・試合数）を確認 |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `pillar.admin` | OP | 看板設置・各種 set 系設定・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/pillar join`・`/pillar leave`・`/pillar start`・`/pillar status`・`/pillar stats` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/pillar status` を確認してください。フィールド範囲（`setfield 1`・`2`）が設定され、ロビーに最低人数以上いる必要があります。フィールド片付け中は数秒待って再実行します。

??? failure "柱が生成されない／柱が重なる"
    フィールド範囲が狭いと参加人数ぶんの柱が入りきりません。`pillar.min-gap`（柱の最小間隔）を満たせないと自動で半径を広げますが、それでも入らない場合は範囲を広げてください。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/pillar lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/PillarPvP/lobby-inventory.yml` に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

??? failure "config を編集したのに反映されない"
    `/pillar reload` で再読み込みしてください（試合中は不可）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【一本柱PvP】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加・離脱時にサーバー全体へ通知します（`name-tag.announce-join` で切替。試合結果も全体へ告知されます）。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます（「他のミニゲーム ○○ に参加中です」）。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存** … ロビー入場時に退避した所持品を `plugins/PillarPvP/lobby-inventory.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します（試合終了時は復元せず、ロビー在籍中はずっと空のまま）。異常で持ち物が戻らない・ロックが残った場合はOPが `/pillar lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時および `/pillar reload` 時に、`config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は旧ファイルを `config.yml.bak-日時` に退避）。`locations`・`arena`・`signs` などのデータ領域は補完対象外です。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | 未復元の退避データの保持日数 |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | 一本柱PvP | 「【】」に入れる正式名 |
| `name-tag.color` | GOLD | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.spectator-style` | AUTO | 観戦方式（AUTO＝統合版だけゴースト観戦／GHOST＝全員ゴースト／VANILLA＝全員観戦モード） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 一本柱PvP 概要へ](index.md){ .md-button }
