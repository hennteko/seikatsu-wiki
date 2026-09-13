<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ショットガンルーレット ― OP・運営ガイド { .page-op #shotgun-op }

ショットガンルーレットの導入・席／地点設定・看板・設定GUI・config・権限・管理コマンドをまとめます。地点・席・看板はコマンドで登録すると自動保存されます（手動編集は基本不要）。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Shotgun |
| メインコマンド | `/shotgun`（エイリアス `/sg`） |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| 作者 | henry |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Shotgun/config.yml` |
| 権限ノード | `shotgun.admin`（既定OP） |

## 導入手順

1. ビルドした `Shotgun-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Shotgun/config.yml` が自動生成される。
3. `/shotgun setstartspawn`・`/shotgun setlobby` で初期スポーンと共通ロビーを設定する。
4. 円卓の **席（1〜4）** を `/shotgun setseat <エリア> <番号>` で登録し、観戦地点を設定する（席を登録するとエリアが自動作成される）。
5. 参加・離脱・開始の看板を設置する。
6. `/shotgun status`・`/shotgun arenalist` で設定状況を確認する。

!!! info "複数エリア対応（同時進行）"
    席・ロビー・観戦・看板は **エリア（卓）ごと** に登録でき、**複数エリアで試合を同時進行** できます。各コマンドは `<エリア>` を取りますが、**エリアが1つだけのときはエリア名を省略可**（省略時は唯一のエリア、無ければ `main` を自動作成）。**旧バージョンの設定（席・観戦・参加看板）は起動時にエリア `main` へ自動移行** されます。設定のやり直しは不要です。

!!! tip "席は中央を向いて登録"
    `/shotgun setseat <エリア> <1-4>` は **実行者の位置・向き** を席として保存します。円卓の中央を向いて実行してください。着席は透明アーマースタンドで固定され、プレイヤーは席から降りられません。

## セットアップ手順（コマンド）

地点・席系は **実行した位置**、看板系は看板を **見ながら（6ブロック以内）** 実行します。すべて `shotgun.admin` 権限が必要です。エリアが1つだけなら `<エリア>` は省略できます。

```text title="初期スポーン（離脱時の戻り先／その場に立って実行）"
/shotgun setstartspawn
```

```text title="共通ロビー（その場に立って実行。エリア別は setlobby <エリア>）"
/shotgun setlobby
```

```text title="エリア別ロビー（その場に立って実行）"
/shotgun setlobby main
```

```text title="席を登録（エリア＋1〜4・中央を向いて実行。エリア自動作成）"
/shotgun setseat main 1
```

```text title="観戦地点（エリア指定・未設定時は席の重心の2ブロック上にフォールバック）"
/shotgun setspectate main
```

```text title="参加看板を登録（エリア指定）"
/shotgun setsign join main
```

```text title="離脱看板を登録（エリア不問）"
/shotgun setsign leave
```

```text title="開始看板を登録（エリア＋モード指定・br/dealer/team）"
/shotgun setsign start main br
```

```text title="視線先の看板の登録を解除（種別を自動判別・テキストもクリア）"
/shotgun setsign delete
```

!!! success "看板は複数設置できます"
    参加・離脱・開始の各看板を **複数拠点に設置** できます（参加はエリアごと、開始はエリア＋モード `br`/`dealer`/`team` ごと、離脱はエリア不問）。登録は上書きではなく **追記** され、`/shotgun setsign delete` は視線先の1枚だけ解除します。保存形式は `signs.join.<エリア>` / `signs.leave`（リスト）・`signs.start.<エリア>.<br|dealer|team>`（リスト）です。

## 設定GUIとクイック設定コマンド

残機・タイマーなどは、コマンドまたは **OP用の設定アイテム（設定GUI）** で変更できます。変更は **次の試合から** 反映されます。

```text title="残機の初期値を設定（1〜10）"
/shotgun setlives 3
```

```text title="手番タイマーを設定（秒・10〜120）"
/shotgun settimer 30
```

```text title="設定アイテム（ネザースター）を受け取る"
/shotgun settings
```

!!! note "設定GUIでできること"
    `/shotgun settings` で配布されるネザースター「§6ショットガン設定」から、残機・タイマー・弾数・サドンデス・**アイテムのON/OFF** をGUIで変更できます。変更内容は即 `config.yml` に保存され、次の試合から反映されます。

## config.yml 設定項目

地点・席・看板はコマンドで自動保存されます。主な調整項目は次のとおりです。

### 人数・時間・残機

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 1 | 最低人数（テスト用に1人開始を許可。0は常に拒否） |
| `max-players` | 4 | 最大参加人数 |
| `countdown-seconds` | 5 | 開始カウントダウン |
| `turn-seconds` | 30 | 手番タイマー（時間切れ＝自分を撃つ） |
| `result-seconds` | 15 | 結果発表の表示秒数 |
| `lives.initial` | 3 | 残機の初期値 |
| `lives.max-bonus` | 2 | 残機の上限＝`initial + max-bonus`（金リンゴでこれを超えられない） |

### 弾・サドンデス・発砲

| キー | 既定値 | 説明 |
|---|---|---|
| `shells.min` | 2 | 1装填の最小総弾数 |
| `shells.max` | 8 | 1装填の最大総弾数 |
| `shells.per-player` | 2 | 総弾数＝生存者数 × per-player ± jitter |
| `shells.jitter` | 1 | 総弾数のブレ幅 |
| `sudden-death.after-reloads` | 3 | この回数目の再装填以降、金リンゴの代わりに「怪しい薬」（SUSPICIOUS）を配布する（0で無効） |
| `gun.aim-range` | 8.0 | 視線判定の距離 |

### アイテム

| キー | 既定値 | 説明 |
|---|---|---|
| `items.per-reload` | `[1, 2, 3, 3, 4]` | n回目の装填での配布個数（以降は最後の値） |
| `items.enabled.*` | 全13種 true | 設定GUIと連動。falseのアイテムは全プールから除外 |
| `items.pool.2` / `.3` / `.4` | 生存者数ごとのリスト | 抽選プール（重複記載で重み付け可）。2人戦はエンダーパール・盾・ベルを外す等 |

アイテムの種類は `SPYGLASS`（望遠鏡）/ `HOPPER`（ホッパー）/ `GOLDEN_APPLE`（金リンゴ）/ `CHAIN`（鎖）/ `GUNPOWDER`（火薬）/ `INVERTER`（反転）/ `COMPASS`（コンパス）/ `STEAL`（盗賊の手）/ `ENDER_PEARL`（エンダーパール）/ `SHIELD`（盾）/ `TOTEM`（不死のトーテム）/ `BELL`（ベル）/ `SUSPICIOUS`（怪しい薬）の13種です。`SUSPICIOUS` は **サドンデス以降のみ** 配布され、金リンゴの枠を置き換えます（50%で残機+1・50%で残機-1）。

### モード・HUD・予想・環境

| キー | 既定値 | 説明 |
|---|---|---|
| `dealer.lives` | 5 | ディーラー戦のディーラー残機 |
| `dealer.chain-limit` | false | ディーラーに「同一相手へ連続で鎖不可」を適用するか |
| `hud.show-remaining-types` | false | trueで実弾/空砲の残数を常時表示 |
| `bet.enabled` | true | 観戦者の実弾/空砲予想を有効化 |
| `keep-food` | true | 満腹度を減らさない |

### 報酬（`rewards`）

順位ごとにコンソールから実行するコマンドを登録できます（`%player%` 置換・既定は空）。他プラグインの経済には影響しません。

| キー | 説明 |
|---|---|
| `rewards.commands.1st` 〜 `4th` | 各順位のプレイヤーに対して実行するコマンドのリスト |

### ロビー共通インベントリ（`lobby-inventory`）

ロビー入場時に所持品を退避し、退室・試合終了で復元する共通システムです。退避データは `plugins/Shotgun/lobby-inventory.yml` に保存され、再起動をまたいでも復元されます。

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | 機能の有効化 |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（`ADVENTURE`/`SURVIVAL`/`CREATIVE`/`KEEP`） |
| `lobby-inventory.clear-effects` | true | ロビーでポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビーで体力をリセット |
| `lobby-inventory.reset-exp` | true | ロビーで経験値をリセット |
| `lobby-inventory.expire-days` | 30 | 退避データの保持日数 |

!!! note "自動生成される領域（手動編集不要）"
    `default-spawn` / `lobby-spawn` / `spectate-spawn` / `seats`（席）/ `signs`（看板）はコマンドで自動生成・自動保存されます。手動編集は不要です。値を変えたら `/shotgun reload`（試合中は不可）で反映します。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/shotgun setstartspawn` | 初期スポーン地点を設定（共通） |
| `/shotgun setlobby [エリア]` | ロビー地点を設定（エリア省略で共通ロビー） |
| `/shotgun setseat <エリア> <1-4>` | 席を登録（実行者の位置・向き。エリア自動作成。単一エリアなら `<エリア>` 省略可） |
| `/shotgun setspectate [エリア]` | 観戦地点を設定 |
| `/shotgun arenalist` | エリア一覧を表示 |
| `/shotgun delarena <エリア>` | エリアを削除（席・看板登録も削除・試合中は不可） |
| `/shotgun setlives <n>` | 残機の初期値を設定（1〜10・次の試合から） |
| `/shotgun settimer <秒>` | 手番タイマーを設定（10〜120・次の試合から） |
| `/shotgun settings` | OP用設定アイテム（設定GUI）を配布 |
| `/shotgun setsign <join [エリア]\|leave\|start <エリア> <br\|dealer\|team>\|delete>` | 看板を設定／解除 |
| `/shotgun lobbyfix <プレイヤー> [force]` | ロビー退避データを手動で復旧する |
| `/shotgun stop [エリア]` | ゲームを強制終了（結果なしでロビーへ） |
| `/shotgun reset` | 緊急リセット（全エリアのアーマースタンド・TextDisplay・BossBar・GUIを完全消去） |
| `/shotgun reload` | config を再読み込み（試合中は不可） |
| `/shotgun status` | 設定状況・現在の状況を確認（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `shotgun.admin` | OP | セットアップ・設定変更・看板設置・強制停止・lobbyfix など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/shotgun join`・`/shotgun leave`・`/shotgun start <モード>`・`/shotgun status`・`/shotgun bet <live\|blank>` は権限チェックがなく、全プレイヤーが使えます。`/shotgun start` は看板クリックと同じく誰でも開始できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/shotgun status` を確認してください。席（`setseat`）が人数分、ロビーが設定されている必要があります。ロビーに最低人数以上いることも必要です。

??? failure "席に座れない・降りられてしまう"
    席は透明アーマースタンドで固定します。`/shotgun setseat` で席が登録されているか確認してください。異常時は `/shotgun reset` でアーマースタンド等を消去できます。

??? failure "試合が正常に終わらない・残留物がある"
    `/shotgun reset` を実行してください。アーマースタンド・TextDisplay・BossBar・GUIを完全に消去し、状態をリセットします。

??? failure "config を編集したのに反映されない"
    `/shotgun reload` で再読み込みしてください（試合中は実行できません）。残機・タイマー等は次の試合から反映されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ショットガンルーレット 概要へ](index.md){ .md-button }
