<div class="audience-banner op">🛠️ OP・運営向けページ — TerrainTools は整地ツール統合プラグインです（整地系コマンドは OP 専用。`/spawn` のみ全プレイヤー向け）。</div>

# TerrainTools ― OP・運営ガイド { .page-op }

<span class="badge done">公開中 / 統合プラグイン</span>

岩盤置換・整地アシスト・水中洞窟埋立・スポーン移動を **1 つの jar にまとめた整地ツールパック** です。旧 BedrockReplacer / Seitiassist / CaveEraser の 3 プラグインを CasinoPlugin と同じ ModuleRegistry パターンで束ね、**コマンド体系・権限ノード・config を互換** で引き継ぎつつ、新たに `spawn` モジュール（`/spawn`）を追加しています。利用者の操作感は従来どおりのまま、サーバー側の管理対象を削減できます。

!!! info "統合プラグインです"
    本プラグインは旧 3 プラグイン（**BedrockReplacer** + **Seitiassist** + **CaveEraser**）を統合した後継版です。各機能は **モジュール**（`bedrock` / `seiti` / `cave` / `spawn`）として独立しており、`config.yml` のフラグで個別に ON/OFF できます。旧プラグインは廃止予定のため、新規導入・更新ではこちらを使用してください。`spawn` モジュールは本統合版で新規追加された **全プレイヤー向け** 機能です。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | TerrainTools |
| バージョン | 1.0.0 |
| 種別 | 自作プラグイン（統合・へんりー作） |
| メインクラス | `jp.henry.terraintools.TerrainTools` |
| 構成モジュール | `bedrock` ／ `seiti` ／ `cave` ／ `spawn` の 4 モジュール |
| API バージョン | 26.1.2（Paper 1.26.x 想定） |
| 統合管理コマンド | `/terraintools`（エイリアス `/tt`） |
| 既存コマンド | `/bedrockreplacer`（`/br`, `/bedrock`）／ `/sa`（`/seiti`）／ `/caveerase`（`/ce`）／ `/spawn` |
| 依存 | なし（`softdepend: CasinoPlugin` ― cave モジュールのコスト機能で利用） |
| 設定ファイル | `plugins/TerrainTools/config.yml` ／ `plugins/TerrainTools/modules/*.yml` |
| 生成 jar | `target/TerrainTools-1.0.0.jar` |

## 導入手順

1. プロジェクトをビルドする。
    ```
    cd "C:\Users\hennt\IdeaProjects\【1.26.2】\【1.26.2】生活鯖WIKI\【1.26.2】TerrainTools\TerrainTools"
    mvn clean package
    ```
2. `target/TerrainTools-1.0.0.jar` を `plugins/` フォルダに配置する。
3. サーバーを起動すると、`plugins/TerrainTools/config.yml` と `plugins/TerrainTools/modules/{bedrock,seiti,cave,spawn}.yml` が自動展開される（`ConfigUtil` の自動展開・デフォルト fallback）。
4. 必要に応じて各モジュールの設定を編集し、`/tt reload` で再読み込みする。
5. 既存ノード（`bedrockreplacer.*` / `seitiassist.*` / `caveeraser.*`）は **plugin.yml にそのまま残存** しているため、LuckPerms 等の権限設定は無修正で動作する。

## モジュール構成

4 つのモジュールはそれぞれ独立して読み込み・有効化されます。1 モジュールの初期化に失敗してもサーバー全体や他モジュールの動作には影響しません（`ModuleRegistry` で catch される）。

