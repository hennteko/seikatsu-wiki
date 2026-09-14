<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# PowerWash ― OP・運営ガイド { .page-op #powerwash-op }

PowerWash（協力洗浄ミニゲーム）の現在実装済み（Phase 1）のセットアップ・config をまとめます。

!!! warning "現在開発中（Phase 1）です"
    実装済みのコマンドは **ステージ作成・フィールド／スポーン設定・セル生成（scan）・汚れ設定・設定ツール・テスト用洗浄機配布・status・reload** です。`join`/`start`/`shop`/`hint`/`list` などのプレイループ系は **Phase 2 以降** で追加予定です。config には将来のフェーズ用のキーもあらかじめ用意されていますが、Phase 1 で実際に読み込まれるのは `resolution` / `max-cells` / `washer.base-*` のみです。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | PowerWash |
| バージョン | 0.1.0 |
| メインコマンド | `/pw`（エイリアス `/powerwash`） |
| api-version | 1.21 |
| 作者 | へんりー |
| 設定ファイル | `plugins/PowerWash/config.yml` |
| 権限ノード | `powerwash.admin`（既定OP） |

## セットアップ手順（Phase 1）

ステージは「領域を選択 → 作成 → スキャンでセル生成 → 汚れ設定」の流れで用意します。

```text title="領域選択斧を取得（左クリック=角1／右クリック=角2）"
/pw wand
```

```text title="ステージを作成（選択中の範囲があれば反映）"
/pw create <識別子>
```

```text title="フィールド範囲を現在地で設定（角1／角2）"
/pw setfield <識別子> 1
/pw setfield <識別子> 2
```

```text title="ステージ開始位置を現在地で設定"
/pw setspawn <識別子>
```

```text title="汚れセルを自動生成（フィールド内の表面をセル化）"
/pw scan <識別子>
```

```text title="選択範囲のセルの汚れ種類を上書き"
/pw dirt <識別子> <種類>
```

```text title="（Phase1テスト用）高圧洗浄機を入手"
/pw testwasher
```

```text title="設定状況を確認（識別子省略で一覧）"
/pw status [識別子]
```

```text title="設定を再読み込み"
/pw reload
```

!!! note "セルとスキャン"
    `scan` は、フィールド範囲内の表面ブロックを **1面あたり `resolution^2` 個のセル** に分割して生成します。生成後は BlockDisplay で表示されます。1ステージあたりのセル数は `max-cells`（既定5000）で制限され、超過するスキャンは実行されず警告のみ表示されます。`resolution` を変更しても、既存のセルには遡及しません（再スキャンが必要）。

## config.yml 設定項目

### Phase 1 で有効なキー

| キー | 既定値 | 説明 |
|---|---|---|
| `resolution` | 2 | 1 / 2 / 4。1面あたりの分割数（`resolution^2`）。scan後の変更は既存セルに遡及しない |
| `max-cells` | 5000 | 1ステージあたりのセル上限。超過するscanは実行せず警告 |
| `washer.material` | IRON_HOE | 高圧洗浄機のアイテム |
| `washer.custom-model-data` | 1001 | 高圧洗浄機のカスタムモデルデータ |
| `washer.base-power` | 1.0 | 洗浄の基本出力 |
| `washer.base-radius` | 0.5 | 水流の基本半径 |
| `washer.base-range` | 5.0 | 水流の基本射程 |
| `performance.particle-tick-interval` | 3 | パーティクルの描画間隔（tick） |
| `performance.display-spawn-per-tick` | 200 | scan後にBlockDisplayを生成する1tickあたりの上限 |

### 将来のフェーズ用（Phase 1 では未読込）

`washer.nozzle`（ノズル：jet/fan）・`detergent`（洗剤）・`reward`（報酬）・`upgrade`（出力/半径/射程のアップグレード）・`hint`（ヒント）・`admin-tool`（OP用設定ツール）・`lobby-spawn`／`default-spawn`／`sign`／`leave-sign`／`start-sign`（地点・看板）は、将来のフェーズで使用するために **キー名だけ先行して確保** されています。現時点では読み込まれません。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/pw wand` | 領域選択斧を取得 |
| `/pw create <識別子>` | ステージを作成 |
| `/pw setfield <識別子> <1\|2>` | フィールド範囲を設定 |
| `/pw setspawn <識別子>` | 開始位置を設定 |
| `/pw scan <識別子>` | 汚れセルを自動生成 |
| `/pw dirt <識別子> <種類>` | 選択範囲の汚れ種類を上書き |
| `/pw testwasher` | テスト用の高圧洗浄機を入手 |
| `/pw status [識別子]` | 設定状況を確認（全員可） |
| `/pw reload` | 設定を再読み込み |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `powerwash.admin` | OP | セットアップ系コマンド（wand/create/setfield/setspawn/scan/dirt/reload/testwasher） |

`/pw status`・`/pw help` は権限不要で全員が使えます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← PowerWash 概要へ](index.md){ .md-button }
