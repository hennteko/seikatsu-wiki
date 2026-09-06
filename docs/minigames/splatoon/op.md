<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# SplatoonPlugin ― OP・運営ガイド { .page-op #splatoon-op }

SplatoonPlugin の導入・フィールド設定・モード・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | SplatoonPlugin |
| バージョン | 2.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/splatoon`（対戦）・`/salmon`（サーモンラン） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/SplatoonPlugin/config.yml` |

!!! success "v2.0.0 の主な追加（ブキ刷新＆ガチマッチ＆サーモンラン）"
    メインブキ9種・サブ6種・スペシャル5種に拡張され、**ブキ選択GUI**（ブキ看板）で自由編成できるようになりました。さらに対戦モードが **ナワバリ／ガチエリア／ガチホコ／ガチヤグラ** の4種に増えています。会場・看板・モードはすべてコマンドまたは **チェスト型の管理GUI（`/splatoon book`・`/salmon book`）** で設定し、`config.yml` に自動保存されます。**複数の対戦会場（アリーナ）を登録して切替運用** でき（`/splatoon setarena`）、加えて **サーモンラン（PvE協力モード）** を新搭載し、`/salmon` コマンドと独立した看板・config（`salmon.*`）で運用します（複数ステージ＝アリーナを同時稼働可能）。

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
```text title="看板の登録を解除（視線先の参加/離脱/ブキ/開始看板を自動判別・1枚ずつ）"
/splatoon setsign delete
```

!!! success "看板は複数設置できます（v更新）"
    参加・離脱・ブキ選択・開始の各看板を **複数拠点に設置** できるようになりました（会場ごとに保持）。`setsign` は上書きではなく **追記** され、`setsign delete` は視線先の1枚だけ解除します。人数・状態表示は全枚数が同時に更新されます。既存の看板データは自動で引き継がれるため、**設定のやり直しは不要**です。開始看板はモード別（`turf`/`area`/`hoko`/`tower`）で、それぞれ複数設置できます。
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

## 複数会場（アリーナ）と管理GUI

v2.0.0 から **複数の対戦会場（アリーナ）を登録し、切り替えて運用** できます。スポーン・フィールド・ゾーン・ホコ・ゴール・ヤグラCP といった地点設定は **会場ごと** に保存され、`setfield`／`setspawn`／`setzone`／`sethoko`／`setgoal`／`settower` などはすべて **「現在会場」** に対して作用します。初期の会場IDは `default` です。

```text title="編集/対象の会場を切り替える（未登録なら新規作成）"
/splatoon setarena <会場名>
```
```text title="会場を削除する"
/splatoon delarena <会場名>
```
```text title="会場を指定して試合開始（会場を切り替えてから開始）"
/splatoon start <会場名> <turf|area|hoko|tower>
```

!!! note "現在会場という考え方"
    地点系コマンドは常に **現在会場** に保存されます。会場を増やすときは `setarena <会場名>` で切り替えてから各地点を設定してください。`/splatoon status` の先頭に **現在会場（編集/対象）** と **登録会場一覧** が表示されます。会場名を指定した `start <会場名> <モード>` は会場を切り替えてから、省略した `start <モード>` は現在会場で開始します。

### 管理GUI（`/splatoon book`・`/salmon book`）

コマンドを覚えなくても設定できるよう、**チェスト型の管理GUI** を用意しています。`/splatoon book` または `/salmon book` で共通メニューが開き、**PvP設定（会場一覧→会場編集）** と **サーモンラン設定（ステージ一覧→ステージ編集）** に分岐します。数値は左右クリックで増減（シフトで大きく）、地点は立ち位置で即セット、看板は視線先の看板に設定でき、変更は即 `config.yml` に保存されます。会場一覧では **シフト＋右クリックで会場を削除** できます。なお **新規会場／新規ステージはGUIからは作成できず**、初回だけコマンド（`/splatoon setarena <会場名>` ／ `/salmon setspawn <ステージ名>`）で作成すると一覧に現れます。