| モジュール ID | 旧プラグイン | 役割 | 主なコマンド |
|---|---|---|---|
| `bedrock` | BedrockReplacer | 最下層以外の岩盤を **深層岩** に置換 | `/br chunk` ／ `/br radius` ／ `/br status` |
| `seiti` | Seitiassist | 整地アシスト（wand / clean / pickaxe / megapickaxe / fluid / trash）＋ 連鎖破壊・スポナーアイテム化 | `/sa wand` ／ `/sa clean` ／ `/sa pickaxe` 他 |
| `cave` | CaveEraser | 水中洞窟検出・埋立、チャンク単位の粗整地 | `/caveerase scan` ／ `/caveerase fill` ／ `/caveerase chunkfill` |
| `spawn` | （新規） | スポーン移動／個人ワープ地点（全プレイヤー向け） | `/spawn` ／ `/spawn set` ／ `/spawn <番号>` |

bedrock ／ seiti ／ cave の各モジュールは旧プラグイン（BedrockReplacer ／ Seitiassist ／ CaveEraser）の挙動を継承していますが、seiti モジュールには **連鎖破壊（Vein Miner）** と **スポナーのアイテム化** が新規追加されています。`spawn` モジュールは本統合版で新たに追加された機能です。

### bedrock モジュール（旧 BedrockReplacer）

ワールド内の **最下層（`getMinHeight()`）以外にある岩盤** を **深層岩** に置き換える整地補助モジュールです。

- 対象ブロック： `BEDROCK` のみ。置換後： `DEEPSLATE`。
- スキャン範囲： 各チャンクの `World#getMinHeight()` 〜 `World#getMaxHeight() - 1`。
- **除外条件**： `Y == getMinHeight()` の岩盤は処理対象外（ワールド底抜け防止）。
- 処理速度： **1 tick あたり 1 チャンク**。10 チャンクごとに進捗が送信される。
- ネザー天井・床上層の岩盤撤去に有効。

### seiti モジュール（旧 Seitiassist）

整地作業を快適にするアシスト機能を提供します。

- **範囲削除の杖（wand / clean）** ― 木の斧で 2 点を選び、ダイヤモンドをコストに直方体を一括削除。
- **範囲採掘ツルハシ（pickaxe）** ― ダイヤツルハシで 1x1 ～ 7x7 の範囲採掘（右クリックでモード切替）。
- **超大範囲ツルハシ（megapickaxe）** ― ネザライトツルハシで 9x9 ～ 21x21 の超大範囲採掘（イベント報酬用）。
- **流体凝固アイテム（fluid）** ― パック氷（既定 `PACKED_ICE`）をオフハンドに持って採掘すると、周囲の水・溶岩を経験値消費で丸石・黒曜石に変換。
- **スマート・ゴミ箱（trash）** ― 拾った瞬間に指定マテリアルを消去し、代わりに経験値を獲得。
- **連鎖破壊（Vein Miner）※新規** ― 任意のツルハシ／斧を持って **スニーク中** に採掘すると、同種で連結しているブロックを一括破壊（コマンド不要）。ツルハシは同種ブロック全般、斧は木材系（既定 `LOGS`）を対象とし、`max-blocks` の上限・経験値コスト（`exp-per-block`）で制御。`blacklist` の `BEDROCK` / `AIR` は対象外。
- **スポナーのアイテム化（Spawner）※新規** ― シルクタッチ付き（既定）ツルハシでスポナーを破壊すると 100% アイテム化。湧く Mob の種類を保持し、再設置で同じ Mob が湧くよう復元（`spawner.preserve-mob-type`）。クリエイティブでは複製防止のためドロップしません。

岩盤はすべての破壊系機能から自動的に除外されます。`/sa` 配下のコマンドはすべて OP 専用ですが、配布済みの杖・ツルハシ・フィルタ等、および連鎖破壊・スポナーアイテム化は一般プレイヤーでも利用できます。

### cave モジュール（旧 CaveEraser）

岩盤整地で残ってしまう **水中洞窟（水と接した自然洞窟）** を BFS で検出し、許可ブロックで埋め立てるモジュールです。

