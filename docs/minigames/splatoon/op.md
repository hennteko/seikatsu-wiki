<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# SplatoonPlugin ― OP・運営ガイド { .page-op #splatoon-op }

SplatoonPlugin の導入・フィールド設定・モード・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | SplatoonPlugin |
| バージョン | 2.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/splatoon` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/SplatoonPlugin/config.yml` |

!!! success "v2.0.0 の主な追加（ブキ刷新＆ガチマッチ）"
    メインブキ9種・サブ5種・スペシャル5種に拡張され、**ブキ選択GUI**（ブキ看板）で自由編成できるようになりました。さらに対戦モードが **ナワバリ／ガチエリア／ガチホコ／ガチヤグラ** の4種に増えています。会場・看板・モードはすべてコマンドで設定し、`config.yml` に自動保存されます。

## 導入手順

1. ビルドした `SplatoonPlugin-2.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/SplatoonPlugin/config.yml` が自動生成される。
3. 後述の「セットアップ手順」でフィールド・スポーン・看板・モードを設定する。
4. `/splatoon status` でいつでも設定状態を確認できる。

## セットアップ手順

専用フィールドを用意し、OP権限（`splatoon.admin`）で以下を **その場に立って／対象を見て** 実行します。座標は自動で `config.yml` に保存されます。

```text title="フィールド範囲の角1 / 角2（塗り判定の範囲）"
/splatoon setfield 1
```
```text title="フィールド範囲の角2"
/splatoon setfield 2
```
```text title="オレンジチームの試合内スポーン"
/splatoon setspawn orange
```
```text title="ブルーチームの試合内スポーン"
/splatoon setspawn blue
```
```text title="参加看板を設定（5ブロック以内の看板を見て実行）"
/splatoon setsign join
```
```text title="離脱看板を設定"
/splatoon setsign leave
```
```text title="ブキ選択看板を設定（クリックで編成GUIを開く）"
/splatoon setsign weapon
```
```text title="開始看板を設定（モード別。クリックでそのモードの試合開始）"
/splatoon setsign start <turf|area|hoko|tower>
```
```text title="ロビーのスポーン地点（任意・試合終了後の戻り先）"
/splatoon setlobby
```
```text title="ゲーム外スポーン地点（任意・ログアウト後の戻り先）"
/splatoon setstartspawn
```
```text title="設定状態を確認"
/splatoon status
```

!!! tip "フィールドと塗れる床"
    `setfield 1`/`2` の2点で囲んだ直方体が塗り判定の範囲です。**塗れる床は限定されています**（草・土・石・白/薄灰/灰コンクリート・白コンクリートパウダー・テラコッタ・白テラコッタ・砂・砂利・オーク/トウヒの板材・オレンジ/青コンクリート）。これ以外（純色の羊毛・ガラス・水など）や、真上が空気でないブロックは塗れません。塗ると床は一時的にチームカラーのコンクリートへ置換され、試合終了時に元の状態へ復元されます。

## 対戦モードの設定

`/splatoon setmode <turf|area|hoko|tower>` でモードを切り替えます（`config.yml` に保存）。各モードで追加の必須地点があります。

```text title="モードを設定（ナワバリ/ガチエリア/ガチホコ/ガチヤグラ）"
/splatoon setmode <turf|area|hoko|tower>
```

| モード | 値 | 勝敗ルール | 追加の必須セットアップ |
|---|---|---|---|
| ナワバリ | `turf` | 制限時間終了時、塗った床の合計のうち自チーム塗り割合が高い方が勝ち | なし（フィールド＋スポーンのみ） |
| ガチエリア | `area` | 2つのゾーンを70%超で確保。両方を同時に確保している間だけカウントが減り、先に0でノックアウト | `setzone` でゾーン2つ×各2角 |
| ガチホコ | `hoko` | 中央のホコを敵ゴール台へ運び到達でノックアウト。カウントは最接近距離で進む | `sethoko`＋`setgoal orange`＋`setgoal blue` |
| ガチヤグラ | `tower` | 各チームの番号付きCPを順に進み、自チームの最大番号CP（＝ゴール）に到達でノックアウト。スタートは両チームCP1の中点 | `settower checkpoint orange <番号>`＋`settower checkpoint blue <番号>`（**setgoalは使わない**） |

