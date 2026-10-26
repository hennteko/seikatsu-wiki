<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# スロット ― OP・運営ガイド { .page-op #slot-op }

スロット（slot モジュール）の有効化・`slot.yml` の設定項目・シンボル重み・配当の調整・権限をまとめます。スロットは単独プラグインではなく **CasinoPlugin の slot モジュール** です。

## 基本情報

| 項目 | 値 |
|---|---|
| 所属プラグイン | CasinoPlugin（統合プラグイン） |
| モジュール ID | `slot` |
| メインコマンド | `/slot [help\|solo\|status\|remote]` ／ OP: `setsign <join\|delete>\|stop [solo [プレイヤー]]\|wand\|menu\|reload\|lobbyfix <プレイヤー> [force]` |
| 設定ファイル | `plugins/CasinoPlugin/modules/slot.yml` |
| 有効化フラグ | `plugins/CasinoPlugin/config.yml` の `modules.slot.enabled` |
| 通貨 | エメラルド（bank モジュールの口座残高。EmeraldAPI 経由） |
| 専用jar | 不要（CasinoPlugin に内蔵） |

!!! info "slot モジュールの位置づけ"
    スロットは CasinoPlugin（銀行＋10ゲームの統合プラグイン）の中の1モジュールです。専用jarの導入は不要で、CasinoPlugin 本体を入れれば slot モジュールも同梱されています。賭け金・配当の処理はすべて bank モジュールの口座（EmeraldAPI）を通じて行われるため、**bank モジュールが先に起動している必要があります**。

## 有効化

スロットは CasinoPlugin のモジュールとして動作します。専用jarの導入は不要です。

1. CasinoPlugin 本体を `plugins/` に導入し、サーバーを起動する（`config.yml` と `modules/slot.yml` が自動生成されます）。
2. `plugins/CasinoPlugin/config.yml` の `modules:` ブロックで `slot.enabled: true` にする。
3. スロット固有の設定（シンボル重み・配当・確変など）は `plugins/CasinoPlugin/modules/slot.yml` を編集する。
4. サーバーを再起動して設定を反映する。

!!! warning "bank モジュールが前提"
    slot モジュールは起動時に EmeraldAPI（bank モジュールが提供する口座機能）が利用可能かを確認します。`modules.bank.enabled` が false だと slot モジュールの起動が失敗します。bank は通常 true 固定で運用してください。

!!! note "config.yml と slot.yml の役割分担"
    `config.yml` はモジュールの ON / OFF だけを扱います。スロット本体の細かい挙動（リール速度・シンボル重み・配当倍率・確変回転数など）はすべて `modules/slot.yml` に分離されています。

## slot.yml 設定項目

`plugins/CasinoPlugin/modules/slot.yml` の各キーは以下のとおりです。旧 Slot2 プラグインの config.yml を移植したものです。

### リール設定（`reel`）

| キー | 既定値 | 説明 |
|---|---|---|
| `reel.spin_speed` | 2 | リール回転の更新間隔（tick単位）。小さいほど速く回る |
| `reel.bedrock_spin_speed` | 3 | 統合版（Bedrock）プレイヤーの回転速度下限（tick単位）。GUIの高頻度更新でカクつくのを防ぐため、統合版では `spin_speed` がこの値より小さくてもこの値まで引き上げられる |
| `reel.slip_frames` | 1 | 目押しの滑りコマ数。停止ボタンを押してから何コマ先まで滑って止まるか（0〜3推奨） |

### シンボル設定（`symbols`）

各シンボルに **通常時の出現重み**・**確変時の出現重み**・**配当倍率** を設定します。倍率は3つそろったときの「賭け金 × 倍率」に使われます。

!!! warning "このスロットは目押し式（skill-stop）です"
    `normal_weight` / `kakuhen_weight` は **各リールの初期絵柄の抽選** にしか使われません。実際にリールが止まる絵柄は目押し（停止ボタン）で決まるため、**重みを上げても当選率は直接は上がりません**。当たりやすさは重みではなく、下記の **「引き込み」（`kakuhen.assist-percent` / `fever.assist-percent`）** で表現します。重みは「どの絵柄が盤面に現れやすいか」の見た目バランス調整と考えてください。