- **scan** で水中洞窟を検出 → **list / info** で確認 → **fill / auto** で埋立。
- **chunkfill** は洞窟検出をスキップし、現在のチャンクを中心に半径指定で `WATER` / `LAVA` / `AIR` / `CAVE_AIR` を一括変換（粗整地用）。
- CasinoPlugin の **EmeraldAPI** が利用可能な場合、エメラルドを引き落としてからの実行となる（未導入時はコスト判定をスキップ）。
- 全権限が **`default: op`** のため、OP は標準でフル操作可能。一般プレイヤーに開放する場合は権限管理プラグインで個別付与してください。

### spawn モジュール（新規）

ワールドのスポーン地点への移動と、プレイヤーごとの **個人ワープ地点**（番号スロット）を提供する **全プレイヤー向け** モジュールです。

- **`/spawn`** ― 現在いるワールドのスポーン地点へ移動（視点はそのまま）。
- **`/spawn set <番号>`** ― 現在地を個人ワープ地点として登録（番号スロット `1` 〜 `max-points`、既定 5）。
- **`/spawn <番号>`** ― 登録済みの番号へワープ。
- **`/spawn del <番号>`** ― 登録地点を削除（`delete` / `remove` も可）。
- **`/spawn list`** ― 登録済み地点の一覧。
- すべて **プレイヤー専用**（位置情報が必要）。権限 `terraintools.spawn.use` は **`default: true`** で全プレイヤーが使用できます。
- 個人ワープ地点は `plugins/TerrainTools/data/spawn-points.yml` に UUID・スロット別で永続化されます。

## 設定ファイル

### `plugins/TerrainTools/config.yml`（メイン）

各モジュールの有効/無効を切り替えます。

```yaml
modules:
  bedrock:
    enabled: true   # 岩盤→深層岩への置換 (旧 BedrockReplacer)
  seiti:
    enabled: true   # 整地アシスト (旧 Seitiassist)
  cave:
    enabled: true   # 水中洞窟検出・埋立 (旧 CaveEraser)
  spawn:
    enabled: true   # スポーン/個人ワープ (/spawn)

debug:
  verbose: false    # 詳細ログ (現状未使用、将来拡張用)
```

| キー | 既定 | 説明 |
|---|---|---|
| `modules.bedrock.enabled` | `true` | bedrock モジュール（岩盤置換）の有効化 |
| `modules.seiti.enabled` | `true` | seiti モジュール（整地アシスト）の有効化 |
| `modules.cave.enabled` | `true` | cave モジュール（水中洞窟埋立）の有効化 |
| `modules.spawn.enabled` | `true` | spawn モジュール（スポーン/個人ワープ）の有効化 |
| `debug.verbose` | `false` | 詳細ログ（現状未使用） |

### `plugins/TerrainTools/modules/bedrock.yml`

旧 BedrockReplacer に config は存在しなかったため、**現状このファイルは将来拡張用のプレースホルダー** です。中身は空のままで OK（`BedrockModule#getModuleConfig()` 経由で参照可能）。

### `plugins/TerrainTools/modules/seiti.yml`

旧 `plugins/Seitiassist/config.yml` を移植し、新機能（連鎖破壊・スポナーアイテム化）のキーを追加したものです。主要キーを抜粋します。

