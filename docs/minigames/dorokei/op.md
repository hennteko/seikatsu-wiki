<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# DorokeiGame ― OP・運営ガイド { .page-op #dorokei-op }

DorokeiGame の導入・会場（ロビー＋エリア）の設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | DorokeiGame |
| バージョン | 3.1（コマンド統合・簡略化版） |
| api-version | 26.1.2 |
| 作者 | へんりー |
| メインコマンド | `/dorokei`（エイリアス `/dk`） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/DorokeiGame/config.yml` |

!!! success "最新アップデートの変更点（ラッキーチェスト＆逃走者支援アイテム）"
    逃走者（市民）を支援する **ラッキーチェスト** が追加されました。マップ上のチェスト・トラップチェスト・樽を会場に登録すると、市民が右クリックした際にランダムで支援アイテムを入手できます（警官・収監中は利用不可）。あわせて次が追加されています。

    - **ラッキーチェストの登録／解除** … `/dorokei addchest`（視線先のチェストを登録）・`/dorokei removechest`（1か所解除）・`/dorokei removechest clear`（全解除）。旧 `/dorokei setchest` / `setchest clear` も後方互換で受理されます。
    - **逃走者支援アイテム5種** … スピードポーション／跳躍のお守り／蜘蛛の巣トラップ／鈍足の罠／レーダージャマー（加えて予備の煙幕玉・緊急テレポート用エンダーパールが抽選で出現）。
    - **`config.yml` の `chest-settings`** … 再開封クールダウン（`cooldown-seconds`）と出現重み付きルートテーブル（`loot`）を設定可能。
    - **`/dorokei status`** にラッキーチェストの登録数を表示。

!!! success "コマンド統合・全員開放（最新版）"
    共通ルールに合わせ、コマンド体系が整理されました。

    - **`join` / `leave` / `start` は全員が実行可能** になりました（コマンドブロック／コンソールからの `start` も可）。`status` / `list` / `stats` も閲覧のみのため全員可です。
    - 看板登録は **`/dorokei setsign join` / `leave` / `start` / `delete`** に統一されました（旧 `setstart` / `removesign` は後方互換で受理）。
    - 数値設定コマンド **`/dorokei settime <秒>` / `setmax <人数>` / `setmin <人数>`** が追加されました（即時 config 保存）。
    - ラッキーチェスト登録は **`/dorokei addchest` / `removechest`** に統一（旧 `setchest` は後方互換）。
    - 成績表示 **`/dorokei stats [名前]`** が追加されました。
    - `start`（新標準名）と `gamestart`（後方互換エイリアス）はどちらも動作します。

!!! note "会場運用について（単一会場方式）"
    会場は **1サーバー1会場（単一会場）方式** です。セットアップ・看板・開始コマンドから **ゲーム名の指定は不要** です。牢屋は複数登録可（`/dorokei setjail` / `setjail clear`）、人数別の警官数は `/dorokei setcop <人数> <警官数>` で上書きでき、`/dorokei status` で設定状況を確認できます。制限時間・最大人数・最低人数は `/dorokei settime` / `setmax` / `setmin` でも変更できます。

!!! note "コマンド名について（後方互換）"
    ゲーム開始は **`/dorokei start`** が標準名です（`gamestart` も後方互換エイリアスとして動作）。看板登録は **`/dorokei setsign start` / `delete`** が標準で、旧 `setstart` / `removesign` も受理されます。ラッキーチェストは **`/dorokei addchest` / `removechest`** が標準で、旧 `setchest` / `setchest clear` も受理されます。なお `start` / `join` / `leave` / `status` / `list` / `stats` は **全員が実行可能** です（OP 限定はセットアップ・看板・数値設定・`delete` のみ）。

## 導入手順

1. ビルドした `DorokeiGame` の jar ファイルをサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動・再起動すると `plugins/DorokeiGame/config.yml` が自動生成される。
3. 後述の手順でゲーム（ロビー＋エリア）を設定する。
4. 必要に応じて `config.yml` を直接編集し、サーバーを再起動して反映する。

!!! note "config の反映について"
    本プラグインには config 再読み込み用のコマンドがありません。`config.yml` を手で編集した場合は **サーバーの再起動** で反映してください。なお `setstartspawn` / `setlobby` / `setfield` / `setjail` / `addchest` / `setcop` / `settime` / `setmax` / `setmin` / `delete` 系コマンドで変更した内容は即座にファイルへ保存され、ゲームインスタンスへも即時反映されます（`chest-settings` の出現重み・クールダウンを手動編集した場合のみ再起動が必要です）。

## config.yml 設定項目

| キー | 既定値 | 説明 |
|---|---|---|
| `version` | "3.1" | 設定ファイルのバージョン |
| `spawn-location` | world / 0,64,0 | 初期リスポーン地点。ログアウト時に戻り先が不明な場合に使用 |
| `games` | `{}` | 会場定義。単一会場（内部名 `main`）で管理され、セットアップコマンドで自動的に追加される |
| `games.main.lobby` | — | 待機ロビー定義。`world` / `x` / `y` / `z` / `yaw` / `pitch` / `max-players`（既定20） |
| `games.main.area` | — | ゲームエリア定義。`world` / `pos1` / `pos2` / `jails`（牢屋座標のリスト） / `luck-chests`（ラッキーチェスト座標のリスト） |
| `games.main.area.luck-chests` | — | ラッキーチェストの座標リスト。`/dorokei addchest` で自動追加される |
| `signs` | `{}` | 登録済み看板（参加 / 離脱 / 開始）。`setsign join` / `leave` / `start` で自動追加され、再起動後も復元される |
| `game-settings.game-time` | 480 | ゲーム時間（秒）。既定8分。`/dorokei settime <秒>` でも変更可 |
| `game-settings.escape-preparation-time` | 30 | 逃走準備時間（秒）。この間は警官が動けない |
| `game-settings.min-players` | 2 | ゲーム開始に必要な最低人数。`/dorokei setmin <人数>` でも変更可 |
| `game.result-seconds` | 10 | 試合終了後、結果発表を表示してからロビーへ戻すまでの秒数 |
| `chest-settings.cooldown-seconds` | 45 | 同じラッキーチェストの再開封クールダウン（秒） |
| `chest-settings.loot` | 7種の既定テーブル | ラッキーチェストの出現アイテム。`item`（アイテムキー）と `weight`（出現重み・大きいほど出やすい）のリスト |
| `cop-allocation` | 2〜20の対応表 | プレイヤー数ごとの警官人数の割り振り表（`/dorokei setcop` で上書き可能） |
| `defaults.cop-count` | 1 | フォールバック用の既定警官人数 |
| `defaults.game-time` | 300 | フォールバック用の既定ゲーム時間（秒） |

!!! info "牢屋（jails）は複数登録に対応"
    `games.main.area.jails` は牢屋座標の **リスト** で、`/dorokei setjail` を実行するたびに1か所ずつ追加されます。旧形式の単一 `jail: { x:.., y:.., z:.. }` も引き続き読み込めます。座標は手動編集よりもセットアップコマンドでの登録を推奨します。

### games セクションの構造例

```yaml
games:
  main:
    lobby:
      world: world
      x: 0.0
      y: 64.0
      z: 0.0
      yaw: 0.0
      pitch: 0.0
      max-players: 20
    area:
      world: world
      pos1: { x: 100.0, y: 60.0, z: 100.0 }
      pos2: { x: 200.0, y: 80.0, z: 200.0 }
      jails:
        - { x: 150.0, y: 65.0, z: 150.0 }
        - { x: 160.0, y: 65.0, z: 140.0 }
      luck-chests:
        - { x: 120.0, y: 64.0, z: 130.0 }
        - { x: 180.0, y: 64.0, z: 170.0 }
