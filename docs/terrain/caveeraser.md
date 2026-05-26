<div class="audience-banner op">🛠️ OP・運営向けページ — CaveEraser は管理者（OP）専用の整地プラグインです。</div>

# CaveEraser ― OP・運営ガイド { .page-op }

<span class="badge custom">自作プラグイン</span>

岩盤整地（プレイヤーが地下を空洞化していく整地スタイル）で残ってしまう **水中洞窟（水と接した自然洞窟）** を自動検出し、指定ブロックで埋め立てるための整地補助プラグインです。地形を一括変換する強力なツールのため、**全権限が default false** に設定されており、明示的に権限を付与された OP のみが使用できます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | CaveEraser |
| バージョン | `${project.version}`（pom 連動） |
| 種別 | 自作プラグイン（へんりー作） |
| 想定 API | `26.1.2` |
| 主なコマンド | `/caveerase`（エイリアス `/ce`） |
| 依存 | なし（softdepend: `CasinoPlugin`） |
| 設定ファイル | `plugins/CaveEraser/config.yml` |

!!! warning "`/ce` エイリアスが CustomEnchants と衝突します"
    同じ整地・地形改変カテゴリの **CustomEnchants も `/ce` をエイリアス** として使用します。プラグインのロード順により、どちらか片方の `/ce` が無効化されます。CaveEraser を使用する際は、衝突しない正式名の **`/caveerase`** を使ってください（このページのコマンド表記もすべて `/caveerase` で統一しています）。

## 概要

CaveEraser は以下のフローで動作します。

1. **scan**（検出）: プレイヤーの周囲を走査し、水ブロックを起点に **水面下の空気/水で繋がった連結領域**（＝水中洞窟）を BFS で抽出。
2. **list / info**: 検出済み洞窟の一覧と詳細（中心座標・体積・深さ・推定コスト）を表示。
3. **fill / auto**: 検出した洞窟の空気部分を、許可されたブロック（既定 `STONE` など）で埋め立て。
4. **chunkfill**: 洞窟検出をスキップし、**現在のチャンクを中心に半径指定で水・溶岩・空気を一括埋め立て**（粗整地用）。

検出は自然洞窟をターゲットにしているため、岩盤まで掘り尽くした「クリア済みの広大な空間」を誤検出しないよう、**体積上限**（既定 100,000 ブロック）と **最小水ブロック数**（既定 1）で除外する仕組みになっています。

## 導入手順

1. `CaveEraser-<version>.jar` を `plugins/` に配置。
2. サーバー起動（または `/reload`）で `plugins/CaveEraser/config.yml` が生成されます。
3. **コスト機能を使う場合のみ** CasinoPlugin（bank モジュール）を導入し、`EmeraldAPI.isReady()` が true になっていることを確認。CasinoPlugin が無い場合、コスト判定は自動でスキップされ、埋め立て自体は動作します。
4. 権限管理プラグインで OP に `caveeraser.*`（または個別ノード）を付与します。

## CasinoPlugin（softdepend）連携について

- CasinoPlugin は **softdepend**（任意依存）です。未導入でもプラグイン本体は動作します。
- CasinoPlugin が導入され bank モジュールが起動すると、`jp.casinoplugin.api.EmeraldAPI` がリフレクション経由で検出され、`fill` / `auto` / `chunkfill` 実行時にエメラルドを引き落とします。
- EmeraldAPI は **long ベース**（整数）のため、`per-block: 0.1` のように小数を設定していても、**合計コストは引き落とし時に四捨五入**（`Math.round`）されます。
- CasinoPlugin が無効・bank 未起動の場合、コスト判定はスキップされ、`withdraw` は常に成功扱いになります（料金を取らず通過）。

## config.yml 設定項目

### settings（スキャン・処理）

| キー | 既定 | 説明 |
|---|---|---|
| `max-scan-radius` | `200` | `scan` / `auto` で指定できる最大半径（ブロック） |
| `default-scan-radius` | `50` | 半径未指定時の既定値 |
| `default-fill-material` | `STONE` | `fill` / `auto` / `chunkfill` で素材未指定時のブロック |
| `async-processing` | `true` | 非同期処理を有効化。false にすると同期一括処理（大きな洞窟でラグの原因になる） |
| `blocks-per-tick` | `1000` | 1 tick あたりに変換するブロック数（小さいほど低負荷・低速） |
| `min-cave-volume` | `10` | 洞窟として認識する最小ブロック数 |
| `max-cave-volume` | `100000` | 洞窟として認識する最大ブロック数（広大な整地済み空間の誤検出防止） |
| `max-caves-per-player` | `50` | 1 プレイヤーが保持できる検出結果の上限 |

