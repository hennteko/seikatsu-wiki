<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# スロット ― OP・運営ガイド { .page-op #slot-op }

スロット（slot モジュール）の有効化・`slot.yml` の設定項目・シンボル重み・配当の調整・権限をまとめます。スロットは単独プラグインではなく **CasinoPlugin の slot モジュール** です。

## 基本情報

| 項目 | 値 |
|---|---|
| 所属プラグイン | CasinoPlugin（統合プラグイン） |
| モジュール ID | `slot` |
| メインコマンド | `/slot [remote]` |
| 設定ファイル | `plugins/CasinoPlugin/modules/slot.yml` |
| 有効化フラグ | `plugins/CasinoPlugin/config.yml` の `modules.slot.enabled` |
| 通貨 | エメラルド（bank モジュールの口座残高。EmeraldAPI 経由） |
| 専用jar | 不要（CasinoPlugin に内蔵） |

!!! info "slot モジュールの位置づけ"
    スロットは CasinoPlugin（銀行＋8ゲームの統合プラグイン）の中の1モジュールです。専用jarの導入は不要で、CasinoPlugin 本体を入れれば slot モジュールも同梱されています。賭け金・配当の処理はすべて bank モジュールの口座（EmeraldAPI）を通じて行われるため、**bank モジュールが先に起動している必要があります**。

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
| `reel.slip_frames` | 1 | 目押しの滑りコマ数。停止ボタンを押してから何コマ先まで滑って止まるか（0〜3推奨） |

### シンボル設定（`symbols`）

各シンボルに **通常時の出現重み**・**確変時の出現重み**・**配当倍率** を設定します。重みが大きいほど抽選で出やすくなります。倍率は3つそろったときの「賭け金 × 倍率」に使われます。

| シンボル | `normal_weight`（通常重み） | `kakuhen_weight`（確変重み） | `multiplier`（倍率） |
|---|---|---|---|
| `DIAMOND`（ダイヤ） | 7 | 15 | 10.0 |
| `GOLD`（金インゴット） | 20 | 25 | 5.0 |
| `IRON`（鉄インゴット） | 35 | 40 | 3.0 |
| `COAL`（石炭） | 35 | 19 | 0.5 |
| `BARRIER`（ハズレ） | 3 | 1 | 0.0 |

!!! note "重みと出現率の関係"
    出現率は「そのシンボルの重み ÷ 全シンボルの重み合計」で決まります。確変モードに突入すると `normal_weight` の代わりに `kakuhen_weight` が使われ、ダイヤ・金の重みが上がり石炭・ハズレが下がるため、当選しやすくなります。`multiplier` が `0.0` の `BARRIER` は3つそろっても配当がありません。

### 確変設定（`kakuhen`）

| キー | 既定値 | 説明 |
|---|---|---|
| `kakuhen.diamond_turns` | 100 | ダイヤ3つが揃ったときに付与される確変回転数。確変中は1回転ごとに1消費される |

### デバッグ

| キー | 既定値 | 説明 |
|---|---|---|
| `debug` | false | true にするとリールの停止・滑り・現在表示シンボルなどの詳細ログを出力する（本番では false 推奨） |

!!! tip "配当バランスの調整"
    配当を渋くしたい場合は各シンボルの `multiplier` を下げるか、`DIAMOND` / `GOLD` の `normal_weight` を下げます。逆に当たりやすくしたい場合は重みを上げてください。賭け金は `1・5・10・20・50 E` の5段階で固定（コード内定義）のため、slot.yml では変更できません。

## 起動看板の設置

一般プレイヤーは `[Slot]` 看板の右クリックでスロットGUIを開きます。看板を設置し、看板を見ながら（6ブロック以内）以下のコマンドを実行すると自動で整形されます。

```text title="起動看板を登録"
/slot setsign
```

看板には `[Slot]`／`クリックで`／`スロットを開く` が自動で書き込まれます。手書き設置（1行目に `[Slot]` または `[スロット]` と入力）もサポートされていますが、`slot.admin` 権限が必要です。

## 管理コマンド

`/slot` コマンドは **OP専用**（`slot.admin` 権限）です。一般プレイヤーは `[Slot]` 看板または起動ロッドからGUIを開きます。

```text title="賭け金選択GUIを開く（OP専用）"
/slot
```

```text title="スロット起動ロッド（ブレイズロッド）を入手"
/slot remote
```

```text title="視線先の看板を [Slot] 起動看板として登録"
/slot setsign
```

| コマンド | 説明 |
|---|---|
| `/slot` | 賭け金選択GUIを開く（OP専用） |
| `/slot remote` | スロット起動ロッド（ブレイズロッド）を入手する。右クリックでGUIを開けるアイテム |
| `/slot setsign` | 視線先の看板を `[Slot]` 起動看板として登録する |

!!! note "設定変更の反映"
    `slot.yml` を変更したあとは **サーバーの再起動** で反映してください。slot モジュール単体のリロードコマンドはありません。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `slot.admin` | OP | `/slot` コマンドの実行・`[Slot]` 看板の設置 |
| `slot.remote` | OP | `/slot remote` でスロット起動ロッド（ブレイズロッド）を入手できる |

!!! note "remote 権限の互換"
    `/slot remote` は内部で `slot.remote` または旧 `slot2.remote` のどちらか一方を持っていれば許可される実装です。plugin.yml に宣言されているのは `slot.remote`（`default: op`）のみで、旧プラグイン由来の `slot2.remote` は宣言されていません。一般プレイヤーに起動ロッドを配りたい場合は、権限プラグイン（LuckPerms 等）で `slot.remote` を明示的に付与してください。

## トラブルシューティング

??? failure "`/slot` が「不明なコマンド」になる"
    slot モジュールが `config.yml` の `modules.slot.enabled: false` で無効化されている可能性があります。無効モジュールはコマンド・リスナーが一切登録されません。起動ログを確認し、`modules.slot.enabled` を `true` にして再起動してください。

??? failure "起動ログに slot モジュールの有効化失敗が出る"
    slot モジュールは起動時に EmeraldAPI（bank モジュール）が利用可能かを確認し、未準備なら `EmeraldAPI not bound; bank module required before slot` という例外で停止します。`modules.bank.enabled` が `true` になっているか確認してください。

??? failure "プレイヤーがスロットを利用できない"
    `/slot` コマンドはOP専用です。一般プレイヤーには `[Slot]` 看板または起動ロッドを用意してください。また、口座残高がマイナス（借金）のプレイヤーはスロットを利用できない仕様です。

??? failure "`/slot remote` が「権限がありません」になる"
    `/slot remote` は `slot.remote`（または旧 `slot2.remote`）を要求します。`slot.remote` は plugin.yml 上 `default: op` のため、一般プレイヤーに使わせる場合は権限プラグインで `slot.remote` を付与してください。

??? failure "配当や出現率を調整したい"
    `modules/slot.yml` の `symbols.<シンボル>` の `normal_weight`・`kakuhen_weight`・`multiplier` を編集し、サーバーを再起動してください。リール速度は `reel.spin_speed`、目押しの滑り具合は `reel.slip_frames`、確変回転数は `kakuhen.diamond_turns` で調整します。

??? failure "リールの挙動を詳しく確認したい"
    `slot.yml` の `debug` を `true` にすると、各リールの停止要求・滑り・現在表示シンボルなどの詳細ログがサーバーコンソールに出力されます。原因切り分けが終わったら本番では `false` に戻してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← スロット 概要へ](index.md){ .md-button }