```

### 警官の自動割り振り（`cop-allocation`）

参加人数に応じて警官の人数を自動決定します。既定の対応表は次のとおりです。

| 参加人数 | 警官数 | 参加人数 | 警官数 |
|---|---|---|---|
| 2〜4人 | 1人 | 12〜14人 | 5人 |
| 5〜6人 | 2人 | 15〜17人 | 6人 |
| 7〜8人 | 3人 | 18〜20人 | 7人 |
| 9〜11人 | 4人 | | |

割り振りはコマンドでも上書きできます。

```text title="人数別の警官数を設定（例: 6人参加時は警官2人）"
/dorokei setcop 6 2
```

!!! tip "対応表にない人数のとき"
    `cop-allocation` に該当する人数の項目がない場合は、内部のフォールバック計算（約35%目安）で警官数が決定されます。21人以上の場合も内部計算で7人になります。`/dorokei setcop <人数> <警官数>` で個別に上書きすると、その人数のときの警官数を固定できます（警官数は1以上かつ参加人数未満で指定）。

### ラッキーチェスト（`chest-settings`）

逃走者（市民）専用のラッキーチェストの挙動を設定します。チェストの **設置場所** は `config.yml` を直接編集せず、ゲーム内で `/dorokei addchest` を使って登録してください（後述）。出現アイテムの内容や確率はこの `chest-settings` で調整します。

| キー | 既定値 | 説明 |
|---|---|---|
| `chest-settings.cooldown-seconds` | 45 | 同じチェストを再度開けられるようになるまでの秒数 |
| `chest-settings.loot` | 下表の7種 | 出現アイテムのリスト。各要素は `item`（アイテムキー）と `weight`（出現重み） |

ルートテーブルの既定値と各アイテムキーは次のとおりです。`weight` が大きいほど出やすくなります。

| `item` キー | アイテム | 既定 `weight` | 効果 |
|---|---|---|---|
| `speed_potion` | スピードポーション | 10 | 10秒間 速度II |
| `jump_charm` | 跳躍のお守り | 10 | 15秒間 跳躍III |
| `smoke_bomb` | 予備の煙幕玉 | 8 | 配布と同じ煙幕玉を補充 |
| `ender_escape` | 緊急テレポート | 5 | エンダーパール（バニラ動作） |
| `cobweb_trap` | 蜘蛛の巣トラップ | 5 | 足元に蜘蛛の巣を設置（約8秒で消滅） |
| `slow_trap` | 鈍足の罠 | 5 | 周囲6m以内の警官を4秒間 鈍足 |
| `radar_jammer` | レーダージャマー | 4 | 全警官の追跡コンパスを20秒間 無効化 |

```yaml title="chest-settings の記述例"
chest-settings:
  cooldown-seconds: 45
  loot:
    - { item: speed_potion, weight: 10 }
    - { item: jump_charm,   weight: 10 }
    - { item: smoke_bomb,   weight: 8 }
    - { item: ender_escape, weight: 5 }
    - { item: cobweb_trap,  weight: 5 }
    - { item: slow_trap,    weight: 5 }
    - { item: radar_jammer, weight: 4 }