```text title="管理GUIを開く（PvP＋サーモンラン共通）"
/splatoon book
```
```text title="管理GUIを開く（/salmon からでも同じメニュー）"
/salmon book
```

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

座標系は **会場ごと** に `arenas.<会場ID>.*`（`spawn.*` / `field.*` / `zone1/2` / `hoko` / `goal.*` / `tower.checkpoints.*`）へ、現在会場IDは `arena-current` に、看板・ロビー等は各 `*-sign` / `lobby-spawn` / `default-spawn` へ、コマンド実行時に自動保存されます。旧バージョンの単一 `arena.*` 構造は起動時に自動で `arenas.default` へ移行されます（`config.yml` の手動編集は不要）。

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
| `/splatoon book` | チェスト型の管理GUIを開く（PvP＋サーモンラン共通） |
| `/splatoon setarena <会場名>` | 編集/対象の会場を切替（未登録なら新規作成） |
| `/splatoon delarena <会場名>` | 登録済みの会場を削除 |
| `/splatoon start <会場名> <turf\|area\|hoko\|tower>` | 会場を切り替えてから指定モードで試合開始 |
| `/splatoon stop` | 進行中の試合を強制終了 |
| `/splatoon setfield <1\|2>` | フィールド範囲の角を現在地に設定（現在会場） |
| `/splatoon setspawn <orange\|blue>` | チームの試合内スポーンを現在地に設定 |
| `/splatoon setlobby` / `setstartspawn` | ロビー / ゲーム外スポーンを現在地に設定 |
| `/splatoon setsign <join\|leave\|weapon>` | 視線先の看板を参加 / 離脱 / ブキ選択看板に設定 |
| `/splatoon setsign start <turf\|area\|hoko\|tower>` | 視線先の看板を **モード別の開始看板** に設定 |
| `/splatoon setsign delete` | 視線先の看板の登録を解除（参加 / 離脱 / ブキ / 開始を自動判別） |
| `/splatoon setmode <turf\|area\|hoko\|tower>` | 既定の対戦モードを設定 |
| `/splatoon setzone <1\|2> <1\|2>` | ガチエリアのゾーンを設定 |
| `/splatoon sethoko` / `setgoal <orange\|blue>` | ガチホコ中央 / ゴール台を設定（ゴール台はホコ専用） |
| `/splatoon settower checkpoint <orange\|blue> <番号>` | ガチヤグラの番号付きCPを設定（最大番号がゴール） |
| `/splatoon settower clear` | ガチヤグラの経路・関門を全消去 |
| `/splatoon setmin <人数>` | 最小参加人数（カウントダウン開始に必要な人数）を設定し `config.yml` に保存 |
| `/splatoon setmax <人数>` | 最大参加人数を設定し `config.yml` に保存 |
| `/splatoon settime <秒>` | 試合の制限時間（秒）を設定し `config.yml` に保存（次の試合から反映） |

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `splatoon.admin` | OP | 会場設定・看板設置・モード設定・stop などの管理コマンド |

!!! note "参加系コマンドは全員が使えます"
    `join`・`leave`・`status`・`start <モード>` は **権限不要で全プレイヤーが実行可能** です（看板と同等）。`splatoon.admin` が必要なのは会場設定・看板設置・モード設定・`stop` などの管理コマンドだけです。なお `/salmon` 系も同じ `splatoon.admin` 権限を共用します。

## サーモンラン（PvE協力モード）

`/salmon` コマンドで運用する **協力PvEモード** です。対戦（`/splatoon`）とは独立しており、**ステージ識別子ごとに1つのアリーナ** を定義して **複数ステージを同時稼働** できます。座標・看板・WAVE設定はすべて `/salmon` の各 set コマンドで `config.yml` の `salmon.*` 配下へ **即自動保存** されます。

!!! warning "対戦（PvP）と同時には動きません"
    サーモンランと通常対戦は戦闘登録（プレイヤー状態）を共有するため、**PvP対戦が `WAITING` 以外（進行中）のときはサーモンランを開始できません**。逆にサーモンラン稼働中のPvP開始も抑止されます。