| シンボル | `normal_weight`（通常重み） | `kakuhen_weight`（確変重み） | `multiplier`（倍率） |
|---|---|---|---|
| `DIAMOND`（ダイヤ） | 7 | 15 | 10.0 |
| `GOLD`（金インゴット） | 20 | 25 | 5.0 |
| `IRON`（鉄インゴット） | 35 | 40 | 3.0 |
| `COAL`（石炭） | 35 | 19 | 0.5 |
| `BARRIER`（ハズレ） | 3 | 1 | 0.0 |

!!! note "重みと盤面の関係"
    各リールの初期絵柄の出現率は「そのシンボルの重み ÷ 全シンボルの重み合計」で決まります。確変モードに突入すると `normal_weight` の代わりに `kakuhen_weight` が使われ、盤面にダイヤ・金が現れやすく石炭・ハズレが現れにくくなります。ただし前述のとおり **最終的な当たりやすさを決めるのは重みではなく引き込み**（`kakuhen.assist-percent`）です。`multiplier` が `0.0` の `BARRIER` は3つそろっても配当がありません。

### 確変設定（`kakuhen`）

| キー | 既定値 | 説明 |
|---|---|---|
| `kakuhen.diamond_turns` | 100 | ダイヤ3つが揃ったときに付与される確変回転数。確変中は1回転ごとに1消費される |
| `kakuhen.assist-percent` | 30 | 確変中の **引き込み確率（%）**。2本目以降のリールが停止したとき、先に止まった絵柄へ補正される確率。これが確変中の当たりやすさの本体 |

### フィーバー設定（`fever`）

フィーバータイム（後述のサーバー全体イベント）が開催中のときだけ適用される値です。

| キー | 既定値 | 説明 |
|---|---|---|
| `fever.assist-percent` | 50 | フィーバー中の **引き込み確率（%）**。確変中の `kakuhen.assist-percent` より高めに設定し、全体で当たりやすくする |
| `fever.payout-multiplier` | 1.0 | フィーバー中の配当倍率。`1.0` は変化なし。`1.0` 未満を指定してもコード側で `1.0` に切り上げられる（配当は減らせない） |

### デバッグ

| キー | 既定値 | 説明 |
|---|---|---|
| `debug` | false | true にするとリールの停止・滑り・現在表示シンボルなどの詳細ログを出力する（本番では false 推奨） |

!!! tip "配当バランスの調整"
    配当額を渋くしたい場合は各シンボルの `multiplier` を下げます。**当たりやすさ（当選頻度）を調整したい場合は重みではなく引き込み**（`kakuhen.assist-percent`・`fever.assist-percent`）を増減してください。目押し式のため重みは当選率を直接は動かしません。賭け金は `1・5・10・20・50 E` の5段階で固定（コード内定義）のため、slot.yml では変更できません。

## フィーバータイム（サーバー全体イベント）

フィーバータイムは slot.yml ではなく **CasinoPlugin 共通の `config.yml` の `events.slot-fever`** で管理される、サーバー全体イベントです。開催中は引き込み確率が `fever.assist-percent`（既定50%）に引き上げられ、全体で当たりやすくなります。発生・終了・残り時間は全体チャットで告知されます。

```text title="config.yml（抜粋）events.slot-fever"
events:
  slot-fever:
    enabled: true                 # フィーバータイム抽選の有効化
    check-interval-minutes: 30    # 抽選間隔（分）
    chance-percent: 25            # 抽選ごとの発生確率（%）
    duration-minutes: 10          # 効果時間（分）
    cooldown-minutes: 60          # 終了後の再抽選禁止時間（分）
    min-online-players: 1         # オンライン人数がこれ未満なら抽選しない
    announce-remaining-minutes: [5, 1]  # 残り告知を出す分
```

OPは抽選を待たずに手動で開始・終了できます（`casino.admin` または OP）。

