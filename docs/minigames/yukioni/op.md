<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Yukioni ― OP・運営ガイド { .page-op #yukioni-op }

Yukioni の導入・初期設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Yukioni |
| 説明 | 雪鬼ごっこミニゲームプラグイン |
| api-version | 26.1.2 |
| メインコマンド | `/yukioni <setup\|start\|stop\|reload>` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Yukioni/config.yml` |
| 座標保存ファイル | `plugins/Yukioni/locations.yml`（自動生成・自動保存） |

## 導入手順

1. ビルドした `Yukioni` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Yukioni/config.yml` が自動生成される。
3. 後述の手順でスポーン・ロビー・ゲームエリアの座標を設定する。
4. ロビーに参加看板・離脱看板を設置する。
5. 設定を変更したら `/yukioni reload` で再読み込みする。

## config.yml 設定項目

### ゲーム設定（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `match-duration` | 420 | 試合時間（秒）。経過で市民の勝利 |
| `min-players` | 2 | ゲーム開始に必要な最小プレイヤー数 |
| `max-players` | 16 | ロビーの最大プレイヤー数 |
| `oni-freeze-time` | 10 | 開始直後に鬼が動けない時間（秒） |
| `snowblock-cooldown` | 5 | 鬼の雪ブロック投げクールダウン（秒） |
| `countdown-time` | 10 | ゲーム開始前のカウントダウン時間（秒） |
| `citizen-speed` | 0.3 | 市民の移動速度倍率（通常0.2、0.3で約1.5倍） |

### メッセージ設定（`messages`）

`prefix` のほか、ゲーム開始・勝敗・役職通知・クールダウン・ロビー出入りなどの各種メッセージを定義します。色は `&` 形式のレガシーカラーコードで指定します。`%player%` `%time%` `%current%` `%max%` `%min%` `%count%` などのプレースホルダーが利用できます。

### 看板設定（`sign`）

参加看板（`sign.lobby`）・離脱看板（`sign.leave`）の各行の表示文を定義します。`sign.lobby.line4` では `%current%` `%max%` で人数を表示できます。

!!! note "設定変更後は `/yukioni reload`"
    `config.yml` を編集したら `/yukioni reload` を実行してください。設定が再読み込みされます。

## セットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **その場に立って** 実行します（実行位置が座標として `locations.yml` に保存されます）。

```text
/yukioni setup spawn       # 初期スポーン地点（退出・終了時の戻り先）
/yukioni setup lobby       # 待機ロビーの地点
/yukioni setup area pos1   # ゲームエリアの角1
/yukioni setup area pos2   # ゲームエリアの角2
```

設定後、ロビーに看板を設置します。看板の **1行目に `[Yukioni]`**、**2行目に `lobby`（参加用）または `leave`（離脱用）** と書いて設置すると、自動で整形された看板になります（設置には `yukioni.admin` 権限が必要）。

!!! tip "ゲームエリアについて"
    `area pos1` / `area pos2` の2点で囲まれた直方体がゲームエリアになります。プレイヤーはこの範囲外に出られず、開始時はこの範囲内のランダムな位置にテレポートされます。エリア未設定の場合は、エリア判定が無効になりロビー地点が代替に使われます。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/yukioni setup spawn` | `yukioni.admin` | スポーン地点を設定 |
| `/yukioni setup lobby` | `yukioni.admin` | ロビー地点を設定 |
| `/yukioni setup area pos1` | `yukioni.admin` | ゲームエリアの角1を設定 |
| `/yukioni setup area pos2` | `yukioni.admin` | ゲームエリアの角2を設定 |
| `/yukioni start` | `yukioni.admin` | ゲームを開始する |
| `/yukioni stop` | `yukioni.admin` | ゲームを強制終了する |
| `/yukioni reload` | `yukioni.admin` | config.yml を再読み込み |

!!! note "ゲームの開始について"
    プレイヤーがロビー看板から参加し、`min-players` 以上集まったら `/yukioni start` で開始します。人数が不足している場合は開始できません。異常時は `/yukioni stop` で強制終了できます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `yukioni.admin` | OP | 管理・セットアップコマンドすべて、看板の設置 |
| `yukioni.play` | 全員 | ゲーム参加権限 |

## トラブルシューティング

??? failure "プレイヤーがゲームに参加できない"
    参加導線は **ロビー看板** です。1行目 `[Yukioni]`・2行目 `lobby` の看板が正しく設置されているか、`/yukioni setup lobby` でロビー座標が設定済みかを確認してください。

??? failure "ゲームが開始できない"
    `min-players`（既定2人）以上がロビーに入っているか確認してください。人数不足の場合、`/yukioni start` を実行しても開始されません。また、すでにゲームが進行中の場合も開始できません。

??? failure "プレイヤーがエリア外に出てしまう／開始位置がおかしい"
    `/yukioni setup area pos1` と `pos2` の両方が設定されているか確認してください。エリアが未設定だとエリア判定が無効になり、開始時のテレポート先がロビー地点になります。pos1・pos2 は同じワールド内で設定してください。

??? failure "看板を設置しても整形されない"
    看板の整形には `yukioni.admin` 権限が必要です。1行目を `[Yukioni]`、2行目を `lobby` または `leave` と正確に入力してください（大文字小文字は問いません）。

??? failure "設定変更が反映されない"
    `config.yml` を編集したら `/yukioni reload` を実行してください。なお、座標は `locations.yml` に保存され、setup コマンド実行時に即保存されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Yukioni 概要へ](index.md){ .md-button }
