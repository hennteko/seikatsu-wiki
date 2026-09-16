<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Overcook ― OP・運営ガイド { .page-op #overcook-op }

Overcook（オーバークック風・協力調理ミニゲーム）の導入・ステージ／ステーション設定・看板・config／recipes.yml・権限・管理コマンドをまとめます。地点・ステージ・ステーション・看板はコマンドまたは **設定ツール（`/overcook tool`）** で登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Overcook |
| メインコマンド | `/overcook` |
| api-version | 1.21 |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Overcook/config.yml`（全体設定・地点・看板・ステージ）／`recipes.yml`（食材・料理） |
| 権限ノード | `overcook.admin`（既定OP） |

## 導入手順

1. ビルドした `Overcook` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` と `recipes.yml` が自動生成される。
3. `/overcook setlobby`・`/overcook setstartspawn` でロビーと初期スポーンを設定する。
4. `/overcook stage create <名前>` でステージを作成し、フィールド・スポーン・各ステーションを登録する。
5. 参加・離脱・開始の看板を設置する。
6. `/overcook status` で設定状況（開始可能かどうか）を確認する。

!!! tip "設定は「設定ツール」が最速です"
    `/overcook tool` で受け取る設定ツールを使うと、ブロックを見ながらステーション登録などを素早く行えます。コマンドでも同じ設定ができます。

## セットアップ手順

地点系は **実行した位置**、ステーション・看板系は対象ブロック／看板を **見ながら（6ブロック以内）** 実行します。すべて `overcook.admin` 権限が必要です。

```text title="設定ツールを受け取る（推奨）"
/overcook tool
```

```text title="初期スポーン（途中抜けの戻り先／その場に立って実行）"
/overcook setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/overcook setlobby
```

```text title="ステージを作成（英数字・_・-、16文字以内）"
/overcook stage create stage1
```

```text title="ステージ一覧／削除"
/overcook stage list
/overcook stage delete stage1
```

```text title="ゲームスポーン（その場に立って実行）"
/overcook setspawn stage1
```

```text title="フィールド範囲の角1／角2（その場に立って実行）"
/overcook setfield stage1 1
/overcook setfield stage1 2
```

```text title="ステーションを登録／解除（対象ブロックを見て実行）"
/overcook setstation stage1 <board|stove|pot|fryer|rice_cooker|plate|table|serve|trash|extinguisher|gate|ingredient|delete> [食材ID]
```

```text title="食材チェストの登録は食材ID付き（例：牛肉）"
/overcook setstation stage1 ingredient beef
```

!!! note "ステーションの種類（12種）"
    `board`（まな板）/ `stove`（コンロ・焼く）/ `pot`（鍋・煮る）/ `fryer`（フライヤー・揚げる）/ `rice_cooker`（炊飯器・炊く）/ `plate`（皿置き場）/ `table`（作業台）/ `serve`（提供口）/ `trash`（ゴミ箱）/ `extinguisher`（消火器置き場）/ `gate`（開閉ブロック）/ `ingredient`（食材チェスト）です。食材チェスト（`ingredient`）は **食材ID** を付けて登録します（食材IDは `recipes.yml` の定義。既定は `beef`/`lettuce`/`tomato`/`fish`/`bread`/`apple`/`rice`/`egg`/`basil`/`potato`/`chicken`/`carrot`）。同じ種類を複数ブロック登録できます。`delete` は見ているブロックの登録を解除します。加熱ステーション（stove/pot/fryer/rice_cooker）はそれぞれ対応する調理法で食材を加熱します。

## 看板の設置

```text title="参加看板を登録（看板を見て実行）"
/overcook setsign join
```

```text title="離脱看板を登録"
/overcook setsign leave
```

```text title="開始看板を登録（ステージ指定）"
/overcook setsign start stage1
```

```text title="視線先の看板の登録を解除"
/overcook setsign delete
```

!!! success "看板は複数設置できます"
    参加・離脱・開始の各看板を複数設置できます（開始看板はステージごと）。`config.yml` の `signs` にリストで保存されます。

## config.yml 設定項目

### 全体設定（`settings`）

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 8 | 最大参加人数 |
| `settings.drop-item-despawn-seconds` | 30 | 投げた食材が消えるまでの秒数 |
| `settings.board-clicks` | 5 | まな板で切るのに必要な右クリック回数 |
| `settings.stove-cook-seconds` | 8 | コンロで焼き上がるまでの秒数 |
| `settings.stove-burn-seconds` | 10 | 焼き上がり後、焦げるまでの猶予秒数 |
| `settings.pot-cook-seconds` | 12 | 鍋（煮る）の秒数。焦げない |
| `settings.fryer-cook-seconds` | 5 | フライヤー（揚げる）の秒数 |
| `settings.fryer-burn-seconds` | 4 | フライヤー：揚がった後、焦げるまでの猶予秒数 |
| `settings.rice-cook-seconds` | 15 | 炊飯器（炊く）の秒数。焦げない |
| `settings.rice-portions` | 3 | 炊飯器：1回で炊ける人前 |
| `settings.fire-delay-seconds` | 8 | 焦げを放置してから発火するまでの秒数（火事ONステージ） |
| `settings.fire-spread-seconds` | 10 | 火事が延焼を試みる間隔（秒） |
| `settings.fire-spread-radius` | 3 | 延焼する範囲（ブロック） |
| `settings.extinguish-clicks` | 6 | 消火に必要な右クリック回数 |
| `settings.rush-order-multiplier` | 2 | 急ぎ注文の得点倍率 |
| `settings.gate-closed-block` | IRON_BARS | 開閉ブロックが「閉」のときに置くブロック |
| `settings.combo-max` | 3 | コンボ倍率の上限（連続提供2回ごとに+1） |
| `settings.order-warning-seconds` | 10 | 注文の残り秒数がこれ以下で赤表示 |
| `settings.expire-penalty` | 20 | 注文期限切れの減点 |
| `settings.wrong-serve-penalty` | 10 | 誤納品（注文にない料理）の減点 |
| `settings.time-bonus-max` | 50 | 期限に対する残り時間ボーナスの最大値 |
| `settings.countdown-seconds` | 3 | 開始カウントダウン |
| `settings.result-seconds` | 5 | 終了後の結果表示時間 |

### ステージ別設定（`stages.<id>`）

ステージはコマンドで自動生成されます。以下のパラメータは `config.yml` の `stages.<id>` で調整できます（座標系はコマンド／ツールで設定）。

| キー | 既定値 | 説明 |
|---|---|---|
| `time-limit-seconds` | 180 | 制限時間（秒） |
| `base-players` | 3 | 基準人数（人数に応じた難易度調整の基準） |
| `order-interval-seconds` | 15 | 注文が入る間隔（秒） |
| `order-expire-seconds` | 60 | 注文の期限（秒） |
| `max-orders` | 4 | 同時に出る注文の最大数 |
| `easy-seconds` | 45 | 序盤の易しい時間（この間は tier2 以上の料理は出ない） |
| `target-scores` | `[500, 1000, 1500]` | 星1／星2／星3 の目標スコア |
| `menu` | 料理ID→重み | このステージで出る料理と出現重み（例：`{ salad: 3, steak: 3, grilled_fish: 2, steak_set: 1 }`） |
| `gimmicks` | 下記マップ | ステージのギミック設定（火事・開閉ブロック・提供口移動・ネズミ・急ぎ注文） |

#### ステージギミック（`stages.<id>.gimmicks`）

| キー | 既定値 | 説明 |
|---|---|---|
| `fire-enabled` | 0 | 火事を有効化（1でON。焦げ放置で発火・延焼） |
| `max-fire` | 2 | 同時に存在できる火の最大数 |
| `gate-open-seconds` | 0 | 開閉ブロックが「開」でいる秒数（0で常時開＝ギミック無効） |
| `gate-closed-seconds` | 5 | 開閉ブロックが「閉」でいる秒数 |
| `serve-rotate-seconds` | 0 | 提供口が移動する間隔（0で固定） |
| `rat-seconds` | 0 | ネズミが出る間隔（0で出ない。食材を狙う） |
| `rush-chance` | 0 | 急ぎ注文が出る確率 |
| `rush-time-seconds` | 0 | 急ぎ注文の制限時間（秒） |

!!! note "自動生成される領域（手動編集は座標以外のみ推奨）"
    `lobby-spawn` / `default-spawn` / `signs` / `stages.<id>.spawn` / `field` / `stations` はコマンド・ツールで自動保存されます。座標データの手動編集は非推奨です。`time-limit-seconds` や `menu` / `target-scores` などのステージ調整値は `config.yml` で編集できます。

## recipes.yml（食材・料理）

食材と料理は `recipes.yml` で定義し、`/overcook reload` で再読み込みできます。

- **ingredients（食材）** … `item`（生の見た目）/ `cut-item`（切った後）/ 加熱後の見た目 `cooked-item`（焼く）/ `boiled-item`（煮る）/ `fried-item`（揚げる）/ `steamed-item`（炊く）/ 加熱可否 `can-cut`・`can-cook`・`can-boil`・`can-fry`・`can-steam` / `requires-cut`（切ってからでないと加熱できない。旧 `cook-requires-cut` も可）。
- **dishes（料理）** … `requires`（`"食材ID:状態"` のリスト。同じ食材を2つ書けば2個必要。状態＝`raw`/`cut`/`cooked`/`cut_cooked`/`boiled`/`cut_boiled`/`fried`/`cut_fried`/`steamed`/`cut_steamed`）/ `score`（基本点）/ `tier`（1＝序盤から出る、2以上＝`easy-seconds` の間は出ない）/ `icon`（設定GUIでの見た目）。

既定では食材12種（`beef`/`lettuce`/`tomato`/`fish`/`bread`/`apple`/`rice`/`egg`/`basil`/`potato`/`chicken`/`carrot`）、料理12種（サラダ／ステーキ／焼き魚／ステーキ定食／ハンバーガー／フィッシュサラダ／フルーツ盛り／フライドポテト／唐揚げ／野菜スープ／ミックスグリル／ガパオライス）が定義されています。**加熱法は 焼く（コンロ）／煮る（鍋）／揚げる（フライヤー）／炊く（炊飯器）** の4種で、食材の `can-*` フラグで対応法が決まります。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/overcook tool` | 設定ツールを入手 |
| `/overcook stage <create\|delete\|list> [名前]` | ステージの作成／削除／一覧 |
| `/overcook setlobby` / `setstartspawn` | ロビー／初期スポーンを設定 |
| `/overcook setspawn <ステージ>` | ゲームスポーンを設定 |
| `/overcook setfield <ステージ> <1\|2>` | フィールド範囲の角を設定 |
| `/overcook setstation <ステージ> <種類\|delete> [食材ID]` | ステーションを登録／解除 |
| `/overcook setsign <join\|leave\|start <ステージ>\|delete>` | 看板を設定／解除 |
| `/overcook stop` | ゲームを強制終了 |
| `/overcook reload` | `recipes.yml` を再読み込み |
| `/overcook status` | 設定状況・現在の状況を確認（全員可） |
| `/overcook ranking <ステージ>` | ハイスコアを表示（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `overcook.admin` | OP | 設定ツール・ステージ／ステーション設定・看板設置・強制停止・reload など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/overcook join`・`/overcook leave`・`/overcook start <ステージ>`・`/overcook status`・`/overcook ranking` は権限チェックがなく、全プレイヤーが使えます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/overcook status` を確認してください。各ステージに **スポーン・フィールド・必要なステーション** が揃っていないと「未設定」と表示され開始できません。ロビーの設定と参加人数も確認してください。

??? failure "料理が完成しない・提供できない"
    `recipes.yml` の `requires`（必要な食材と状態）を満たしているか確認してください。食材の状態（raw/cut/cooked/cut_cooked）が一致しないと料理になりません。`/overcook reload` で再読み込みできます。

??? failure "食材チェストから正しい食材が出ない"
    `ingredient` ステーションは **食材ID付き** で登録します（`/overcook setstation <ステージ> ingredient <食材ID>`）。食材IDは `recipes.yml` の定義に一致させてください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Overcook 概要へ](index.md){ .md-button }
