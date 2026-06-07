<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Tntran ― OP・運営ガイド { .page-op #tntran-op }

Tntran の導入・エリアのセットアップ・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Tntran |
| バージョン | 1.1 |
| api-version | 26.1.2 |
| メインコマンド | `/tntran` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Tntran/config.yml` |

## 導入手順

1. ビルドした `Tntran` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Tntran/config.yml` が自動生成される。
3. 後述の手順でゲームエリア・ロビー・スポーン地点の座標を設定する。
4. ロビーに参加用・退出用の看板を設置する。
5. config.yml を変更したら `/tntran reload` で再読み込みする。

## config.yml 設定項目

| キー | 既定値 | 説明 |
|---|---|---|
| `decay-delay-ticks` | 20 | 足場に乗ってからブロックが消滅するまでの時間（tick単位、20 tick = 1秒） |
| `max-players` | 6 | 最大参加人数 |
| `locations.spawn` | 未設定 | 初期リスポーン地点。途中抜け時の戻り先。`/tntran setstartspawn` で設定 |
| `locations.lobby` | 未設定 | 待機ロビー（看板設置場所）。`/tntran setlobby` で設定 |
| `locations.area.pos1` | 未設定 | ゲームエリアの角1。`/tntran setfield 1` で設定 |
| `locations.area.pos2` | 未設定 | ゲームエリアの角2。`/tntran setfield 2` で設定 |
| `signs.join` / `signs.leave` / `signs.start` | `[]` | 参加・退出・開始看板の位置リスト。`/tntran setsign <join\|leave>`・`/tntran setstart` で自動保存 |

!!! note "座標はコマンドで設定"
    `locations` 配下の座標は手書きせず、後述の各設定コマンドで設定するのが確実です。実行したプレイヤーの立ち位置が保存されます。

## セットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **設定したい場所に立って** 実行します（実行位置が座標として保存されます）。

```text title="初期リスポーン地点（途中抜け時の戻り先）"
/tntran setstartspawn
```

```text title="待機ロビー"
/tntran setlobby
```

```text title="ゲームエリアの角1"
/tntran setfield 1
```

```text title="ゲームエリアの角2"
/tntran setfield 2
```

!!! tip "ゲームエリア（field）の作り方"
    `setfield 1`〜`setfield 2` で囲んだ直方体範囲が、ゲーム開始時に **マップ（ウール足場）が自動生成される領域** になります。pos1 と pos2 の Y 座標の差をもとに、下から **5ブロック間隔・最大6層** の足場が生成されます（赤・橙・黄・黄緑・緑・水色のウール）。十分な高さ・広さを確保してください。

### ロビー看板の設置

ロビーには参加・退出・開始用の看板をコマンドで登録します。看板を見ながら（5ブロック以内）以下のコマンドを実行してください。

| コマンド | 役割 |
|---|---|
| `/tntran setsign join` | 参加看板を登録。クリックでゲームに参加・ロビーへTP。参加人数を表示 |
| `/tntran setsign leave` | 退出看板を登録。クリックでゲームから離脱 |
| `/tntran setstart` | **開始看板** を登録（表示: `[Tntran]`／`start`／`▶クリックで開始`）。クリックでゲーム開始 |

看板のテキストはプラグインが自動で書き込みます。登録を解除するには看板を見ながら `/tntran removesign` を実行してください。

!!! tip "開始看板はプレイヤーも押せます"
    開始看板のクリックに権限は不要です。最低人数などの開始条件チェックは看板経由でも行われるため、条件を満たしていればプレイヤー主導でゲームを始められます。

!!! note "手書き登録は廃止済み"
    以前の方式（1行目に `[Tntran]`、2行目に `lobby`/`leave` を手書きして認識させる方式）は廃止されています。必ずコマンドで登録してください。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/tntran join [対象]` | なし（全員） | ゲームに参加する |
| `/tntran leave [対象]` | なし（全員） | ゲームから離脱する |
| `/tntran start` | `tntran.admin` | ゲームを開始する（マップ生成・5秒カウントダウン後にスタート） |
| `/tntran stop` | `tntran.admin` | ゲームを強制停止・リセットする |
| `/tntran setstartspawn` | `tntran.admin` | 初期リスポーン地点を設定する |
| `/tntran setlobby` | `tntran.admin` | 待機ロビーを設定する |
| `/tntran setfield <1\|2>` | `tntran.admin` | ゲームエリアの角1または角2を設定する |
| `/tntran setsign <join\|leave>` | `tntran.admin` | 視線先の看板を参加／退出看板として登録する |
| `/tntran setstart` | `tntran.admin` | 視線先の看板をゲーム開始看板として登録する |
| `/tntran removesign` | `tntran.admin` | 視線先の看板の登録を解除する |
| `/tntran reload` | `tntran.admin` | config.yml を再読み込みする |

!!! note "ゲームの運営"
    プレイヤーがロビー看板から参加するのを待ち、十分集まったら `/tntran start` または **開始看板のクリック** で開始します。自動開始の機能はありません。異常時は `/tntran stop` で強制終了・リセットできます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `tntran.admin` | OP | 管理コマンド（start / stop / setstartspawn / setlobby / setfield / setsign / setstart / removesign / reload）を使用できる |

## トラブルシューティング

??? failure "ゲームが開始できない（エリア未設定エラー）"
    `/tntran start` 実行時に「ゲームエリアが設定されていません」と出る場合、角1・角2が両方設定されていません。`/tntran setfield 1` / `/tntran setfield 2` を専用ワールドで実行してください。両方のワールドが読み込まれている必要もあります。

??? failure "参加看板をクリックしても参加できない"
    `/tntran setsign join` で正しく登録されているか確認してください（座標一致で判定するため手書きは機能しません）。また、ゲームエリア（`setfield 1` / `setfield 2`）が未設定だと参加できません。ゲーム進行中・満員時も参加不可です。

??? failure "足場が生成されない／層が少ない"
    足場は `area` の Y 座標の範囲をもとに **5ブロック間隔で最大6層** 生成されます。pos1 と pos2 の高低差が小さいと層数が減ります。十分な高さの範囲を指定してください。

??? failure "config の変更が反映されない"
    `config.yml` を編集したら `/tntran reload` を実行してください。`decay-delay-ticks` や `max-players`、座標設定が再読み込みされます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Tntran 概要へ](index.md){ .md-button }
