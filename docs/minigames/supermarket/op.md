<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Supermarket ― OP・運営ガイド { .page-op #supermarket-op }

Supermarket（協力スーパー経営ミニゲーム）の導入・地点／ステーション設定・看板・プリセット・config・権限・管理コマンドをまとめます。地点・ステーション・看板はコマンドまたは **設定ツール（`/supermarket tool`）** で登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Supermarket |
| メインコマンド | `/supermarket` |
| api-version | 1.21 |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Supermarket/config.yml`（全体設定・地点・看板・ステーション）ほかプリセット・商品データ |
| 権限ノード | `supermarket.admin`（既定OP） |

## 導入手順

1. ビルドした `Supermarket` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される。
3. `/supermarket setlobby`・`/supermarket setstartspawn`・`/supermarket setspawn` でロビー・初期スポーン・ゲームスポーンを設定する。
4. `/supermarket setcustomerspawn` で客の出現地点、`/supermarket setfield 1`・`2` でフィールド範囲を設定する。
5. 搬入口・棚・レジ・ゴミ箱の各ステーションを登録し、看板を設置する。
6. `/supermarket status` で設定状況を確認する。

!!! tip "設定は「設定ツール」が便利です"
    `/supermarket tool` で受け取る設定ツールから、ステーション登録やプリセット（難易度・商品構成）の編集をGUIで行えます。コマンドでも同じ設定ができます。

## セットアップ手順

地点系は **実行した位置**、ステーション・看板系は対象ブロック／看板を **見ながら（6ブロック以内）** 実行します。すべて `supermarket.admin` 権限が必要です。

```text title="設定ツールを受け取る"
/supermarket tool
```

```text title="受付ロビー（その場に立って実行）"
/supermarket setlobby
```

```text title="初期スポーン（離脱時の戻り先）"
/supermarket setstartspawn
```

```text title="ゲームスポーン（開始時のプレイヤー配置）"
/supermarket setspawn
```

```text title="客用スポーン（客の出現地点）"
/supermarket setcustomerspawn
```

```text title="フィールド範囲の角1／角2"
/supermarket setfield 1
/supermarket setfield 2
```

```text title="ステーションを登録／解除（対象ブロックを見て実行）"
/supermarket setstation <delivery|shelf|register|trash> [商品ID]
```

!!! note "ステーションの種類"
    `delivery`（搬入口）/ `shelf`（棚）/ `register`（レジ）/ `trash`（ゴミ箱）の4種です。**棚（`shelf`）は商品IDを付けて登録** します（未解放の商品でも登録可）。同じ種類を複数ブロック登録できます。同じブロックにもう一度実行すると登録解除になります。

## 看板の設置

```text title="参加看板を登録（看板を見て実行）"
/supermarket setsign join
```

```text title="離脱看板を登録"
/supermarket setsign leave
```

```text title="開始看板を登録（プリセット識別子を指定）"
/supermarket setsign start <識別子>
```

```text title="視線先の看板の登録を解除"
/supermarket setsign delete
```

## プリセット（難易度・商品構成）

営業のパラメータや商品構成は **プリセット** で管理します。開始時に識別子で選びます。

```text title="プリセットを作成"
/supermarket preset create <識別子>
```

```text title="プリセットを削除"
/supermarket preset delete <識別子>
```

!!! note "プリセットの編集は設定ツールから"
    作成したプリセットの中身（難易度・商品の解放条件など）は、`/supermarket tool` の **プリセット管理GUI** から編集します。`/supermarket start <識別子>` や開始看板でプリセットを指定して開始します。

## config.yml 設定項目（`settings`）

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 8 | 最大参加人数 |
| `settings.countdown-seconds` | 3 | 開始カウントダウン |
| `settings.prepare-seconds` | 60 | 準備フェーズの長さ（秒） |
| `settings.business-seconds` | 300 | 営業フェーズの長さ（秒） |
| `settings.result-seconds` | 8 | 結果表示の秒数 |
| `settings.shelf-visual-max` | 6 | 棚に見た目として並ぶ最大数 |
| `settings.customer-move-speed` | 0.16 | 客の移動速度 |
| `settings.customer-spawn-interval-seconds` | 8 | 客の出現間隔（秒） |
| `settings.register-wait-warning-seconds` | 30 | レジ待ちの警告を出す秒数 |
| `settings.expire-check-interval-seconds` | 5 | （傷み等の）期限チェック間隔（秒） |
| `settings.coin-rounding` | 10 | 金額の丸め単位 |
| `settings.order-delivery-seconds` | 20 | 発注してから搬入口に届くまでの秒数 |
| `settings.price-change-step` | 10 | 価格変更の刻み幅 |

!!! note "自動生成される領域（手動編集不要）"
    `lobby-spawn` / `default-spawn` / `game-spawn` / `customer-spawn` / `field` / `signs` / `stations` はコマンド・ツールで自動保存されます。プリセット・商品データも専用ファイルで管理されます。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/supermarket tool` | 設定ツールを入手 |
| `/supermarket setlobby` / `setstartspawn` / `setspawn` / `setcustomerspawn` | 各地点を設定 |
| `/supermarket setfield <1\|2>` | フィールド範囲の角を設定 |
| `/supermarket setstation <種類> [商品ID]` | ステーションを登録／解除 |
| `/supermarket setsign <join\|leave\|start <識別子>\|delete>` | 看板を設定／解除 |
| `/supermarket preset <create\|delete> <識別子>` | プリセットの作成／削除 |
| `/supermarket stop` | 営業を強制終了 |
| `/supermarket status` | 設定状況・現在の状況を確認（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `supermarket.admin` | OP | 設定ツール・地点／ステーション設定・看板設置・プリセット管理・強制終了など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/supermarket join`・`/supermarket leave`・`/supermarket ready`・`/supermarket start <識別子>`・`/supermarket status` は権限チェックがなく、全プレイヤーが使えます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/supermarket status` を確認してください。ロビー・ゲームスポーン・客用スポーン・フィールド、および各ステーション（搬入口・棚・レジ）が設定されている必要があります。開始にはプリセットの指定（`start <識別子>`）も必要です。

??? failure "客が商品を買わない・棚から取らない"
    棚（`shelf`）に商品IDが正しく割り当てられ、搬入口から補充できているか確認してください。棚が空だと客は購入できません。

??? failure "設定を変えたのに反映されない"
    営業パラメータは `config.yml` の `settings`、難易度・商品構成はプリセットで管理されます。プリセットは `/supermarket tool` のGUIから編集してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Supermarket 概要へ](index.md){ .md-button }