### セットアップ手順（サーモンラン）

OP権限（`splatoon.admin`）で **その場に立って／対象を見て** 実行します。`<ステージ>` は任意の識別子（例: `stage1`）で、初回の set 実行時に自動作成されます。

```text title="ロビースポーン（参加時のTP先・共通）"
/salmon setlobby
```
```text title="初期スポーン（離脱・終了後の戻り先・共通）"
/salmon setstartspawn
```
```text title="基地スポーン（参加者の開始地点・ステージ別）"
/salmon setspawn <ステージ>
```
```text title="金イクラコンテナ（納品地点・ステージ別）"
/salmon setbasket <ステージ>
```
```text title="シャケ湧き地点を追加（複数登録可・ステージ別）"
/salmon setspawner <ステージ>
```
```text title="満潮フィールドの角1 / 角2（必須・ステージ別）"
/salmon setfield <ステージ> <1|2>
```
```text title="干潮フィールドの角1 / 角2（任意・未設定時は満潮を流用）"
/salmon settide <ステージ> <1|2>
```
```text title="潮汐で水位が変化する『水盤』領域の角1 / 角2（任意）"
/salmon settidearea <ステージ> <1|2>
```
```text title="満潮／干潮の水面Y（立ち位置のYを使用・任意）"
/salmon settidelevel <ステージ> <high|low>
```
```text title="WAVE数を設定（既定3）"
/salmon setwaves <ステージ> <数>
```
```text title="各WAVEのノルマを設定（既定 7/12/18）"
/salmon setquota <ステージ> <WAVE> <数>
```
```text title="WAVE制限時間を設定（秒・既定100）"
/salmon settime <ステージ> <秒>
```
```text title="参加看板を設定（共通・複数登録可）"
/salmon setsign join
```
```text title="離脱看板を設定（共通・複数登録可）"
/salmon setsign leave
```
```text title="開始看板を設定（ステージ別）"
/salmon setsign start <ステージ>
```
```text title="看板の登録を解除（視線先の参加/離脱/開始看板を自動判別）"
/salmon setsign delete
```
```text title="設定・進行状況を確認（ステージ名は任意）"
/salmon status [ステージ]
```

!!! tip "ステージ開始に必要な最小設定"
    あるステージで `/salmon start` できるのは **基地スポーン・金イクラコンテナ・湧き地点1つ以上・満潮フィールド** がすべて揃ったときです。干潮フィールドは任意（未設定なら満潮範囲を流用）。不足があると開始時に `/salmon status <ステージ>` で確認するよう案内されます。最低人数は1人（ロビーが0人だと開始できません）。

!!! note "潮汐（水位変化）は任意機能 ― `settide` と `settidearea`/`settidelevel` は別物"
    サーモンランでは各WAVE開始時に **満潮／干潮がランダムに切り替わり** ます。これに関する設定は2系統あり、目的が異なります。

    - **`settide`（干潮フィールド）** … 干潮WAVEで使う **プレイ範囲（湧き・塗り判定の直方体）** を満潮とは別に設定します。未設定なら満潮範囲を流用します。
    - **`settidearea` ＋ `settidelevel`（水位変化）** … 実際に **水が満ち引きする演出** を行う機能です。`settidearea 1/2` で **水盤（水位が上下する立方体）** を指定し、`settidelevel high`／`low` で **満潮／干潮それぞれの水面Y**（実行時の立ち位置のY）を登録します。**水盤領域の角2点と満潮Y・干潮Yの4つがすべて揃うと有効** になり、満潮WAVEでは水盤内の空気↔水を満潮Yまで満たし、干潮WAVEでは干潮Yまで水を引かせます（固形ブロックは変更しません）。水没した低い足場は危険地帯となり、**水面下では水死判定でダウン** することがあります。

    `settide` と `settidearea`/`settidelevel` は独立しており、片方だけ／両方の設定が可能です。いずれも任意で、未設定でもステージは動作します。

### サーモンランのコマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/salmon join` / `leave` | 全員 | ロビーへ参加 / 退出 |
| `/salmon start <ステージ>` | 全員 | ステージを開始（コマンドブロック / コンソール可） |
| `/salmon status [ステージ]` | 全員 | 設定・進行状況を確認 |
| `/salmon book` | `splatoon.admin` | チェスト型の管理GUIを開く（PvP＋サーモンラン共通） |
| `/salmon stop <ステージ>` | `splatoon.admin` | ステージを強制終了して `IDLE` に戻す |
| `/salmon setlobby` / `setstartspawn` | `splatoon.admin` | 共通スポーンを設定 |
| `/salmon setspawn <ステージ>` | `splatoon.admin` | 基地スポーンを設定 |
| `/salmon setbasket <ステージ>` | `splatoon.admin` | 金イクラコンテナを設定 |
| `/salmon setspawner <ステージ>` | `splatoon.admin` | シャケ湧き地点を追加 |
| `/salmon setfield <ステージ> <1\|2>` | `splatoon.admin` | 満潮フィールドの角を設定 |
| `/salmon settide <ステージ> <1\|2>` | `splatoon.admin` | 干潮フィールドの角を設定（任意） |
| `/salmon settidearea <ステージ> <1\|2>` | `splatoon.admin` | 潮汐で水位が変化する水盤領域の角を設定（任意） |
| `/salmon settidelevel <ステージ> <high\|low>` | `splatoon.admin` | 満潮／干潮の水面Y（立ち位置のY）を設定（任意） |
| `/salmon setwaves <ステージ> <数>` | `splatoon.admin` | WAVE数を設定 |
| `/salmon setquota <ステージ> <WAVE> <数>` | `splatoon.admin` | WAVEごとのノルマを設定 |
| `/salmon settime <ステージ> <秒>` | `splatoon.admin` | WAVE制限時間を設定 |
| `/salmon setsign <join\|leave\|start <ステージ>\|delete>` | `splatoon.admin` | 看板を設定 / 解除 |

!!! note "デバッグ用コマンド"
    `/salmon addegg <ステージ> [個数]`（納品カウントを直接加算）と `/salmon spawnegg <ステージ>`（足元に金イクラ実体を生成）は **動作検証用のデバッグコマンド** です。いずれも進行中ステージでのみ機能し、通常運用では使いません。

### config.yml（`salmon.*` 主要項目）

座標・看板・各ステージ設定（`salmon.lobby-spawn` / `salmon.default-spawn` / `salmon.join-signs` / `salmon.leave-signs` / `salmon.stages.<識別子>.*`）はコマンド実行時に自動保存されます。以下は **全ステージ共通の調整パラメータ**（コードに既定値あり・未記載でも動作）です。

| キー | 既定値 | 説明 |
|---|---|---|
| `salmon.ink-damage` | 4.0 | インク弾1ヒットがシャケに与えるダメージ |
| `salmon.zako.live-cap` | 12 | 雑魚シャケの同時存在上限 |
| `salmon.zako.per-second` | 2 | 1秒あたりの雑魚湧き数 |
| `salmon.zako.per-wave-base` | 12 | WAVE1の総湧き数の基準 |
| `salmon.zako.per-wave-step` | 6 | WAVEが進むごとの総湧き増加量 |
| `salmon.zako.golden-egg-chance` | 0.0 | 雑魚撃破時の金イクラ落下確率（本来の供給源はオオモノ） |
| `salmon.downed-seconds` | 12 | ダウンから戦線離脱までの猶予（秒） |
| `salmon.revive-radius` | 2.0 | 味方が近づくと救助できる半径 |
| `salmon.golden-egg.carry-slowness` | 1 | 金イクラ運搬中の鈍足レベル |
| `salmon.golden-egg.basket-radius` | 3.0 | コンテナ納品判定の半径 |
| `salmon.boss.per-wave` | 2 | WAVEごとのオオモノ出現数 |
| `salmon.boss.spawn-interval` | 8 | オオモノの出現間隔（秒） |
| `salmon.boss.reward-eggs` | 3 | オオモノ撃破時に出現する金イクラ数 |
| `salmon.boss.steelhead.hp` / `bomb-interval` / `bomb-radius` / `bomb-damage` | 60 / 5 / 3.0 / 4.0 | バクダン：弱点HP・爆撃間隔・範囲・ダメージ |
| `salmon.boss.scrapper.hp` | 50 | テッパン：背面弱点HP |
| `salmon.boss.stinger.segments` / `segment-hp` / `shot-interval` / `shot-damage` | 4 / 10 / 4 / 3.0 | タワー：段数・段HP・狙撃間隔・ダメージ |

