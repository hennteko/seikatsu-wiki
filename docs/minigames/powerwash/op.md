<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# PowerWash ― OP・運営ガイド { .page-op #powerwash-op }

PowerWash（協力洗浄ミニゲーム）の導入・セットアップ・config をまとめます。

!!! note "実装状況（Phase 2）"
    **参加・開始のプレイループ**（`join`/`leave`/`start`）・**制限時間**・**ロビー**・**看板** に対応しています。加えて Phase 1 のステージ作成・セル生成（scan）・汚れ設定・設定ツールも利用できます。`shop`/`hint`/お金・アップグレード・洗剤などは **今後のフェーズ** で追加予定で、config にキーだけ先行して用意されています（現時点では読み込まれません）。

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

## ロビー・看板の設定（Phase 2）

プレイループ用に、共通ロビーと参加・離脱・開始の看板を設定します。

```text title="共通ロビー地点を設定"
/pw setlobby
```

```text title="初期スポーン（離脱時の戻り先）を設定"
/pw setstartspawn
```

```text title="参加／離脱／開始の看板を登録（看板を見て実行）"
/pw setsign join
/pw setsign leave
/pw setsign start <識別子>
```

```text title="視線先の看板の登録を解除"
/pw setsign delete
```

!!! success "看板は複数設置できます"
    参加（`sign`）・離脱（`leave-sign`）看板は複数設置でき、開始（`start-sign`）看板は **識別子ごと** に複数設置できます。座標は config に自動保存されます。

## セットアップ手順（ステージ作成）

ステージは「領域を選択 → 作成 → スキャンでセル生成 → 汚れ設定」の流れで用意します。

```text title="領域選択斧を取得（左クリック=角1／右クリック=角2）"
/pw wand
```

```text title="ステージを作成（選択中の範囲があれば反映）"
/pw create <識別子>
```

```text title="フィールド範囲：ワンドの選択範囲をそのまま反映（角番号を省略）"
/pw setfield <識別子>
```

```text title="フィールド範囲：現在地を角1に設定"
/pw setfield <識別子> 1
```

```text title="フィールド範囲：現在地を角2に設定"
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

```text title="選択範囲の汚れセルを削除（面を省略すると全方向）"
/pw removecells <識別子>
```

```text title="選択範囲の汚れセルを面指定で削除（up/down/north/south/east/west）"
/pw removecells <識別子> <面>
```

```text title="テスト用の高圧洗浄機を入手（未参加の管理者のみ・進行中でないステージで試用可）"
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

## 汚れの種類（`/pw dirt` で指定）

`/pw scan` は元ブロックの種類から汚れ種類を自動割り当てします。`/pw dirt <識別子> <種類>` で選択範囲のセルを任意の種類に上書きできます。種類と落としにくさ（HP倍率）は `dirt-types.yml` で定義されています。

| 種類（指定名） | 表示名 | HP倍率 | 必要洗浄力Lv | 自動割り当ての例 |
|---|---|---|---|---|
| `MUD` | 泥（既定） | 1.0 | 0 | 土・粗い土・農地・ポドゾル |
| `MOSS` | 苔・サビ | 2.0 | 0 | 苔ブロック・苔石・酸化した銅系 |
| `OIL` | 油汚れ | 4.0 | 0 | 黒色コンクリート・黒曜岩系・ネザーラック・石炭ブロック |
| `STUBBORN` | 頑固な汚れ | 5.0 | 3 | 黒曜石・古代の瓦礫・灰色コンクリート |

!!! note "必要洗浄力レベル"
    `STUBBORN`（頑固な汚れ）は必要洗浄力レベル3が設定されています。洗浄力レベルを上げる強化（アップグレード）は今後のフェーズで追加予定のため、現時点で高難度セルを配置する場合はご注意ください。自動割り当てで `STUBBORN` になるブロック（黒曜石など）も同様です。

## config.yml 設定項目

### 有効なキー（Phase 1〜2）

