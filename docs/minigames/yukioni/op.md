<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Yukioni ― OP・運営ガイド { .page-op #yukioni-op }

Yukioni の導入・初期設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Yukioni |
| 説明 | 雪鬼ごっこミニゲームプラグイン |
| api-version | 26.1.2 |
| メインコマンド | `/yukioni <join\|leave\|setstartspawn\|setlobby\|setfield\|setsign\|start\|stop\|status\|reload>`（join/leaveは全員可） |
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
| `result-seconds` | 10 | 決着後、結果を表示してからロビーへ戻すまでの秒数 |

#### 初期の鬼の人数（`game.oni-thresholds`）

開始時の鬼の人数を、参加人数のしきい値テーブルで決めます。「その人数**以上**」で適用し、該当する最大のしきい値の値を採用します。最低1人は市民が残るよう内部で上限調整されます。

| 参加人数 | 初期の鬼 |
|---|---|
| 1〜7人 | 1人 |
| 8〜13人 | 2人 |
| 14人以上 | 3人 |

```yaml title="config.yml（既定値）"
game:
  oni-thresholds:
    "1": 1
    "8": 2
    "14": 3
```

### メッセージ設定（`messages`）

`prefix` のほか、ゲーム開始・勝敗・役職通知・クールダウン・ロビー出入りなどの各種メッセージを定義します。色は `&` 形式のレガシーカラーコードで指定します。`%player%` `%time%` `%current%` `%max%` `%min%` `%count%` などのプレースホルダーが利用できます。

### 看板設定（`sign`）

参加看板（`sign.lobby`）・離脱看板（`sign.leave`）の各行の表示文を定義します。`sign.lobby.line4` では `%current%` `%max%` で人数を表示できます。**開始看板の文言（`sign.start`）は config.yml に既定セクションが無く、プラグイン内蔵のデフォルト**（`[Yukioni]` / `▶クリックで開始`）で書き込まれます。カスタマイズしたい場合のみ config に `sign.start` を追記してください。

!!! note "設定変更後は `/yukioni reload`"
    `config.yml` を編集したら `/yukioni reload` を実行してください。設定が再読み込みされます。

!!! warning "既存サーバーは config が自動追記されません"
    本プラグインは `saveDefaultConfig()` のみのため、**既存の `config.yml` に新しいキーは自動追記されません**。コード側に既定値があるため動作はしますが、`sign.start` 等を編集したい場合は手動追記、または config を退避して再生成してください。

## セットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **その場に立って** 実行します（実行位置が座標として `locations.yml` に保存されます）。

```text title="初期スポーン地点（退出・終了時の戻り先）"
/yukioni setstartspawn
```

```text title="待機ロビーの地点"
/yukioni setlobby
```

```text title="ゲームエリアの角1"
/yukioni setfield 1
```

```text title="ゲームエリアの角2"
/yukioni setfield 2
```

設定後、ロビーに看板を設置します。看板を設置したら、**その看板を見た状態**で以下のコマンドを実行します（`yukioni.admin` 権限が必要）。

```text title="参加看板として登録"
/yukioni setsign join
```

```text title="離脱看板として登録"
/yukioni setsign leave
```

```text title="開始看板として登録（クリックでゲーム開始）"
/yukioni setsign start
```

```text title="登録済み看板の解除（視線先の登録看板を自動判別して解除）"
/yukioni setsign delete
```

```text title="設定状況を確認"
/yukioni status
```

コマンドを実行すると、プラグインがテキストを自動書き込みし、位置を `locations.yml` の各種別リストに保存します。手書きでのテキスト入力は不要です。`setsign delete` は視線先の看板の種別（参加／離脱／開始）を自動判別して登録を解除し、看板テキストもクリアします。

!!! success "看板は複数設置できます（v更新）"
    参加・離脱・開始の各看板を **複数拠点に設置** できるようになりました。`/yukioni setsign <join|leave|start>` は上書きではなく **追記** され、`/yukioni setsign delete` は視線先の1枚だけ解除します。人数表示は全枚数が同時に更新されます。**看板の位置は `locations.yml` に保存され、既存の看板データは自動で引き継がれます。設定のやり直しは不要**です（サーバー再起動のみでOK）。

### 数値設定コマンド（config に即保存）

以下のコマンドは config.yml の該当キーを書き換えて即保存します（`/yukioni reload` は不要）。

```text title="最小人数を設定"
/yukioni setmin <人数>
```

```text title="最大人数を設定"
/yukioni setmax <人数>
```

```text title="制限時間（秒）を設定"
/yukioni settime <秒>
```

```text title="鬼の足止め秒数を設定"
/yukioni setdelay <秒>
```

!!! note "開始看板はクリックで誰でも開始できます"
    `/yukioni setsign start` で設置した開始看板は、クリックするとゲームが始まります（内部で権限チェックを行わないため、設置すると一般プレイヤーもクリックで開始できます）。独立した `setstart` コマンドは廃止され、開始看板も `setsign start` で登録します。運営のみで開始したい場合は開始看板を設置せず、`/yukioni start` で開始してください。

!!! tip "ゲームエリアについて"
    `setfield 1` / `setfield 2` の2点で囲まれた直方体がゲームエリアになります。プレイヤーはこの範囲外に出られず、開始時はこの範囲内のランダムな位置にテレポートされます。エリア未設定の場合は、エリア判定が無効になりロビー地点が代替に使われます。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/yukioni setstartspawn` | `yukioni.admin` | スポーン地点を設定 |
| `/yukioni setlobby` | `yukioni.admin` | ロビー地点を設定 |
| `/yukioni setfield 1` | `yukioni.admin` | ゲームエリアの角1を設定 |
| `/yukioni setfield 2` | `yukioni.admin` | ゲームエリアの角2を設定 |
| `/yukioni join [名前]` | 全員（他人指定はOP） | ロビーに参加する。名前を指定すると対象を参加させる（OP限定） |
| `/yukioni leave [名前]` | 全員（他人指定はOP） | ロビーから退出する。名前を指定すると対象を退出させる（OP限定） |
| `/yukioni setsign <join\|leave\|start\|delete>` | `yukioni.admin` | 視線先の看板を参加／離脱／開始看板に登録。`delete` は視線先の登録看板を解除 |
| `/yukioni setmin <人数>` | `yukioni.admin` | 最小人数（`min-players`）を設定し即保存 |
| `/yukioni setmax <人数>` | `yukioni.admin` | 最大人数（`max-players`）を設定し即保存 |
| `/yukioni settime <秒>` | `yukioni.admin` | 制限時間（`match-duration`）を設定し即保存 |
| `/yukioni setdelay <秒>` | `yukioni.admin` | 鬼の足止め秒数（`oni-freeze-time`）を設定し即保存 |
| `/yukioni start` | 全員 | ゲームを開始する |
| `/yukioni stop` | `yukioni.admin` | ゲームを強制終了する |
| `/yukioni status` | 全員 | 設定状況を確認する（読み取り専用） |
| `/yukioni stats [名前]` | 全員 | 成績を確認する（読み取り専用） |
| `/yukioni reload` | `yukioni.admin` | config.yml を再読み込み |

!!! note "ゲームの開始について"
    プレイヤーがロビー看板から参加し、`min-players` 以上集まったら `/yukioni start` で開始します。`start` は全員可（コンソール／コマンドブロックからも実行可）で、人数が不足している場合は開始できません。異常時は `/yukioni stop` で強制終了できます（`stop` はOP限定）。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `yukioni.admin` | OP | 管理・セットアップコマンドすべて、看板の設置 |
| `yukioni.play` | 全員 | ゲーム参加権限 |

## トラブルシューティング

??? failure "プレイヤーがゲームに参加できない"
    参加導線は **ロビー看板** です。`/yukioni setsign join` を、登録したい看板を見ながら実行してください（`yukioni.admin` 権限が必要）。また、`/yukioni setlobby` でロビー座標が設定済みかも確認してください。

??? failure "ゲームが開始できない"
    `min-players`（既定2人）以上がロビーに入っているか確認してください。人数不足の場合、`/yukioni start` を実行しても開始されません。また、すでにゲームが進行中の場合も開始できません。

??? failure "プレイヤーがエリア外に出てしまう／開始位置がおかしい"
    `/yukioni setfield 1` と `/yukioni setfield 2` の両方が設定されているか確認してください。エリアが未設定だとエリア判定が無効になり、開始時のテレポート先がロビー地点になります。pos1・pos2 は同じワールド内で設定してください。

??? failure "看板が登録されない"
    看板の登録には `yukioni.admin` 権限が必要です。登録したい看板を見ながら `/yukioni setsign join`（または `leave`）を実行してください（`yukioni.admin` 権限が必要）。

??? failure "設定変更が反映されない"
    `config.yml` を編集したら `/yukioni reload` を実行してください。なお、座標は `locations.yml` に保存され、setup コマンド実行時に即保存されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Yukioni 概要へ](index.md){ .md-button }