| キー | 既定値 | 説明 |
|---|---|---|
| `pickup.drop-at-feet` | `true` | 採掘・連鎖破壊・スポナーのドロップをプレイヤー足元にまとめる |
| `wand.blocks-per-diamond` | `64` | `/sa clean` のダイヤ 1 個あたりの削除ブロック数 |
| `pickaxe.name` | `§6Bedrock Breaker` | 範囲採掘ツルハシの表示名 |
| `pickaxe.durability-cost` | `1` | 範囲採掘発動時に追加で乗る耐久消費 |
| `mega-pickaxe.name` | `§c§l✦ Mega Breaker ✦` | メガツルハシの表示名 |
| `mega-pickaxe.durability-cost` | `3` | メガツルハシ発動時に追加で乗る耐久消費 |
| `trash.exp-per-item` | `1` | ゴミ 1 個あたりの獲得経験値量 |
| `fluid.trigger-item` | `PACKED_ICE` | オフハンドのトリガーマテリアル名 |
| `fluid.exp-cost` | `2` | 流体 1 ブロック変換あたりの経験値コスト |
| `fluid.radius` | `2` | 流体凝固の効果半径（半径 2 なら 5x5x5） |
| `vein-miner.enabled` | `true` | 連鎖破壊機能の有効/無効 |
| `vein-miner.max-blocks` | `64` | 1 回の連鎖で破壊できる最大ブロック数（暴走・ラグ防止の上限） |
| `vein-miner.exp-per-block` | `1` | 1 ブロック連鎖破壊ごとの経験値コスト（`0` で経験値コストなし） |
| `vein-miner.connect-diagonals` | `false` | `true` で斜めを含む 26 方向連結、`false` で 6 方向のみ |
| `vein-miner.consume-durability` | `true` | 連鎖破壊時にツルハシ／斧の耐久を消費するか（耐久力考慮） |
| `vein-miner.blacklist` | `BEDROCK`, `AIR` | 同種でも連鎖対象から除外するブロック |
| `vein-miner.axe-enabled` | `true` | 斧 + スニークでの木材一括伐採を有効にするか |
| `vein-miner.axe-target-blocks` | `LOGS` | 斧の伐採対象（`LOGS` / `PLANKS` のタグ、または Material 名で個別指定） |
| `spawner.enabled` | `true` | スポナーアイテム化機能の有効/無効 |
| `spawner.require-silk-touch` | `true` | `true` でシルクタッチ必須（推奨）、`false` で任意のツルハシ可 |
| `spawner.preserve-mob-type` | `true` | `true` で湧く Mob 種類を保持し再設置で復元（表示名にも反映） |

トラッシュ機能のフィルタデータは `plugins/TerrainTools/modules/seiti/trash_data.yml` に UUID 別で保存されます。

### `plugins/TerrainTools/modules/cave.yml`

旧 `plugins/CaveEraser/config.yml` をそのまま移植したものです。代表的なキーのみ抜粋します。

| セクション | キー | 既定 | 説明 |
|---|---|---|---|
| `settings` | `max-scan-radius` | `200` | `scan` / `auto` の最大半径 |
| `settings` | `default-scan-radius` | `50` | 半径未指定時の既定値 |
| `settings` | `default-fill-material` | `STONE` | 素材未指定時の埋立ブロック |
| `settings` | `async-processing` | `true` | 非同期処理（false 推奨せず） |
| `settings` | `blocks-per-tick` | `1000` | 1 tick あたりの変換ブロック数 |
| `settings` | `min-cave-volume` ／ `max-cave-volume` | `10` ／ `100000` | 洞窟認識のブロック数範囲 |
| `detection` | `min-water-blocks` | `1` | 洞窟と判定する最小水ブロック数 |
| `cost` | `enabled` | `true` | コスト機能の有効/無効（要 CasinoPlugin） |
| `cost` | `per-block` | `0.1` | 1 ブロックあたりのエメラルド料金 |
| `materials.allowed-fills` | ― | `STONE`, `COBBLESTONE`, `ANDESITE`, `DIORITE`, `GRANITE`, `DEEPSLATE`, `TUFF` | 埋立に指定できる素材 |
| `chunk-fill` | `max-radius` | `5` | `chunkfill` のチャンク半径上限 |
| `chunk-fill` | `protected-blocks` | チェスト等の一覧 | 埋立時に変換しないブロック群 |

### `plugins/TerrainTools/modules/spawn.yml`

個人ワープ地点の設定です。1 キーのみで、登録できるスロット数を決めます。

| キー | 既定 | 説明 |
|---|---|---|
| `max-points` | `5` | 1 プレイヤーが登録できる個人ワープ地点の最大数（番号スロット `1` 〜 `max-points`） |