| キー | 既定値 | 説明 |
|---|---|---|
| `resolution` | 2 | 1 / 2 / 4。1面あたりの分割数（`resolution^2`）。scan後の変更は既存セルに遡及しない |
| `max-cells` | 5000 | 1ステージあたりのセル上限。超過するscanは実行せず警告 |
| `skip-covered-faces` | true | scan時、葉・ガラス・カーペット・ハーフブロック等で完全に覆われた面（見えない／洗えない汚れ）のセルを作らない |
| `lobby-spawn` | null | 共通ロビー地点（`/pw setlobby`） |
| `default-spawn` | null | 初期スポーン（離脱時の戻り先。`/pw setstartspawn`） |
| `sign` / `leave-sign` / `start-sign` | リスト | 参加／離脱／開始看板の座標（`/pw setsign` で自動保存） |
| `session.time-options-minutes` | `[5,10,15,20,30]` | 時間選択GUIに出す分数の候補（「無制限」は別ボタンで常設） |
| `session.max-minutes` | 180 | `/pw start <識別子> <分>` で指定できる分の上限（0＝上限なし） |
| `washer.material` | IRON_HOE | 高圧洗浄機のアイテム |
| `washer.custom-model-data` | 1001 | 高圧洗浄機のカスタムモデルデータ |
| `washer.base-power` | 1.0 | 洗浄の基本出力 |
| `washer.base-radius` | 0.5 | 水流の基本半径 |
| `washer.base-range` | 5.0 | 水流の基本射程 |
| `performance.particle-tick-interval` | 3 | パーティクルの描画間隔（tick） |
| `performance.display-spawn-per-tick` | 200 | scan後にBlockDisplayを生成する1tickあたりの上限 |

### 将来のフェーズ用（現時点では未読込）

`washer.nozzle`（ノズル：jet/fan）・`detergent`（洗剤）・`reward`（報酬）・`upgrade`（出力/半径/射程のアップグレード）・`hint`（ヒント）・`admin-tool`（OP用設定ツール）は、将来のフェーズで使用するために **キー名だけ先行して確保** されています。現時点では読み込まれません。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/pw wand` | 領域選択斧を取得 |
| `/pw create <識別子>` | ステージを作成 |
| `/pw setfield <識別子> <1\|2>` | フィールド範囲を設定 |
| `/pw setspawn <識別子>` | 開始位置を設定 |
| `/pw scan <識別子>` | 汚れセルを自動生成 |
| `/pw dirt <識別子> <種類>` | 選択範囲の汚れ種類を上書き |
| `/pw removecells <識別子> [面]` | 選択範囲の汚れセルを削除（面: up/down/north/south/east/west／進行中は不可） |
| `/pw setlobby` / `setstartspawn` | 共通ロビー／初期スポーンを設定 |
| `/pw setsign <join\|leave\|start <識別子>\|delete>` | 看板を設定／解除 |
| `/pw testwasher` | テスト用の高圧洗浄機を入手 |
| `/pw status [識別子]` | 設定・洗浄状況を確認（全員可） |
| `/pw reload` | 設定を再読み込み |
| `/pw forceunlock <プレイヤー> [confirm]` | 参加中フラグの強制解除・退避データの復元（コンソール可） |

プレイヤー用（全員可）は `/pw join`・`/pw leave`・`/pw start <識別子> [分]`・`/pw status`・`/pw help` です。

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `powerwash.admin` | OP | セットアップ系（wand/create/setfield/setspawn/scan/dirt/removecells/setlobby/setstartspawn/setsign/reload/testwasher/forceunlock） |

`/pw join`・`/pw leave`・`/pw start`・`/pw status`・`/pw help` は権限不要で全員が使えます。

---

## 全ゲーム共通の改修（2026-09）

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ゲーム名】名前」になり、離脱で元に戻ります（表示名は config の `display-name`）。
- **参加/離脱の全体告知** … 参加・離脱時にサーバー全体へ「【ゲーム名】名前 が参加しました (N人)」等を通知します。
- **同時参加は1ゲームまで** … 他ゲームに参加中は参加が拒否されます（「【○○】に参加中です」）。異常で参加ロックが残った場合はOPが `/<コマンド> forceunlock <プレイヤー>` で解除できます。
- **退避データのファイル保存** … ロビー入場時に退避した所持品を `plugins/<プラグイン>/vault/<UUID>.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します（退避・復元は各1回、試合終了時は復元しません）。PowerWash は旧 `players.yml` を起動時に vault へ移行します（旧ファイルは `players.yml.migrated` へ改名）。

### 追加された config キー

| キー | 説明 |
|---|---|
| `display-name` | ゲーム表示名（「【…】」の中身） |
| `messages.join-broadcast` | 参加の全体告知文 |
| `messages.leave-broadcast` | 離脱の全体告知文 |
| `messages.already-in-other-game` | 他ゲーム参加中に拒否したときの文言 |

!!! note "config は自動で追記されるようになりました"
    起動時（`reload` 対応プラグインは reload 時も）に、`config.yml`（PowerWash は `messages.yml` も）へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は `.bak` を保存）。座標・看板・会場・ステージなどのデータ領域は補完対象外です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← PowerWash 概要へ](index.md){ .md-button }