```

!!! note "loot 未設定でも動作します"
    `chest-settings.loot` が未設定（または空）の場合は、上表の組み込み既定テーブルで動作します。特定のアイテムを出さないようにしたい場合は、その行を削除するか `loot` を書き換えてサーバーを再起動してください。

## セットアップ手順（単一会場）

専用ワールドを用意し、OP権限で以下のコマンドを **設定したい地点に立って** 実行します（実行位置が座標として保存されます）。会場は1つ（内部名 `main`）なので、ゲーム名の指定は不要です。

```text title="① 初期リスポーン地点を現在地に設定"
/dorokei setstartspawn
```

```text title="② 待機ロビーを現在地に設定"
/dorokei setlobby
```

```text title="③ エリア角1を現在地に設定"
/dorokei setfield 1
```

```text title="④ エリア角2を現在地に設定"
/dorokei setfield 2
```

```text title="⑤ 牢屋を現在地に追加（別の場所で繰り返すと複数登録できる）"
/dorokei setjail
```

```text title="牢屋の登録を全て解除したいとき"
/dorokei setjail clear
```

```text title="⑥ ラッキーチェストを登録（視線先のチェスト・トラップチェスト・樽。5ブロック以内）"
/dorokei addchest
```

```text title="視線先のラッキーチェストを1か所だけ解除したいとき"
/dorokei removechest
```

```text title="ラッキーチェストの登録を全て解除したいとき"
/dorokei removechest clear
```

設定の流れは次のとおりです。

1. `/dorokei setstartspawn` で初期リスポーン地点を設定する。
2. ロビーにする場所に立ち `/dorokei setlobby` を実行する（ロビーの既定上限は20人。`config.yml` の `games.main.lobby.max-players` を手動で書き換えれば変更可能）。
3. エリアの角1に立ち `/dorokei setfield 1`、対角の角2に立ち `/dorokei setfield 2` を実行する（pos1/pos2 がそろうとエリアサイズがチャットに表示されます）。
4. 牢屋にする場所に立ち `/dorokei setjail` を実行する。必要なら別の場所で繰り返し実行して牢屋を増やせます。
5. （任意）マップ上にチェスト・トラップチェスト・樽を設置し、それを見ながら `/dorokei addchest` を実行してラッキーチェストを登録する。別のチェストで繰り返すと複数登録できます。
6. `/dorokei status` で4点（ロビー / pos1 / pos2 / 牢屋）・ラッキーチェスト・看板の設定状況を確認する。

!!! note "ラッキーチェストは任意設定です"
    ラッキーチェスト（`/dorokei addchest`）は会場成立の必須条件ではありません。未登録でもゲームは開始でき、その場合は逃走者支援アイテムが手に入らないだけです。設置するとゲーム性が大きく変わるので、会場に合わせて数か所登録するのがおすすめです。

!!! tip "次のステップが自動で案内されます"
    各セットアップコマンドの実行後、未設定の項目（lobby / pos1 / pos2 / jail）がチャットに表示されます。4点すべてがそろうと「セットアップが完了しました!」と通知され、`/dorokei start` で開始できる旨が案内されます。

!!! warning "pos1 / pos2 のワールドは一致が必須"
    pos1 と pos2 が異なるワールドにあるとセットアップが拒否されます。先に登録した方と同じワールドで取り直してください。

## 看板の設置

プレイヤーがロビーへ参加するための看板をコマンドで登録します。看板を設置し、看板を見ながら以下のコマンドを実行してください（テキストはプラグインが自動で書き込みます）。

```text title="参加看板を登録（クリックで参加・ロビーへTP・人数表示）"
/dorokei setsign join
```

```text title="退出看板を登録（クリックでロビーから退出）"
/dorokei setsign leave
```

```text title="開始看板を登録（クリックでゲーム開始）"
/dorokei setsign start
```

```text title="視線先の看板の登録を解除"
/dorokei setsign delete
```

!!! note "旧コマンドも受理されます"
    旧 `/dorokei setstart`（→ `setsign start`）と `/dorokei removesign`（→ `setsign delete`）も後方互換で受理されますが、新しい `setsign` 系コマンドの使用を推奨します。

!!! note "手書き登録は廃止済み"
    以前の方式（参加看板に `[Dorokei]`/`[ドロケイ]` を手書き、退出看板に `[DorokeiLeave]`/`[ドロケイ退出]` を手書きして認識させる方式）は廃止されています。看板は必ずコマンドで登録してください（座標で保存され、再起動後も復元されます）。

## ゲームの運営

```text title="ゲームを開始（全員可・ロビーに最低人数が必要・コマンドブロック可）"
/dorokei start
```

```text title="会場の状態を表示（全員可）"
/dorokei list
```

```text title="設定状況（地点・看板・数値設定・現在の状態）を確認（全員可）"
/dorokei status
```

数値設定は以下のコマンドで即時に変更・保存できます（OP 限定）。

```text title="制限時間を設定（秒・10以上）"
/dorokei settime 480
```

```text title="ロビー最大人数を設定（1以上）"
/dorokei setmax 20
```

```text title="開始に必要な最低人数を設定（1以上）"
/dorokei setmin 2
```

1. プレイヤーが看板またはコマンドでロビーに集まるのを待つ（既定で最低2人必要）。
2. `/dorokei start`（全員可）または開始看板でゲームを開始する。
3. ゲームは「カウントダウン → 逃走準備（既定30秒）→ 追跡開始 → 決着」と進行します。
4. 終了から既定10秒後に参加者は自動でロビーへ戻され、同じロビーへ再参加した状態になります。

## 管理コマンド一覧

セットアップ・看板・数値設定・`delete` は **OP（`dorokei.admin`）限定**、`start` / `join` / `leave` / `list` / `status` / `stats` は **全員可** です。

| コマンド | 権限 | 説明 |
|---|---|---|
| `/dorokei`（引数なし） | 全員 | ヘルプを表示する |
| `/dorokei setstartspawn` | OP | 初期リスポーン地点を現在地に設定する |
| `/dorokei setlobby` | OP | 現在地を待機ロビーに設定する |
| `/dorokei setfield 1` | OP | エリアの角1を現在地に設定する |
| `/dorokei setfield 2` | OP | エリアの角2を現在地に設定する |
| `/dorokei setjail` | OP | 牢屋を現在地に追加する（複数登録可） |
| `/dorokei setjail clear` | OP | 牢屋の登録を全て解除する |
| `/dorokei addchest` | OP | 視線先のチェスト（樽可）をラッキーチェストに追加する（複数登録可） |
| `/dorokei removechest` | OP | 視線先のラッキーチェストを1か所解除する |
| `/dorokei removechest clear` | OP | ラッキーチェストの登録を全て解除する |
| `/dorokei setcop <人数> <警官数>` | OP | 人数別の警官数を設定する |
| `/dorokei settime <秒>` | OP | 制限時間を設定する（10秒以上） |
| `/dorokei setmax <人数>` | OP | ロビー最大人数を設定する（1以上） |
| `/dorokei setmin <人数>` | OP | 開始に必要な最低人数を設定する（1以上） |
| `/dorokei delete` | OP | 会場の設定を削除する |
| `/dorokei setsign join` | OP | 視線先の看板を参加看板に登録する |
| `/dorokei setsign leave` | OP | 視線先の看板を離脱看板に登録する |
| `/dorokei setsign start` | OP | 視線先の看板を開始看板に登録する |
| `/dorokei setsign delete` | OP | 視線先の看板の登録を解除する |
| `/dorokei start` | 全員 | ゲームを開始する（コマンドブロック可・`gamestart` も可） |
| `/dorokei list` | 全員 | 会場の状態を表示する |
| `/dorokei status` | 全員 | 設定状況を確認する |
| `/dorokei stats [名前]` | 全員 | 成績を確認する（名前省略時は自分） |
| `/dorokei join [名前]` | 全員（他者操作はOP） | ロビーに参加する（通常は看板を使用） |
| `/dorokei leave [名前]` | 全員（他者操作はOP） | ロビーから退出する（通常は看板を使用） |

!!! note "後方互換エイリアス"
    `gamestart`（→ `start`）・`setchest` / `setchest clear`（→ `addchest` / `removechest clear`）・`setstart`（→ `setsign start`）・`removesign`（→ `setsign delete`）も引き続き受理されます。

## 権限ノード

`plugin.yml` に定義されている権限ノードは **`dorokei.admin`（既定 OP）の1つだけ** です。コマンドレベルの permission は付けず、コード側でサブコマンドごとに権限を判定します。

| 権限 | 既定 | 用途 |
|---|---|---|
| `dorokei.admin` | OP | セットアップ系（`setstartspawn` / `setlobby` / `setfield` / `setjail` / `addchest` / `removechest`） / 数値設定（`setcop` / `settime` / `setmax` / `setmin`） / 看板系（`setsign` join/leave/start/delete） / `delete` / 他プレイヤーの `join` / `leave` の実行 |

!!! note "全員可コマンドについて"
    `/dorokei start`・`join`（自分）・`leave`（自分）・`status`・`list`・`stats` は **権限不要で誰でも実行できます**（読み取りや自分の操作のため権限チェックなし）。他プレイヤーを指定する `/dorokei join <名前>` / `leave <名前>` のみ OP 限定です。看板の右クリックも従来どおり権限不要で利用できます。

## 実装状況

!!! info "実装済みの機能"
    - 単一会場の統合管理（ロビー＋エリアを `games.main` 配下にまとめて保持・ゲーム名指定不要）
    - 簡略化されたセットアップコマンド（ゲーム名引数の廃止・ワンド廃止）
    - **牢屋の複数登録**（`setjail` で追加・`setjail clear` で全解除）
    - **人数別の警官数の手動設定**（`setcop`）・**数値設定コマンド**（`settime` / `setmax` / `setmin`）と **設定状況の確認**（`status`）
    - **成績記録・表示**（`stats`：参加数／勝利数／捕獲数／救出数／捕まった数）
    - **`join` / `leave` / `start` のコマンド化**（全員可。他プレイヤー操作は OP 限定）
    - ロビー（既定上限20人・看板参加）
    - ゲームエリア管理（pos1/pos2/牢屋による範囲設定・エリア外移動制限）
    - 参加人数に応じた警官の自動割り振り（`cop-allocation`）
    - 逃走準備フェーズ（警官の盲目・移動制限）と追跡フェーズ
    - 捕獲（警棒）・牢獄・市民による救出（救出時の速度上昇バフ）
    - 警官／市民の専用アイテム（煙幕玉・ダッシュブーツ・スプリントブースト・ネット投擲）
    - **ラッキーチェスト**（`addchest` で登録・逃走者専用・チェストごとの再開封クールダウン・出現重み付きルートテーブル）
    - **逃走者支援アイテム5種**（スピードポーション・跳躍のお守り・蜘蛛の巣トラップ・鈍足の罠・レーダージャマー）＋抽選用の予備煙幕玉・緊急テレポート
    - ボスバー・**専用サイドバー（残り時間／逃走中／捕獲済／救出／役割）**・コンパス・追跡パーティクル・心臓の鼓動音などの演出
    - 勝敗判定（全市民捕獲／時間切れ／全員ログアウト）
    - ログアウト処理（ゲーム・ロビーからの離脱、次回ログイン時の位置復帰）
    - ゲーム終了後の自動ロビー帰還・再参加

!!! warning "注意点・制限"
    - 一部コマンドは名称が統一されました（`gamestart` → `start`、`setchest` → `addchest`、`setstart` → `setsign start` など）。旧名も後方互換で受理されますが、新名の使用を推奨します。
    - config 再読み込み用コマンドはありません。`config.yml` を手動編集した場合はサーバー再起動が必要です。
    - 会場は1つ（`main`）のみです。複数会場の同時運用には対応していません。

## トラブルシューティング

??? failure "ゲームが開始できない（プレイヤーが足りません）"
    ゲーム開始には **ロビーに最低2人** が必要です。`/dorokei list` でロビーの参加人数を確認してください。

??? failure "「ゲームエリアが設定されていません」と表示される"
    エリア（pos1 / pos2 / 牢屋）が未設定です。`/dorokei setfield 1` / `/dorokei setfield 2` / `/dorokei setjail` をすべて実行してください。エリアは pos1・pos2・牢屋の3点がそろって初めて有効になります。`/dorokei status` で不足項目を確認できます。

??? failure "会場が [未完成] と表示される"
    ロビーまたはエリアの一部が未設定です。`/dorokei status` で `ロビー: 未設定` や `フィールド1(pos1): 未設定` 等を確認し、不足している項目を `/dorokei setlobby` / `/dorokei setfield 1` / `/dorokei setfield 2` / `/dorokei setjail` で追加してください。3点がそろわないエリアは読み込まれません。ワールドが存在しない場合も読み込みに失敗します。

??? failure "「pos2 と異なるワールドです」と言われる"
    pos1 と pos2 は同じワールドである必要があります。先に登録した方と同じワールドで取り直してください。

??? failure "参加看板をクリックしても参加できない"
    `/dorokei setsign join` で登録済みか確認してください（座標登録が必要なため手書きは機能しません）。対象ロビーがゲーム中の場合は参加できません。

??? failure "config.yml を編集したのに反映されない"
    config 再読み込みコマンドはありません。サーバーを再起動して反映してください。

??? failure "ラッキーチェストを右クリックしても何も出ない"
    まず `/dorokei addchest` でそのチェストが登録済みか確認してください（`/dorokei status` の「ラッキーチェスト」件数で確認できます）。登録済みでも、**警官が開いた場合**や**収監中**は出ません（逃走者専用のため）。また同じチェストには再開封クールダウン（既定45秒）があります。なお `/dorokei addchest` はチェスト・トラップチェスト・樽のいずれかを **5ブロック以内で見ながら** 実行する必要があります。

??? failure "アップグレード後、ラッキーチェストの抽選内容を変えたい / 反映されない"
    `chest-settings.loot` を編集してサーバーを再起動してください。**v3.0 など古いバージョンから更新した既存サーバーでは、既存の `config.yml` に `chest-settings` が自動追記されません**（組み込み既定テーブルで動作します）。抽選内容をカスタムしたい場合は、`config.yml` に `chest-settings` セクションを手動追記するか、一度 `config.yml` を退避してサーバー再起動で再生成してください。

??? failure "会場を丸ごと作り直したい"
    `/dorokei delete` で会場の設定を丸ごと削除できます。削除後に同じ手順で再設定してください。牢屋だけを取り直したい場合は `/dorokei setjail clear` で全解除してから登録し直せます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← DorokeiGame 概要へ](index.md){ .md-button }