登録データは `plugins/TerrainTools/data/spawn-points.yml` に `players.<uuid>.<slot>` 形式で保存されます。

## コマンド一覧

### 統合管理 `/terraintools`（`/tt`）

TerrainTools 本体の管理用コマンドです。`terraintools.admin` 権限（既定 op）が必要。

| サブコマンド | 説明 |
|---|---|
| `/tt modules` | 各モジュールの読み込み状況・有効/無効を一覧表示 |
| `/tt reload` | `config.yml` および全モジュールの設定を再読み込み |
| `/tt version` | プラグインバージョンを表示 |

### bedrock モジュール（旧 BedrockReplacer）

| コマンド | 説明 |
|---|---|
| `/bedrockreplacer chunk` ／ `/br chunk` | 現在チャンクのみを処理（プレイヤー専用） |
| `/br radius <半径>` | 半径 1〜20 チャンクを一括処理（`(2r+1)²` チャンク） |
| `/br cancel` | 実行中の処理を中断 |
| `/br status` | 進捗・置換ブロック数を表示 |
| `/br world` | **無効化中**（警告のみ。`radius` を複数回実行で代替） |

### seiti モジュール（旧 Seitiassist）

`/sa` 自体の実行に `seitiassist.use`（既定 op）が必要です。

| コマンド | 説明 | 権限 |
|---|---|---|
| `/sa wand [player]` | 範囲消去の杖（木の斧）を入手 | `seitiassist.command.wand` |
| `/sa clean` | 杖で選択した直方体を削除（ダイヤ消費、プレイヤー専用） | `seitiassist.command.clean` |
| `/sa pickaxe [player]` | 範囲採掘ツルハシ（ダイヤ製、1〜7x7） | `seitiassist.command.pickaxe` |
| `/sa megapickaxe [player]` ／ `mpickaxe` ／ `mp` | メガツルハシ（ネザライト製、9〜21x21） | `seitiassist.command.megapickaxe` |
| `/sa fluid [player]` | 流体凝固アイテム（パック氷）を入手 | `seitiassist.command.fluid` |
| `/sa trash` | ゴミ箱フィルタ GUI を開く（プレイヤー専用） | `seitiassist.command.trash` |
| `/sa reload` | seiti モジュールの設定を再読み込み | `seitiassist.admin` |

### cave モジュール（旧 CaveEraser）

| コマンド | 説明 | 権限 |
|---|---|---|
| `/caveerase scan [半径]` | 周囲を走査して水中洞窟を検出 | `caveeraser.scan` |
| `/caveerase list` | 検出済み洞窟の一覧 | `caveeraser.use` |
| `/caveerase info <id>` | 洞窟の詳細（中心座標・体積・推定コスト） | `caveeraser.use` |
| `/caveerase fill <id> [素材]` | 指定洞窟を埋め立て | `caveeraser.fill` |
| `/caveerase auto [半径] [素材]` | スキャン＋埋立を一括実行 | `caveeraser.auto` |
| `/caveerase chunkfill [半径] [素材]` | チャンク単位で粗整地 | `caveeraser.chunkfill` |
| `/caveerase reload` | cave モジュールの設定を再読み込み | `caveeraser.reload` |

!!! warning "`/ce` エイリアスは CustomEnchants と衝突します"
    cave モジュールの `/ce` エイリアスは、CustomEnchants の `/ce` と競合します。プラグインのロード順により片方が無効化されるため、本プラグインでは **正式名 `/caveerase` の使用を推奨** しています。

### spawn モジュール（新規）

`/spawn` は **全プレイヤー**（`terraintools.spawn.use`、既定 `true`）が使用できます。すべてプレイヤー専用です。