```text title="ガチエリアのゾーン設定（ゾーン番号1/2 と 角1/2）"
/splatoon setzone <1|2> <1|2>
```
```text title="ガチホコの中央地点を設定"
/splatoon sethoko
```
```text title="ガチホコのゴール台を設定（両チーム分・ホコ専用）"
/splatoon setgoal <orange|blue>
```
```text title="ガチヤグラのチェックポイントを番号付きで設定（最大番号がゴール）"
/splatoon settower checkpoint <orange|blue> <番号>
```
```text title="ガチヤグラの経路・関門をすべて消去"
/splatoon settower clear
```

!!! warning "モード必須地点が未設定だとナワバリ進行になります"
    ガチエリア/ガチホコ/ガチヤグラで必須地点が未設定の場合、試合開始時に警告を出した上で **ナワバリ判定にフォールバック** して進行します（試合は止まりません）。`/splatoon status` で各モードの設定状況を必ず確認してください。なおガチホコでは **オレンジのゴール台は青チームの目標、青のゴール台はオレンジの目標** です。

!!! note "ガチヤグラのチェックポイント設定（番号付き）"
    ガチヤグラは `settower checkpoint <orange|blue> <番号>` で **各チームのCPを番号付きで設置** します。**最大番号のCPがそのチームのゴール**、番号1〜（最大-1）が **中間関門**（通過時に一時停止・既定5秒）です。**スタート(中央)はオレンジCP1と青CP1の中点** になります。両チームに最低1個ずつCPがあれば動作し、`setgoal` は使いません（ホコ専用）。`settower clear` で全消去します。`add` サブコマンドは廃止されました。

## config.yml 主要項目

座標・看板・モードはコマンドで自動保存されるため手動編集は不要です。数値パラメータの調整時のみ編集します。

| キー | 既定値 | 説明 |
|---|---|---|
| `game.min-players` | 2 | ロビーカウントダウン開始に必要な人数 |
| `game.max-players` | 8 | 1試合の最大人数 |
| `game.game-seconds` | 180 | 試合時間（秒） |
| `game.lobby-seconds` | 30 | 開始前カウントダウン（秒） |
| `game.mode` | `turf` | 既定モード（`setmode` で上書き保存） |
| `game.area.count` / `zone-threshold` | 100 / 0.70 | ガチエリアのカウント・確保しきい値（70%） |
| `game.hoko.count` / `goal-radius` 等 | 100 / 2.0 | ガチホコのカウント・ゴール半径ほか（`hold-seconds` はconfigに残るが**保持超過の自爆は現在無効**） |
| `game.tower.count` / `occupy-radius` / `advance-seconds` 等 | 100 / 3.0 / 25 | ガチヤグラのカウント・占有半径・前進/後退秒ほか |

座標系（`arena.spawn.*` / `arena.field.*` / `arena.zone1/2` / `arena.hoko` / `arena.goal.*` / `arena.tower.*` / 各 `*-sign` / `lobby-spawn` / `default-spawn`）はコマンド実行時に自動保存されます。

!!! warning "既存サーバーを更新する場合（config自動マージなし）"
    本プラグインは `saveDefaultConfig()` のみで、**既存の `config.yml` に新しいキー（`game.area` / `game.hoko` / `game.tower` など）を自動追記しません**。コード側に既定値があるため未記載でも既定値で動作しますが、**各モードのカウントや時間を調整したい場合は配布 `config.yml` を参照して手動で追記**してください。座標・看板・モードはコマンドで都度保存されるため問題ありません。

## コマンド

### プレイヤー用（全員可）

