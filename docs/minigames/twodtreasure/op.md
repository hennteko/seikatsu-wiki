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
4. ロビーに参加看板・退出看板を設置する。
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

`spawn` / `lobby` / `area.pos1` / `area.pos2` の4地点を保持します。これらは後述の `/2dtreasure setup` コマンドで設定され、自動的に `config.yml` に保存されます。手動で書き換える必要はありません。

!!! note "設定変更後は `/2dtreasure reload`"
    `config.yml` を編集したら `/2dtreasure reload` で再読み込みしてください。ただしゲーム進行中はリロードできません（待機中のみ可）。

## セットアップ手順

ゲーム用ワールドを用意し、OP権限で以下のコマンドを **設定したい場所に立って** 実行します（実行位置の座標が保存されます）。

```text
/2dtreasure setup spawn          # 初期リスポーン地点（途中抜け時の戻り先）
/2dtreasure setup lobby          # ロビー地点（参加者の集合場所）
/2dtreasure setup area pos1      # ゲームエリアの角1
/2dtreasure setup area pos2      # ゲームエリアの角2
```

- `area pos1` と `area pos2` の2点で囲んだX軸の範囲が、マップが生成されるプレイエリアになります。
- 4地点をすべて設定すると、コマンド実行時に「✓ 全ての設定が完了しています！」と表示されます。
- 設定状況は `/2dtreasure status` でいつでも確認できます。

!!! tip "ゲームエリアはX軸の幅で決まります"
    マップ生成はエリアの最小X〜最大X、固定Z（`fixed-z`）、Y軸（`y-min`〜`y-max`）の範囲で行われます。pos1・pos2 のX座標が十分に離れた範囲を選んでください。

## 看板の設置

参加・退出はロビーの看板で行います。看板の **1行目に `[2DT]`** を入力し、**2行目** に種別を入力して設置すると自動でフォーマットされます。看板の設置には `twodtreasure.admin` 権限が必要です。

| 種別 | 1行目 | 2行目 | 用途 |
|---|---|---|---|
| 参加看板 | `[2DT]` | `lobby` | クリックでロビーに参加。参加人数（`0/最大人数`）が表示される |
| 退出看板 | `[2DT]` | `leave` | クリックでロビー／ゲームから離脱 |

!!! note "参加看板の人数表示"
    参加看板には現在のロビー参加人数が `現在/最大人数` の形式で表示され、参加・退出に応じて自動更新されます。最大人数は `config.yml` の `game.max-players` の値です。

## 管理コマンド

メインコマンドは `/2dtreasure`（エイリアス `/2dt`・`/treasure2d`）です。`start` はコマンドブロックやコンソールからも実行できます。

| コマンド | 説明 |
|---|---|
| `/2dtreasure setup <spawn\|lobby\|area>` | 地点を設定する（area は `pos1`/`pos2` を指定） |
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
    参加には `twodtreasure.play` 権限（既定で全員付与）が必要です。また、ゲーム進行中は参加できません。看板が `[2DT]` ＋ `lobby` で正しく設置されているか確認してください。

??? failure "看板を設置しても自動フォーマットされない"
    看板の設置には `twodtreasure.admin` 権限が必要です。権限がない場合は設置自体がキャンセルされます。1行目が `[2DT]`、2行目が `lobby` または `leave` になっているか確認してください。

??? failure "ゲーム中に設定をリロードできない"
    `/2dtreasure reload` はゲーム待機中（WAITING）のみ実行できます。ゲーム進行中は `/2dtreasure stop` で終了してからリロードしてください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← TwoDTreasure 概要へ](index.md){ .md-button }