### detection（検出制限）

| キー | 既定 | 説明 |
|---|---|---|
| `max-distance-from-water` | `-1` | 水ブロックからの最大距離。`-1` で無効 |
| `scan-min-y` | `-64` | 走査の Y 下限 |
| `scan-max-y` | `320` | 走査の Y 上限 |
| `require-water-connection` | `false` | true で水と直接繋がった洞窟のみ検出 |
| `min-water-blocks` | `1` | 洞窟内に必要な最小水ブロック数（整地済み空間との区別の主要条件） |

### cost（コスト・要 CasinoPlugin）

| キー | 既定 | 説明 |
|---|---|---|
| `enabled` | `true` | コスト機能の有効/無効 |
| `per-block` | `0.1` | 1 ブロックあたりのエメラルド料金 |
| `volume-discount` | `1000:0.05` ほか | 体積に応じた割引（しきい値: 割引率）。`1000` で 5%、`5000` で 10%、`10000` で 15%、`50000` で 20% |

### materials（許可素材）

`materials.allowed-fills` に列挙されたブロックのみ、`fill` / `auto` / `chunkfill` の素材引数に指定できます。既定値:

`STONE`, `COBBLESTONE`, `ANDESITE`, `DIORITE`, `GRANITE`, `DEEPSLATE`, `TUFF`

### chunk-fill（チャンク埋め立て）

| キー | 既定 | 説明 |
|---|---|---|
| `max-radius` | `5` | チャンク半径の上限（`0` で実行チャンクのみ） |
| `default-radius` | `0` | 半径未指定時の既定値 |
| `min-y` / `max-y` | `-64` / `320` | 埋め立て対象の Y 範囲（実際の上限は **プレイヤーの Y - 1** に自動クランプされ、プレイヤー位置より上は変換されません） |
| `player-protection-radius` | `0` | 現状未使用（Y 座標による保護が代替） |
| `protected-blocks` | チェスト等の一覧 | 埋め立て時に変換しないブロック群（チェスト・各種シュルカーボックス・看板・額縁・スポナー・エンチャント台・ビーコン等） |

埋め立て対象になるブロックは **`WATER` / `LAVA` / `AIR` / `CAVE_AIR` のみ** で、それ以外（既存の地形・建造物）は変換されません。

### messages

`messages.prefix` 以下にチャット出力テンプレートが定義されています。`{radius}` `{count}` `{id}` `{blocks}` `{cost}` などのプレースホルダが利用可能。色コードは `&` 形式。

## サブコマンド

すべて `/caveerase <sub>` 形式。プレイヤー専用（コンソールからは実行不可）。

### `/caveerase scan [半径]`

プレイヤーを中心に周囲を走査して水中洞窟を検出します。半径未指定時は `default-scan-radius`、上限は `max-scan-radius`。非同期で実行され、完了時に検出件数と検出制限（体積・Y 範囲・最小水ブロック数）が表示されます。検出結果はプレイヤーごとに保持され、`list` / `info` / `fill` で参照します。

- 必要権限: `caveeraser.scan`

### `/caveerase fill <id> [素材]`

`scan` で検出した洞窟（`id` は `list` で表示される番号）を埋め立てます。素材未指定時は `default-fill-material`、指定時は `materials.allowed-fills` に含まれていなければエラー。コスト機能が有効かつ CasinoPlugin が利用可能な場合、エメラルド残高を先に確認・引き落とししてから処理を開始します。完了後、対象洞窟は内部リストから削除されます。

- 必要権限: `caveeraser.fill`

### `/caveerase auto [半径] [素材]`

スキャンと埋め立てを **一括で連続実行** します。検出されたすべての洞窟の合計体積からコストを算出し、まとめて引き落とした上で 1 件ずつ順に埋め立てます。コストが残高不足の場合は処理開始前にキャンセル。

- 必要権限: `caveeraser.auto`

### `/caveerase chunkfill [半径] [素材]`