| コマンド | 説明 |
|---|---|
| `/splatoon join` | 試合（ロビー）に参加する |
| `/splatoon leave` | 試合（ロビー）から離脱する |
| `/splatoon status` | 自分のゲーム状態を確認する |
| `/splatoon start <turf\|area\|hoko\|tower>` | 指定モードでモードを設定して試合開始（全員可・コマンドブロック/コンソール対応） |

!!! note "join / leave / status / start は権限不要"
    これらは全プレイヤーが使えます（看板と同等）。`start <モード>` は **そのモードに設定してから開始** します。運営のみで開始したい場合は開始看板を置かず運用してください。

### 管理用（`splatoon.admin`）

| コマンド | 説明 |
|---|---|
| `/splatoon stop` | 進行中の試合を強制終了 |
| `/splatoon setfield <1\|2>` | フィールド範囲の角を現在地に設定 |
| `/splatoon setspawn <orange\|blue>` | チームの試合内スポーンを現在地に設定 |
| `/splatoon setlobby` / `setstartspawn` | ロビー / ゲーム外スポーンを現在地に設定 |
| `/splatoon setsign <join\|leave\|weapon>` | 視線先の看板を参加 / 離脱 / ブキ選択看板に設定 |
| `/splatoon setsign start <turf\|area\|hoko\|tower>` | 視線先の看板を **モード別の開始看板** に設定 |
| `/splatoon setmode <turf\|area\|hoko\|tower>` | 既定の対戦モードを設定 |
| `/splatoon setzone <1\|2> <1\|2>` | ガチエリアのゾーンを設定 |
| `/splatoon sethoko` / `setgoal <orange\|blue>` | ガチホコ中央 / ゴール台を設定（ゴール台はホコ専用） |
| `/splatoon settower checkpoint <orange\|blue> <番号>` | ガチヤグラの番号付きCPを設定（最大番号がゴール） |
| `/splatoon settower clear` | ガチヤグラの経路・関門を全消去 |

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `splatoon.admin` | OP | 会場設定・看板設置・モード設定・stop などの管理コマンド |

!!! note "参加系コマンドは全員が使えます"
    `join`・`leave`・`status`・`start <モード>` は **権限不要で全プレイヤーが実行可能** です（看板と同等）。`splatoon.admin` が必要なのは会場設定・看板設置・モード設定・`stop` などの管理コマンドだけです。

## トラブルシューティング

??? failure "ガチマッチなのにナワバリになる"
    モードの必須地点が未設定だとナワバリにフォールバックします。`/splatoon status` で、ガチエリアは `setzone`（2ゾーン×2角）、ガチホコは `sethoko`＋`setgoal` 両方、ガチヤグラは `setgoal` 両端が設定済みか確認してください。

??? failure "床が塗れない / 一部しか塗れない"
    塗れる床は決まったマテリアルのみです（上記「フィールドと塗れる床」を参照）。対象外の素材で床を作ると塗れません。フィールド範囲（`setfield` の2点）外も塗り判定の対象外です。

??? failure "プレイヤーがブキを選べない"
    `/splatoon setsign weapon` でブキ選択看板を設置してください。看板クリックでGUIが開き、メイン9種・サブ5種・スペシャル5種を個別に選べます。選択は試合をまたいで保持されますが、**サーバー再起動でリセット**されます（永続保存はされません）。

??? failure "試合が始まらない"
    ロビーに `game.min-players`（既定2人）以上が必要です。`/splatoon start` での強制開始も同じ最小人数が必要です。

??? failure "config.yml の game の値を変えても反映されない"
    `game` セクションは起動時に読み込まれます。変更後はサーバーを再起動してください。既存サーバーでは新しいモード別キーが自動追記されないため、調整したいキーは手動追記が必要です。

??? failure "試合終了後にフィールドが元に戻らない"
    フィールドは試合開始時のスナップショットから自動復元されます。サーバー停止などで中断された場合は復元されないことがあります。`/splatoon stop` で強制終了すると復元が実行されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← SplatoonPlugin 概要へ](index.md){ .md-button }