| コマンド | 説明 |
|---|---|
| `/spawn` | 現在いるワールドのスポーン地点へ移動 |
| `/spawn set <番号>` | 現在地を個人ワープ地点に登録（`1` 〜 `max-points`、既定 5） |
| `/spawn <番号>` | 登録した番号の地点へワープ |
| `/spawn del <番号>` | 登録地点を削除（`delete` / `remove` も可） |
| `/spawn list` | 登録済み地点の一覧 |
| `/spawn help` | ヘルプを表示 |

## 権限ノード

統合管理用ノードのみ新規追加し、**旧 3 プラグインのノードはすべてそのまま維持** しています。LuckPerms 等で旧プラグイン向けに設定済みの権限は、無修正でそのまま動作します。

### 新規追加

| ノード | 既定 | 説明 |
|---|---|---|
| `terraintools.admin` | `op` | 統合管理コマンド `/terraintools`（`modules` / `reload` / `version`）の使用権限 |
| `terraintools.spawn.use` | `true` | `/spawn`（スポーン移動／個人ワープ）の使用権限。**全プレイヤー向け** |

### bedrock モジュール（既存維持）

| ノード | 既定 | 説明 |
|---|---|---|
| `bedrockreplacer.use` | `op` | `/bedrockreplacer` コマンド全体 |

### seiti モジュール（既存維持）

| ノード | 既定 | 説明 |
|---|---|---|
| `seitiassist.use` | `op` | `/sa` コマンド自体の実行権限（OP 専用） |
| `seitiassist.command.wand` | `op` | `/sa wand`（OP 配布用） |
| `seitiassist.command.clean` | `op` | `/sa clean`（OP デバッグ用。一般プレイヤーは杖シフト＋右クリックで代替） |
| `seitiassist.command.pickaxe` | `op` | `/sa pickaxe`（OP 配布用） |
| `seitiassist.command.megapickaxe` | `op` | `/sa megapickaxe`（イベント報酬用） |
| `seitiassist.command.fluid` | `op` | `/sa fluid`（OP 配布用） |
| `seitiassist.command.trash` | `op` | `/sa trash`（OP メンテ用。一般プレイヤーは `[ゴミ箱]` 看板で代替） |
| `seitiassist.admin` | `op` | `/sa reload` 等の管理者権限 |

### cave モジュール（既存維持）

| ノード | 既定 | 説明 |
|---|---|---|
| `caveeraser.use` | `op` | `list` / `info`（基本操作） |
| `caveeraser.scan` | `op` | `scan` |
| `caveeraser.fill` | `op` | `fill` |
| `caveeraser.auto` | `op` | `auto` |
| `caveeraser.chunkfill` | `op` | `chunkfill` |
| `caveeraser.reload` | `op` | `reload` |
| `caveeraser.*` | `op` | 上記すべて（子ノード一括付与） |

!!! danger "cave モジュールは地形を不可逆に書き換えます"
    特に `caveeraser.chunkfill` は影響範囲が大きく、誤実行による被害も大きくなります。OP 以外への開放は権限管理プラグインで限定運用を推奨します。

## 既存サーバーからの移行

!!! warning "既存サーバーからの移行"
    旧 BedrockReplacer / Seitiassist / CaveEraser を運用中のサーバーから乗り換える場合は、以下の手順で安全に差し替えてください。**LuckPerms 等の権限設定は無修正で動きます**。

### 1. 旧 jar を削除

`plugins/` フォルダから以下の jar を削除します（バックアップ推奨）。

- `plugins/BedrockReplacer.jar`
- `plugins/Seitiassist.jar`
- `plugins/CaveEraser.jar`

### 2. TerrainTools-1.0.0.jar を配置

ビルドした `target/TerrainTools-1.0.0.jar` を `plugins/` に置きます。

### 3. 既存 config の引き継ぎ（任意）

既存の設定値を活かしたい場合のみ、以下のとおり **リネームコピー** で継承できます。コピーしない場合は、デフォルト値で動作開始されます（`ConfigUtil` の自動展開）。

