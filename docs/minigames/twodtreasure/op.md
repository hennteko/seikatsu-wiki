<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# TwoDTreasure ― OP・運営ガイド { .page-op #twodtreasure-op }

TwoDTreasure の導入・地点設定・config・看板・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | TwoDTreasure |
| バージョン | 1.1 |
| api-version | 1.26 |
| メインコマンド | `/2dtreasure`（エイリアス `/2dt`・`/treasure2d`） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/TwoDTreasure/config.yml` |

## 導入手順

1. ビルドした `TwoDTreasure-1.1.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/TwoDTreasure/config.yml` が自動生成される。
3. 後述の手順でゲーム用ワールドに地点（spawn・lobby・area）を設定する。
4. ロビーに参加看板・退出看板を設置し、`/2dtreasure setsign` で登録する（後述）。
5. 設定を変更したら `/2dtreasure reload` で再読み込みする。

!!! warning "地点設定が未完了だと開始できません"
    spawn・lobby・area pos1・area pos2 の4地点がすべて設定されていないとゲームを開始できません。プラグイン有効化時のログにも設定状態が出力されます。

## config.yml 設定項目

`config.yml` の各キーは下表の通りです。地点（`locations`）はゲーム内コマンドで設定するため、手動編集は不要です。

### ゲーム設定（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 2 | ゲーム開始に必要な最低人数 |
| `max-players` | 10 | 最大参加人数（参加看板の表示用） |
| `countdown-seconds` | 20 | マップ確認タイムの秒数 |
| `ending-seconds` | 20 | ゲーム終了後、ロビーに戻るまでの待機秒数 |

### 2D地形設定（`terrain`）

| キー | 既定値 | 説明 |
|---|---|---|
| `fixed-z` | 0 | 2D平面のZ座標（プレイ面を固定する1マス） |
| `y-ground-level` | 80 | 地面の高さ |
| `y-min` | -64 | マップ生成の最小Y座標 |
| `y-max` | 320 | マップ生成の最大Y座標 |

### 地点設定（`locations`）

`spawn` / `lobby` / `area.pos1` / `area.pos2` の4地点を保持します。これらは後述の `/2dtreasure setstartspawn` / `setlobby` / `setfield` コマンドで設定され、自動的に `config.yml` に保存されます。手動で書き換える必要はありません。

!!! note "設定変更後は `/2dtreasure reload`"
    `config.yml` を編集したら `/2dtreasure reload` で再読み込みしてください。ただしゲーム進行中はリロードできません（待機中のみ可）。

## セットアップ手順

ゲーム用ワールドを用意し、OP権限で以下のコマンドを **設定したい場所に立って** 実行します（実行位置の座標が保存されます）。

```text title="初期リスポーン地点（途中抜け時の戻り先）"
/2dtreasure setstartspawn
```

```text title="ロビー地点（参加者の集合場所）"
/2dtreasure setlobby
```

```text title="ゲームエリアの角1"
/2dtreasure setfield 1
```

```text title="ゲームエリアの角2"
/2dtreasure setfield 2
```

- `setfield 1` と `setfield 2` の2点で囲んだX軸の範囲が、マップが生成されるプレイエリアになります。
- 4地点をすべて設定すると、コマンド実行時に「✓ 全ての設定が完了しています！」と表示されます。
- 設定状況は `/2dtreasure status` でいつでも確認できます。

!!! tip "ゲームエリアはX軸の幅で決まります"
    マップ生成はエリアの最小X〜最大X、固定Z（`fixed-z`）、Y軸（`y-min`〜`y-max`）の範囲で行われます。`setfield 1`・`setfield 2` のX座標が十分に離れた範囲を選んでください。

## 看板の登録

参加・退出はロビーの看板で行います。看板を設置し、**看板を見ながら（5ブロック以内）** 以下のコマンドで登録します（`twodtreasure.admin` 権限が必要）。

```text title="参加看板として登録"
/2dtreasure setsign join
```

```text title="退出看板として登録"
/2dtreasure setsign leave
```

```text title="開始看板として登録（任意・右クリックでゲーム開始）"
/2dtreasure setstart
```

登録するとプラグインが看板テキストを自動で書き込み、位置が config.yml の `signs.join` / `signs.leave` に保存されます（再起動後も有効）。手書きテキスト（`[2DT]`）による登録は廃止されています。

| 種別 | 表示（自動書き込み） | 用途 |
|---|---|---|
| 参加看板 | `[2DT]` ／ `lobby` ／ `クリックで参加` ／ 参加人数 | クリックでロビーに参加。参加人数（`現在/最大人数`）が表示される |
| 退出看板 | `[2DT]` ／ `leave` ／ `クリックで離脱` | クリックでロビー／ゲームから離脱 |

!!! note "参加看板の人数表示"
    参加看板には現在のロビー参加人数が `現在/最大人数` の形式で表示され、参加・退出に応じて自動更新されます。最大人数は `config.yml` の `game.max-players` の値です。

## 管理コマンド

メインコマンドは `/2dtreasure`（エイリアス `/2dt`・`/treasure2d`）です。`start` はコマンドブロックやコンソールからも実行できます。

| コマンド | 説明 |
|---|---|
| `/2dtreasure setstartspawn` | 初期リスポーン地点を設定する |
| `/2dtreasure setlobby` | ロビー地点を設定する |
| `/2dtreasure setfield <1\|2>` | ゲームエリアの角1／角2を設定する |
| `/2dtreasure setsign <join\|leave>` | 視線先の看板を参加／退出看板として登録する |
| `/2dtreasure setstart` | 視線先の看板をゲーム開始看板として登録する（右クリックで試合開始） |
| `/2dtreasure start` | ゲームを開始する（コマンドブロック対応） |
| `/2dtreasure stop` | 進行中のゲームを強制終了する |
| `/2dtreasure reload` | config.yml を再読み込みする（待機中のみ可） |
| `/2dtreasure status` | ゲーム状態・参加人数・地点設定状況を表示する |

!!! tip "コマンドブロックでの自動開始"
    `/2dtreasure start` はコマンドブロックからも実行できます。ロビーのボタンや感圧板と連動させることで、運営がいなくてもプレイヤー主導でゲームを始められます。

## 権限ノード

`plugin.yml` で定義されている権限は以下の2つです。

| 権限 | 既定 | 用途 |
|---|---|---|
| `twodtreasure.admin` | OP | 管理コマンド（`/2dtreasure ...`）の使用、看板の設置 |
| `twodtreasure.play` | 全員 | ゲームに参加する（参加看板のクリック） |

## トラブルシューティング

??? failure "ゲームが開始できない"
    考えられる原因は次の通りです。

    - **地点設定が未完了** — spawn・lobby・area pos1・area pos2 の4地点をすべて設定してください。`/2dtreasure status` で確認できます。
    - **人数不足** — ロビー参加者が `game.min-players`（既定2人）に達していません。
    - **既にゲームが進行中** — 進行中は新たに開始できません。

??? failure "プレイヤーが参加看板をクリックしても参加できない"
    参加には `twodtreasure.play` 権限（既定で全員付与）が必要です。また、ゲーム進行中は参加できません。看板が `/2dtreasure setsign join` で登録済みか確認してください（手書きの看板は機能しません）。

??? failure "看板が反応しない／フォーマットされない"
    看板は `/2dtreasure setsign <join|leave>` での登録が必要です（`twodtreasure.admin` 権限）。登録したい看板を5ブロック以内で見ながら実行してください。手書きテキスト（`[2DT]`）による自動登録は廃止されています。

??? failure "ゲーム中に設定をリロードできない"
    `/2dtreasure reload` はゲーム待機中（WAITING）のみ実行できます。ゲーム進行中は `/2dtreasure stop` で終了してからリロードしてください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← TwoDTreasure 概要へ](index.md){ .md-button }