```text title="フィーバータイムを手動開始（分を省略すると duration-minutes）"
/casino event start slot-fever [分]
```

```text title="フィーバータイムを手動終了"
/casino event stop slot-fever
```

```text title="開催中イベントの確認（全員可）"
/casino event status
```

!!! note "進行中イベントは再起動をまたいで継続"
    開催中のフィーバータイムは `casino_events.yml` に保存され、サーバーを再起動しても残り時間を引き継いで継続します。`fever.payout-multiplier` を `1.0` より大きくすると、フィーバー中だけ配当も上乗せできます。

## 起動看板の設置

一般プレイヤーは `[Slot]` 看板の右クリックでスロットGUIを開きます。看板を設置し、看板を見ながら（5ブロック以内）以下のコマンドを実行すると自動で整形されます。

```text title="起動看板を登録"
/slot setsign
```

看板には `[Slot]`／`クリックで`／`スロットを開く` が自動で書き込まれます。起動看板は `/slot setsign` での登録（座標で判定）が必要で、手書きの `[Slot]` 看板は起動看板としては動作しません（OPが右クリックすると未登録の案内が表示されます）。

## 管理コマンド

`/slot`（引数なし）と `/slot solo` は **全プレイヤーが使用可能** で、賭け金選択GUIを開きます（`[Slot]` 看板の右クリックと同等）。`/slot status` も全員が実行できます。看板設置・停止・管理ツール・リロードなどは OP または `slot.admin`、`/slot remote` は `slot.remote` 権限を要求します。

```text title="賭け金選択GUIを開く（全員可）"
/slot
```

```text title="賭け金選択GUIを開く（全員可・solo 明示）"
/slot solo
```

```text title="スロットの設定状況を表示（全員可・読み取り専用）"
/slot status
```

```text title="スロット起動ロッド（ブレイズロッド）を入手（slot.remote 権限）"
/slot remote
```

```text title="視線先の看板を [Slot] 起動看板として登録（OP / slot.admin・引数省略時は join）"
/slot setsign
```

```text title="視線先の [Slot] 看板設定を解除（OP / slot.admin）"
/slot setsign delete
```

```text title="進行中のスロットを強制停止して賭け金を返金（OP / slot.admin）"
/slot stop [solo [プレイヤー]]
```

```text title="管理ツールを入手（OP / slot.admin）"
/slot wand
```

```text title="管理メニューを開く（OP / slot.admin）"
/slot menu
```

```text title="slot.yml を再読み込み（OP / slot.admin）"
/slot reload
```

```text title="未復元の預かり持ち物を手動で復元（OP / slot.admin）"
/slot lobbyfix <プレイヤー> [force]
```

| コマンド | 権限 | 説明 |
|---|---|---|
| `/slot` | 全員 | 賭け金選択GUIを開く |
| `/slot solo` | 全員 | 賭け金選択GUIを開く（`/slot` と同じ）。`/slot join` はソロ専用の案内を返すだけ |
| `/slot status` | 全員 | スロットの設定状況（リール速度・滑り・確変/フィーバーの引き込み率・進行中回転数・起動看板の登録数など）を表示する読み取り専用コマンド |
| `/slot remote` | `slot.remote`（OP） | スロット起動ロッド（ブレイズロッド）を入手する。右クリックでGUIを開けるアイテム |
| `/slot setsign` / `/slot setsign join` | OP / `slot.admin` | 視線先の看板を `[Slot]` 起動看板として登録する（引数省略時は `join`） |
| `/slot setsign delete` | OP / `slot.admin` | 視線先の看板の `[Slot]` 設定を解除する（旧 `remove` も互換で動作） |
| `/slot stop [solo [プレイヤー]]` | OP / `slot.admin` | 進行中のスロットを強制停止し、賭け金を返金する |
| `/slot wand` | OP / `slot.admin` | 管理ツール（設置用アイテム）を入手する |
| `/slot menu` | OP / `slot.admin` | 管理メニューを開く |
| `/slot reload` | OP / `slot.admin` | `slot.yml` を再読み込みする |
| `/slot lobbyfix <プレイヤー> [force]` | OP / `slot.admin` | 未復元の預かり持ち物を手動で復元する |