| 旧ファイル | 新ファイル |
|---|---|
| `plugins/Seitiassist/config.yml` | `plugins/TerrainTools/modules/seiti.yml` |
| `plugins/Seitiassist/trash_data.yml` | `plugins/TerrainTools/modules/seiti/trash_data.yml` |
| `plugins/CaveEraser/config.yml` | `plugins/TerrainTools/modules/cave.yml` |

※ BedrockReplacer は元から config が無いため、引き継ぎ作業は **不要** です。

### 4. サーバー再起動 → 動作確認

サーバーを再起動し、以下のコマンドが従来どおり動くことを確認してください。

- `/tt modules` ― 4 モジュール（bedrock / seiti / cave / spawn）が `enabled` で読み込まれていること。
- `/br status` ― bedrock モジュールが応答すること。
- `/sa` ― ヘルプが表示されること。
- `/caveerase list` ― cave モジュールが応答すること。
- `/spawn` ― 現在ワールドのスポーンへ移動できること。

確認後、旧 `plugins/BedrockReplacer/` / `plugins/Seitiassist/` / `plugins/CaveEraser/` フォルダは削除して構いません（必要なら別途バックアップ）。

### 5. LuckPerms 等の権限設定

**変更不要** です。旧 3 プラグインの権限ノード（`bedrockreplacer.*` / `seitiassist.*` / `caveeraser.*`）はすべて新プラグインの `plugin.yml` にそのまま残存しているため、既存設定で動作します。

## トラブルシューティング

??? failure "あるモジュールだけ起動しない"
    `/tt modules` で各モジュールの状態を確認できます。`config.yml` の `modules.<id>.enabled` が `false` になっていないか、サーバーログに該当モジュールの初期化エラーが出ていないかを確認してください。1 モジュールの初期化に失敗しても、他モジュールとサーバー本体は動作を継続します（`ModuleRegistry` で catch されます）。

??? failure "`/tt reload` で設定が反映されない"
    一部のアイテム（範囲採掘ツルハシ等）は **配布済みアイテムの表示名・耐久コストが再取得しないと反映されません**（PersistentData にモードのみが保存されるため）。新しい設定で `/sa pickaxe` 等を再実行して配り直してください。

??? failure "cave モジュールでコストが引かれない／引かれ方がおかしい"
    - CasinoPlugin が未導入、または bank モジュール未起動（`EmeraldAPI.isReady()` が false）の場合、コスト判定はスキップされます。サーバーログに `CasinoPlugin (EmeraldAPI) integration enabled!` または未検出メッセージのいずれかが出ているはずです。
    - EmeraldAPI は long ベース（整数）のため、`per-block` を小数に設定していても **合計コストは四捨五入** されてから引き落とされます。

??? failure "`/ce` を実行しても別プラグインが反応する"
    cave モジュールの `/ce` エイリアスは CustomEnchants と衝突します。**正式名 `/caveerase` を使用してください**。プラグインのロード順により、どちらの `/ce` が有効になるかが決まります。

??? failure "コンソールから `/br chunk` `/br radius` を実行できない"
    `chunk` / `radius` / `world` は **プレイヤー専用** です（位置情報が必要なため）。コンソールでは `cancel` / `status` のみ実行できます。

??? question "旧プラグインに戻したい"
    旧 3 つの jar は `target/<旧名>-<ver>.jar` に残っているはずです。各旧プラグインフォルダには `plugin.yml.bak_20260526` / `.bak_20260527` の退避ファイルもあります。TerrainTools-1.0.0.jar を削除し、旧 3 つの jar を `plugins/` に戻して再起動すればロールバック可能です。

??? question "config が消えた／壊れた"
    `plugins/TerrainTools/config.yml` および `modules/*.yml` を一度削除してサーバーを再起動すると、`ConfigUtil` の自動展開により jar 内蔵のデフォルト値で再生成されます。