各ステージ定義は `salmon.stages.<識別子>` 配下に `base-spawn` / `basket` / `enemy-spawners` / `field.high.min|max` / `field.low.min|max` / `waves` / `quota` / `time` / `start-sign` / `tide.area.min|max` / `tide.high-y` / `tide.low-y` として保存されます（既定: WAVE数3・ノルマ`[7,12,18]`・時間100秒）。`tide.*`（水位変化）は水盤2点と満潮Y・干潮Yの4つが揃ったときのみ有効になる任意設定です。

!!! warning "既存サーバーを更新する場合（config自動マージなし）"
    対戦側と同様、本プラグインは `saveDefaultConfig()` のみのため、**既存の `config.yml` に `salmon.*` の新しい調整キーを自動追記しません**。コード側に既定値があるため未記載でも既定値で動作します。雑魚湧きやオオモノを調整したい場合は配布 `config.yml` を参照して手動追記してください。座標・看板・ステージ設定はコマンドで都度保存されるため問題ありません。

## トラブルシューティング

??? failure "ガチマッチなのにナワバリになる"
    モードの必須地点が未設定だとナワバリにフォールバックします。`/splatoon status` で、ガチエリアは `setzone`（2ゾーン×2角）、ガチホコは `sethoko`＋`setgoal` 両方、ガチヤグラは `settower checkpoint orange/blue` が両チームに最低1個ずつ設定済みか確認してください（ガチヤグラに `setgoal` は使いません）。

??? failure "床が塗れない / 一部しか塗れない"
    塗れる床は決まったマテリアルのみです（上記「フィールドと塗れる床」を参照）。対象外の素材で床を作ると塗れません。フィールド範囲（`setfield` の2点）外も塗り判定の対象外です。

??? failure "プレイヤーがブキを選べない"
    `/splatoon setsign weapon` でブキ選択看板を設置してください。看板クリックでGUIが開き、メイン9種・サブ6種・スペシャル5種を個別に選べます。選択は試合をまたいで保持されますが、**サーバー再起動でリセット**されます（永続保存はされません）。

??? failure "試合が始まらない"
    ロビーに `game.min-players`（既定2人）以上が必要です。`/splatoon start` での強制開始も同じ最小人数が必要です。

??? failure "config.yml の game の値を変えても反映されない"
    `game` セクションは起動時に読み込まれます。変更後はサーバーを再起動してください。既存サーバーでは新しいモード別キーが自動追記されないため、調整したいキーは手動追記が必要です。

??? failure "試合終了後にフィールドが元に戻らない"
    フィールドは試合開始時のスナップショットから自動復元されます。サーバー停止などで中断された場合は復元されないことがあります。`/splatoon stop` で強制終了すると復元が実行されます。

??? failure "サーモンランが開始できない / シャケが湧かない"
    `/salmon status <ステージ>` で **基地スポーン・金イクラコンテナ・湧き地点（1つ以上）・満潮フィールド** が「設定済み」か確認してください。いずれかが未設定だと開始できません。また **PvP対戦が進行中（`WAITING` 以外）だとサーモンランは開始できません**。ロビーに最低1人いる必要もあります。湧き地点は `/salmon setspawner <ステージ>` で複数登録できます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← SplatoonPlugin 概要へ](index.md){ .md-button }