!!! note "設定変更の反映"
    `slot.yml` を変更したあとは `/slot reload` で再読み込みできます。確実に反映したい場合はサーバーの再起動でも構いません。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `slot.admin` | OP | `/slot setsign`（看板の設置・解除）・`stop`・`wand`・`menu`・`reload`・`lobbyfix` などの管理コマンド |
| `slot.remote` | OP | `/slot remote` でスロット起動ロッド（ブレイズロッド）を入手できる |

!!! note "GUIを開く操作に権限は不要"
    賭け金選択GUIを開く操作（`/slot`・`/slot solo`・`[Slot]` 看板の右クリック・起動ロッドの右クリック）は **全プレイヤーが利用可能** で、専用の権限ノードはありません。OP権限が要るのは `setsign`（看板設置）・`stop`・`wand`・`menu`・`reload`・`lobbyfix` などの管理コマンドです。なお旧 `slot.use` 権限は廃止されており、現行コードには存在しません。

!!! note "remote 権限の互換"
    `/slot remote` は内部で `slot.remote` または旧 `slot2.remote` のどちらか一方を持っていれば許可される実装です。plugin.yml に宣言されているのは `slot.remote`（`default: op`）のみで、旧プラグイン由来の `slot2.remote` は宣言されていません。一般プレイヤーに起動ロッドを配りたい場合は、権限プラグイン（LuckPerms 等）で `slot.remote` を明示的に付与してください。

## トラブルシューティング

??? failure "`/slot` が「不明なコマンド」になる"
    slot モジュールが `config.yml` の `modules.slot.enabled: false` で無効化されている可能性があります。無効モジュールはコマンド・リスナーが一切登録されません。起動ログを確認し、`modules.slot.enabled` を `true` にして再起動してください。

??? failure "起動ログに slot モジュールの有効化失敗が出る"
    slot モジュールは起動時に EmeraldAPI（bank モジュール）が利用可能かを確認し、未準備なら `EmeraldAPI not bound; bank module required before slot` という例外で停止します。`modules.bank.enabled` が `true` になっているか確認してください。

??? failure "プレイヤーがスロットを利用できない"
    GUIを開く操作（`/slot`・`/slot solo`・`[Slot]` 看板・起動ロッド）は全プレイヤーが利用できます。利用できない場合は、slot モジュールが無効化されていないか、口座残高がマイナス（借金）になっていないかを確認してください。借金状態のプレイヤーはスロットを利用できない仕様です。

??? failure "`/slot remote` が「権限がありません」になる"
    `/slot remote` は `slot.remote`（または旧 `slot2.remote`）を要求します。`slot.remote` は plugin.yml 上 `default: op` のため、一般プレイヤーに使わせる場合は権限プラグインで `slot.remote` を付与してください。

??? failure "配当や当たりやすさを調整したい"
    配当倍率は `modules/slot.yml` の `symbols.<シンボル>.multiplier` で調整します。**当たりやすさ（当選頻度）は目押し式のため重みではなく引き込みで決まる** ので、確変中は `kakuhen.assist-percent`、フィーバー中は `fever.assist-percent` を増減してください。`normal_weight`・`kakuhen_weight` は盤面に出る絵柄の見た目バランスを変えます。リール速度は `reel.spin_speed`（統合版の下限は `reel.bedrock_spin_speed`）、目押しの滑り具合は `reel.slip_frames`、確変回転数は `kakuhen.diamond_turns` で調整します。編集後はサーバーを再起動してください。

??? failure "リールの挙動を詳しく確認したい"
    `slot.yml` の `debug` を `true` にすると、各リールの停止要求・滑り・現在表示シンボルなどの詳細ログがサーバーコンソールに出力されます。原因切り分けが終わったら本番では `false` に戻してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← スロット 概要へ](index.md){ .md-button }
