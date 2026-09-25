<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 異能バトル（Inoubattle） ― OP・運営ガイド { .page-op #inoubattle-op }

異能バトルの導入・設定ツール・地点／フェーズ／看板設定・config・権限・管理コマンドをまとめます。地点・看板は設定ツール（`/inou wand`）またはコマンドで登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Inoubattle |
| メインコマンド | `/inou` |
| api-version | 26.1.2 |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Inoubattle/config.yml` |
| 権限ノード | `inou.admin`（既定OP） |

## 導入手順

1. ビルドした `Inoubattle` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される。
3. `/inou wand` で設定ツールを受け取り、初期スポーン・ロビー・プレイエリア範囲を設定する。
4. 参加・離脱・開始の看板を設置する（ステージ識別子ごと）。
5. `/inou status` で設定状況を確認する。

## 設定ツール（`/inou wand`）

スナイパーバトロワの設定ツールと同じ操作感で、見ている場所へ直接セットアップできます（ブレイズロッド・OP専用）。

| 操作 | 動作 |
|---|---|
| 左クリック（ブロック） | プレイエリアの角1を設定 |
| 右クリック（ブロック） | プレイエリアの角2を設定 |
| 右クリック（看板） | 選択中の看板モードを適用 |
| 右クリック（空中） | 現在の設定状況を表示 |
| しゃがみ＋左クリック | 現在地を初期スポーン／ロビーに設定 |
| しゃがみ＋右クリック | **設定メニューGUIを開く**（フェーズ表の編集など） |

!!! note "プレイエリア＝WorldBorderの算出元"
    設定ツールで指定したプレイエリアの範囲が、各フェーズのWorldBorder（安全地帯）とエリア判定の基準になります。フェーズ表（範囲・待機秒・縮小秒・ケアパケ投下タイミング）は設定メニューGUIで編集でき、初期値は config の `phases` が使われます。

## 看板の設置

```text title="参加看板を登録（看板を見て実行）"
/inou setsign join
```

```text title="離脱看板を登録"
/inou setsign leave
```

```text title="開始看板を登録（ステージ識別子ごと・複数マップ対応）"
/inou setsign start <識別子>
```

```text title="視線先の看板の登録を解除"
/inou setsign delete
```

!!! success "看板は複数設置できます"
    参加・離脱・開始の各看板を複数設置できます（開始看板はステージ識別子ごと）。保存形式は `signs.join`/`leave`/`start.<識別子>` のリストです。フェーズ表は **ステージ識別子ごと** に持てるので、マップによって規模を変えられます。

## config.yml 設定項目

### 人数・体力・弓

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 1 | 最低人数（練習用に1人開始可・0のみ拒否） |
| `max-players` | 24 | 最大参加人数 |
| `max-hearts` | 3 | 体力（ハート数。弓の被弾は常に固定1ダメージ） |
| `bow.reload-seconds` | 5 | 矢の初期リロード秒数 |
| `bow.reload-min-seconds` | 3 | 強化後の最短リロード秒数 |
| `bow.max-arrows` | 1 | 初期の矢の最大所持数 |
| `bow.max-arrows-cap` | 3 | 強化後の矢の最大所持数の上限 |
| `bow.hits-per-upgrade` | 2 | 1段階強化に必要なヒット数 |
| `bow.max-upgrade-hits` | 8 | 最大強化になるヒット数 |

### フェーズ・安全地帯

| キー | 既定値 | 説明 |
|---|---|---|
| `phases` | 300/300/150/105/63/31 のリスト | 各フェーズの `size`（一辺m）/`wait`（待機秒）/`shrink`（縮小秒）/`carepkg`（投下オフセット秒のリスト） |
| `final-phase.size` | 10 | 最終フェーズの範囲 |
| `final-phase.drift-amount` | 3 | 最終フェーズで中心が移動する量（m） |
| `final-phase.drift-interval-seconds` | 15 | 中心移動の間隔（秒） |
| `border.outside-damage` | 1.0 | 圏外での1回あたりダメージ |
| `border.outside-damage-interval-seconds` | 1 | 圏外ダメージの間隔（秒） |
| `border.warn-before-seconds` | `[30, 10]` | 縮小の予告タイミング（秒前） |

!!! note "フェーズ切替で全員1回復"
    フェーズが切り替わるたびに全員の体力が1回復します。最終フェーズは中心が `drift-interval-seconds` ごとに `drift-amount` だけランダムに移動します（屋内で即詰みにならないよう移動量に上限あり）。

### ケアパケ・観戦

| キー | 既定値 | 説明 |
|---|---|---|
| `carepackage.claim-limit` | 3 | 1つのケアパケで回収できる人数（先着） |
| `carepackage.item-pool` | 回復スプラッシュ×1 | 中身の抽選プール（`material`/`potion`/`weight`/`min`/`max`。重み方式） |
| `spectator.mode` | auto | `auto`＝統合版だけゴースト観戦／`ghost`＝全員ゴースト／`vanilla`＝全員バニラ観戦 |
| `spectator.chat-isolate` | true | 観戦者チャットを観戦者間のみに制限 |

### 異能の有効/無効（`abilities`）

`false` にした異能は次の試合の抽選プールから除外されます（進行中の試合には影響しません）。`/inou setability <能力名> <on|off>` でも切り替えられます。

!!! info "v1で有効（実装済み）の異能"
    既定で有効なのは **疾走（sprint）／隠密（stealth）／速射（rapidfire）／探知（detect）／鑑定（appraise）／追跡（track）／強靭（toughness）** の7種です。その他の異能（跳躍・鉤縄・狙撃・無敵・障壁・背水・捕食・閃光・縮身・巨体・変化・複製・反響・魔眼 など）は **未実装（次回以降）** で、config 上は `false` になっています。実装が進んだら `true` にして有効化できます。

### ランダムイベント

| キー | 既定値 | 説明 |
|---|---|---|
| `random-event.enabled` | true | ランダムイベントの自動抽選を有効化 |
| `random-event.interval-seconds` | 90 | 抽選の間隔（秒） |
| `random-event.chance-percent` | 30 | 抽選時に発動する確率（%） |

イベントは `/inou event <名前>`（OP限定）で手動発火もできます。

### ロビー共通インベントリ（`lobby-inventory`）

ロビー入退室時に所持品を退避・復元する共通システムです（`enabled`/`gamemode`/`clear-effects`/`reset-health`/`reset-exp`/`expire-days`）。

!!! note "自動生成される領域（手動編集不要）"
    `default-spawn` / `lobby-spawn` / `stages`（フィールド範囲）/ `signs`（看板）は設定ツール・コマンドで自動保存されます。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/inou wand` | 設定ツールを受け取る |
| `/inou setstartspawn` | 初期スポーンを設定 |
| `/inou setlobby` | ロビーを設定 |
| `/inou setsign <join\|leave\|start <識別子>\|delete>` | 看板を設定／解除 |
| `/inou setability <能力名> <on\|off>` | 異能の有効／無効を切替 |
| `/inou abilitylist` | 異能の有効／無効一覧を表示 |
| `/inou setmin <人数>` / `setmax <人数>` | 最低／最大人数を設定 |
| `/inou event <名前>` | ランダムイベントを手動発火 |
| `/inou stop` | ゲームを強制停止 |
| `/inou reset` | 残留物を完全消去してIDLEへ |
| `/inou reload` | config を再読み込み |
| `/inou status` | 設定状況・現在の状況を確認（全員可） |

プレイエリア範囲・フェーズ表は **設定ツール（`/inou wand`）とGUI** で編集します（専用コマンドではなくツール操作）。

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `inou.admin` | OP | 設定ツール・地点／看板設定・異能ON/OFF・イベント発火・強制停止など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/inou join`・`/inou leave`・`/inou start <識別子>`・`/inou status` は権限チェックがなく、全プレイヤーが使えます（`start` はコンソール／コマンドブロックからも可）。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/inou status` を確認してください。プレイエリア範囲（設定ツールの角1/2）とロビーが設定され、ロビーに最低人数以上いる必要があります。未設定でもフォールバックしますが警告が出ます。

??? failure "特定の異能が配られない"
    その異能が `abilities` で `false`（未実装含む）になっていないか、`/inou abilitylist` で確認してください。v1で実装済みなのは7種のみです。

??? failure "試合後に残留物がある・異常終了した"
    `/inou reset` を実行してください。障壁などの設置物・WorldBorderを含めて完全消去し、IDLEへ戻します。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 異能バトル 概要へ](index.md){ .md-button }
