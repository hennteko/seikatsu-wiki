<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Bomberman（ボンバーマン）― OP・運営ガイド { .page-op #bomberman-op }

ボンバーマンの導入・フィールド設定・看板・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Bomberman |
| メインコマンド | `/bomberman`（エイリアス `/bm`・`/bomber`） |
| 依存プラグイン | なし |
| api-version | 26.1.2 |
| 設定ファイル | `plugins/Bomberman/config.yml` |

!!! info "仕組み"
    `setfield` の2点で囲んだ直方体にゲーム開始時に **格子状フィールドを自動生成**（外周と偶数マスに硬ブロック、残りに軟ブロックを密度指定で配置）し、終了時に元の地形へ **完全復元** します。爆弾は重力無効の TNT 落下ブロック（FallingBlock）で表示し（統合版／Geyserでも表示されるようにするため。BlockDisplay は使用しません）、十字判定はプラグインが自前で計算します。

## 導入手順

1. ビルドした `Bomberman` の jar を `plugins/` に配置する。
2. サーバーを起動すると `plugins/Bomberman/config.yml` が生成される。
3. 後述の手順でフィールド・スポーン・看板を設定する。
4. `/bomberman status` で設定状況を確認する。

## セットアップ手順

OP権限（`bomberman.admin`）で、設定したい場所に **立って／対象を見て** 実行します。各設定は即 `config.yml` に保存されます。

```text title="① 受付ロビーを現在地に設定"
/bomberman setlobby
```
```text title="② 初期スポーン（離脱・終了時の戻り先）を現在地に設定"
/bomberman setstartspawn
```
```text title="③ フィールド範囲の角1 / 角2を現在地に設定"
/bomberman setfield 1
```
```text title="③ フィールド範囲の角2"
/bomberman setfield 2
```
```text title="④ プレイヤー開始スポーンを現在地に追加（参加人数ぶん 1,2,3… と設定）"
/bomberman setspawn <番号>
```
```text title="（任意）視線先のブロックを軟ブロック種別として登録"
/bomberman setsoftblock
```
```text title="設定状況を確認"
/bomberman status
```

!!! tip "フィールドとスポーンの注意"
    `setfield 1`/`2` の2点で直方体（X・Z範囲）を決め、下から床→障害物2段が生成されます。**スポーンはフィールドの升目上の正しい高さ（足元の段）に立って** `setspawn <番号>` で設定してください。スポーン数が実質の最大参加人数になります（`maxplayers` と少ない方が上限）。外周と偶数マスは固定の硬ブロックになります。

### 看板の設置

参加・離脱・開始の3種の看板を設置できます。看板を見ながら（6ブロック以内）以下を実行します。

```text title="参加看板を登録"
/bomberman setsign join
```
```text title="離脱看板を登録"
/bomberman setsign leave
```
```text title="開始看板を登録"
/bomberman setsign start
```
```text title="視線先の看板の登録を解除"
/bomberman removesign
```

テキストはプラグインが自動で書き込みます（1行目 `[ボンバーマン]`）。位置は config の `signs` に保存され、再起動後も有効です。看板の破壊は `bomberman.admin` のみ可能で、破壊すると登録も解除されます。

## ゲーム設定コマンド（`bomberman.admin`）

各設定は実行直後に config へ保存されます。

```text title="軟ブロックの配置密度（%・0〜100）"
/bomberman setratio <0-100>
```
```text title="爆弾の起爆秒数（>0・小数可）"
/bomberman setbombtimer <秒>
```
```text title="初期火力（1〜50）"
/bomberman setpower <数字>
```
```text title="初期の同時設置数（1〜50）"
/bomberman setbombs <数字>
```
```text title="サドンデス開始秒数（0〜100000・0で無効）"
/bomberman setsuddendeath <秒>
```
```text title="最大参加人数（2〜64）"
/bomberman maxplayers <数字>
```
```text title="開始に必要な最小人数（1〜64・1で一人練習モード可）"
/bomberman setminplayers <数字>
```

!!! tip "一人練習モード"
    `setminplayers 1` にすると **一人でも開始** できます。この場合は通常の勝敗判定を行わず、本人が全滅（爆風で脱落）するまで自由に爆弾を試せる **練習モード** になります。終了は `/bomberman stop` で行います。

## config.yml 主要項目

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 8 | 最大参加人数 |
| `settings.min-players` | 2 | 開始に必要な最小人数（1で一人練習モード可） |
| `settings.bomb-timer` | 2.5 | 爆弾の起爆秒数 |
| `settings.default-power` | 2 | 初期火力 |
| `settings.default-bombs` | 1 | 初期の同時設置数 |
| `settings.sudden-death-time` | 120 | サドンデス開始秒（0で無効） |
| `settings.soft-block-ratio` | 75 | 軟ブロックの配置密度（%） |
| `settings.item-drop-chance` | 35 | 軟ブロック破壊時のパワーアップ出現率（%） |
| `blocks.soft` | TERRACOTTA | 軟ブロックの種類 |
| `blocks.floor` | SMOOTH_STONE | 床ブロックの種類 |
| `powerups.<種別>.weight` | fire30 / bomb30 / speed25 / pierce8 / full7 | パワーアップの抽選重み |

- 硬ブロックは `IRON_BLOCK` 固定（config項目なし）。
- `locations`（ロビー/スポーン/フィールド）と `signs` はコマンド実行時に自動生成・保存されます。手動編集は不要です。

!!! warning "既存サーバーは config が自動追記されません"
    本プラグインは `saveDefaultConfig()` のみで、既存の `config.yml` に新キーを自動追記しません。値を調整したい場合は各 `set*` コマンドで設定するか、config を手動編集してください。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `bomberman.admin` | OP | set系・stop・status・reload・setsign・removesign などの設定/管理、看板の破壊 |

!!! note "join / leave / start は権限不要"
    `/bomberman join`・`leave`・`start` は権限チェックがなく **全プレイヤーが実行可能** です（看板と同等）。設定・管理コマンドのみ `bomberman.admin` が必要です。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/bomberman join` / `leave` / `start` | 全員 | 参加 / 退出 / 開始（看板と同等） |
| `/bomberman stop` | `bomberman.admin` | ゲームを強制終了（コマンドブロック/コンソール可） |
| `/bomberman status` | `bomberman.admin` | 設定・進行状況を表示 |
| `/bomberman reload` | `bomberman.admin` | config 再読み込み＋看板の再ロード |
| `/bomberman set... / maxplayers` | `bomberman.admin` | 各種設定（前述） |

## トラブルシューティング

??? failure "ゲームが開始できない"
    開始には **最小開始人数（既定2人・`setminplayers` で変更可）以上** の参加と、**ロビー・初期スポーン・フィールド（1/2）・スポーン地点（人数分）** がすべて設定されている必要があります。`/bomberman status` で不足項目を確認してください。

??? failure "スポーン地点でプレイヤーが落ちる／埋まる"
    フィールド生成で床・障害物の高さが決まるため、`setspawn` は **フィールドの升目上（足元の段）に立って** 設定してください。フィールド外や高さがずれた位置だと落下・埋まりの原因になります。

??? failure "サドンデスの効果が薄い"
    外周（元々の硬い壁）からせり上がるため、最初の数リングは壁の上書きになります。フィールドが大きいほど中央が詰まるまで時間がかかります。`setsuddendeath` の秒数やフィールドサイズで調整してください。

??? failure "アナウンスが一部のプレイヤーに見えない"
    各種アナウンスはロビー参加者に送られます。観戦のみ（未参加）のプレイヤーには表示されない設計です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Bomberman 概要へ](index.md){ .md-button }