**洞窟検出を行わず**、現在いるチャンクを中心に **チャンク半径** 指定で広範囲を埋め立てます。半径 `0` で現在のチャンクのみ、上限は `chunk-fill.max-radius`（既定 5）。対象は `WATER` / `LAVA` / `AIR` / `CAVE_AIR` のみで、`protected-blocks` に含まれるブロック（チェスト・看板・ビーコン等）と **プレイヤーの Y 座標より上** は除外されます。実行前にプレビュー（変換予定ブロック数）が表示され、続けてコスト引き落とし→非同期で順次変換します。10,000 ブロックごとに進捗が出力されます。

- 必要権限: `caveeraser.chunkfill`

### `/caveerase list`

検出済み洞窟の一覧を表示します。各エントリは `#ID - 体積 ブロック数 at (X, Y, Z) - 推定コスト` の形式。

- 必要権限: `caveeraser.use`

### `/caveerase info <id>`

指定 ID の洞窟の詳細（中心座標・体積・深さ・推定コスト）を表示します。

- 必要権限: `caveeraser.use`

### `/caveerase reload`

`config.yml` を再読み込みします。

- 必要権限: `caveeraser.reload`

## 権限ノード

| ノード | 既定 | 用途 |
|---|---|---|
| `caveeraser.use` | `false` | `list` / `info`（基本操作） |
| `caveeraser.scan` | `false` | `scan` |
| `caveeraser.fill` | `false` | `fill` |
| `caveeraser.auto` | `false` | `auto` |
| `caveeraser.chunkfill` | `false` | `chunkfill` |
| `caveeraser.reload` | `false` | `reload` |
| `caveeraser.*` | `false` | 上記すべてを子ノードとして付与 |

!!! danger "全権限が default false です"
    地形を不可逆に書き換えるツールのため、OP であっても **権限管理プラグインで明示的に付与しなければ動作しません**。CaveEraser 自体は **個別ノード単位で許可** することを推奨します（特に `chunkfill` は影響範囲が大きいので限定運用が安全）。

## トラブルシューティング

??? failure "`/ce` を実行しても別のプラグインが反応する"
    CustomEnchants と `/ce` エイリアスが衝突しています。**`/caveerase` を使用してください**。サーバーのプラグインロード順により、どちらの `/ce` が有効になるかが決まります。

??? failure "コマンドを実行できる権限がないと言われる"
    すべての権限が `default: false` です。`caveeraser.use` / `caveeraser.scan` / `caveeraser.fill` / `caveeraser.auto` / `caveeraser.chunkfill` / `caveeraser.reload` のうち必要なノード、またはまとめて `caveeraser.*` を権限管理プラグインで付与してください。

??? failure "コストが引かれない／引かれる額が想定と違う"
    - CasinoPlugin 未導入、または bank モジュール未起動（`EmeraldAPI.isReady()` が false）の場合、コスト判定はスキップされます。サーバーログに `CasinoPlugin not found. Cost features will be disabled.` または `CasinoPlugin (EmeraldAPI) integration enabled!` のいずれかが出ているはずです。
    - EmeraldAPI は long ベースのため、`per-block` を小数に設定していても **合計コストは四捨五入** されてから引き落とされます。

??? failure "scan しても水中洞窟が見つからない"
    - 半径が小さすぎる可能性。`max-scan-radius`（既定 200）まで広げて試してください。
    - 検出条件: `min-cave-volume`（既定 10）以上、`max-cave-volume`（既定 100,000）以下、内部に `min-water-blocks`（既定 1）以上の水を含む、`scan-min-y` 〜 `scan-max-y` の範囲。クリア済みの巨大空間は意図的に弾かれます。

??? failure "`既に処理中です` と表示されて操作できない"
    同一プレイヤーの scan / fill / auto / chunkfill が並列実行されないようロックされています。前の処理が完了するまで待ってください。サーバー再起動でロックは解除されます（`onDisable` で全タスクキャンセル）。

??? failure "chunkfill で自分の足元や上の建築が壊れた／壊れない"
    - `chunkfill` は **プレイヤーの Y - 1 まで** しか埋めません（プレイヤー位置と頭上は安全）。
    - チェスト・各種シュルカー・看板・額縁・スポナー・エンチャント台・ビーコン・醸造台などは `protected-blocks` で保護されています。それ以外の建材ブロック（石・木材・ガラス等）は **保護対象外** なので、埋め立てたい範囲外で実行しないでください。

??? warning "`async-processing: false` の挙動"
    同期処理に切り替えると、巨大洞窟・大規模 chunkfill の際にサーバーがフリーズする恐れがあります。通常は既定の `true` のまま運用してください。
