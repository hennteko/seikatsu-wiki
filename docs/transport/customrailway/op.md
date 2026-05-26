<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# CustomRailway ― OP・運営ガイド { .page-op #customrailway-op }

CustomRailway の導入・`config.yml`・権限・管理コマンド・トラブルシュートをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | CustomRailway |
| バージョン | `${project.version}`（plugin.yml はプロパティ参照。ビルド成果物に依存） |
| api-version | 26.1.2 |
| メインコマンド | `/railway`（`/rail`・`/rw`） |
| 依存プラグイン | なし（plugin.yml に `depend`/`softdepend` の記載なし） |
| 設定ファイル | `plugins/CustomRailway/config.yml` |
| 駅データ | `plugins/CustomRailway/stations.yml`（自動生成・自動更新） |

!!! note "コマンドの宣言と実装のずれ"
    `plugin.yml` の `usage` は `/railway [station|route|train|help]` ですが、`RailwayCommand` に実装されているサブコマンドは **`station` / `train` / `list` / `info` / `reload` / `help`** です。`route` サブコマンドは実装されていません（駅オブジェクトには `connectedRoutes` リストの構造のみ存在）。

## 導入手順

1. ビルドした `CustomRailway-*.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/CustomRailway/config.yml` と `stations.yml` が自動生成される。
3. 必要に応じて `config.yml` を編集する（連結数・速度・駅作成条件・パーティクルなど）。
4. 設定を変更したら `/railway reload` で再読み込みする（`customrailway.admin` が必要）。

!!! note "動作要件"
    `plugin.yml` の `api-version` は `26.1.2`、ソース内コメントでは Paper 26.1 系を前提とした記述があります（`InventoryView#getTitle()` 非推奨対応・`Material.IRON_CHAIN`／`Particle.HAPPY_VILLAGER` の新名称使用など）。Paper 1.26 系のサーバーで利用してください。

## config.yml

既定値は次の通りです（ソース `src/main/resources/config.yml` より）。

### 列車設定（`train`）

| キー | 既定値 | 説明 |
|---|---|---|
| `train.max-carts` | 5 | 1 編成あたりの最大連結車両数（先頭含む） |
| `train.cart-spacing` | 1.5 | 車両間の距離（ブロック） |
| `train.max-speed-multiplier` | 3.0 | 速度倍率の上限 |
| `train.default-speed` | 1.0 | デフォルト速度倍率 |
| `train.allow-speed-change` | true | 速度制御ブロックでの速度変更を許可 |

### 速度制御ブロック（`speed-blocks`）

線路の **2 ブロック下** に置かれたブロックの種類で速度倍率を切り替えます。キーは `Material` 名、値は倍率です。既定では下表の 5 種類が登録されています。

| ブロック | 既定倍率 | 用途 |
|---|---|---|
| `GOLD_BLOCK` | 2.0 | 高速 |
| `IRON_BLOCK` | 1.0 | 通常 |
| `COAL_BLOCK` | 0.5 | 低速 |
| `DIAMOND_BLOCK` | 3.0 | 超高速 |
| `EMERALD_BLOCK` | 0.0 | 停止（駅） |

無効な Material 名が指定されると `無効なブロックタイプ: <名前>` がログ出力され、その項目はスキップされます。

### 駅設定（`station`）

| キー | 既定値 | 説明 |
|---|---|---|
| `station.player-creation` | true | プレイヤーによる駅作成を許可（読み込みのみ。実コードでは権限と上限で制御） |
| `station.min-distance` | 50 | 既存駅からの最小距離（ブロック） |
| `station.max-per-player` | 3 | プレイヤー 1 人あたりの最大駅数 |
| `station.creation-cost` | 1000 | 駅作成コスト（コメント上は EmeraldBank 連携想定。**現バージョンでは消費処理は未実装**） |

### 自動運転設定（`autopilot`）

| キー | 既定値 | 説明 |
|---|---|---|
| `autopilot.enabled` | true | 自動運転を有効化（**現バージョンで autopilot を駆動するコマンド／ロジックは未提供**） |
| `autopilot.stop-duration` | 10 | 駅での停車時間（秒） |
| `autopilot.waypoint-detection` | 2.0 | ウェイポイント検知距離 |

### パーティクル設定（`particles`）

| キー | 既定値 | 説明 |
|---|---|---|
| `particles.connection` | `HEART` | 連結時のパーティクル |
| `particles.speed-change` | `CLOUD` | 速度変更（切り離し時にも使用）パーティクル |
| `particles.arrival` | `VILLAGER_HAPPY`（旧名） | 駅到着時のパーティクル。旧名 `VILLAGER_HAPPY` は内部で `HAPPY_VILLAGER` に変換 |

不正なパーティクル名はログ警告とともに既定値にフォールバックします。

### メッセージ設定（`messages`）

`messages.prefix`（既定 `&7[&bRailway&7]&r `）に加え、駅作成失敗・連結成功・テレポート完了など全メッセージを差し替え可能です。Adventure Component への変換は内部で `LegacyComponentSerializer` 経由で行われます。プレースホルダは `{name}`・`{max}`・`{distance}`・`{required}`・`{station}`・`{speed}` などです。

!!! note "設定変更後は `/railway reload`"
    `/railway reload` を実行すると `config.yml` と `stations.yml` の両方が再読み込みされます（`customrailway.admin` が必要）。

## 路線の作成について

`plugin.yml` 上では `customrailway.route.create`（default: op）という権限ノードが定義されています。ただし **現バージョンの `RailwayCommand` には `route` サブコマンドが実装されておらず、路線を作成・編集するコマンドは存在しません**。

- 駅クラス `Station` は `connectedRoutes : List<String>` を保持しており、`addRoute(name)`／`removeRoute(name)` の API はあります。
- `/railway station info` および駅詳細GUIでは「接続路線」が表示される構造になっています（未設定時は `なし`）。
- 路線を運用する場合は、将来バージョンでのコマンド追加またはサーバー側で別途データ投入する必要があります（標準のプレイヤー操作では設定不可）。

## 管理コマンド

| コマンド | 必要権限 | 説明 |
|---|---|---|
| `/railway reload` | `customrailway.admin` | `config.yml` と `stations.yml` を再読み込み |
| `/railway station tp <駅名>` | `customrailway.admin` | 指定駅にテレポート |
| `/railway station delete <駅名>` | オーナー本人 or `customrailway.admin` | 駅を削除 |
| `/railway info` | なし | プラグインのバージョン・駅数・列車数・最大連結数・最大速度倍率を表示 |
| `/railway list stations` / `list trains` | なし（trains はプレイヤー限定） | 一覧表示 |

プレイヤー向けコマンド（`station create`／`gui`／`train info`／`train list`／`help`）の詳細は [プレイヤー向けページ](player.md) を参照してください。

## 権限ノード

`plugin.yml` で定義されている権限は次の4つです。

| 権限ノード | 既定 | 説明 |
|---|---|---|
| `customrailway.admin` | op | 管理者権限（`/railway reload`・`/railway station tp` ・他人の駅の削除に必要） |
| `customrailway.station.create` | true | 駅を作成する権限（`/railway station create`） |
| `customrailway.train.connect` | true | 列車を連結／切り離す権限（鎖・ハサミでの操作） |
| `customrailway.route.create` | op | 路線を作成する権限（**現バージョンでは参照箇所なし**） |

!!! example "LuckPerms 設定例"
    ```bash
    # 管理者
    /lp group admin permission set customrailway.admin true

    # 一般プレイヤーは default で station.create / train.connect が true
    # 制限したい場合は明示的に false を設定
    /lp group default permission set customrailway.station.create false
    ```

## トラブルシューティング

??? failure "`/railway station create` で「駅が近すぎます」と出る"
    `station.min-distance`（既定 50 ブロック）以内に既存の駅があります。距離を空けて作成するか、`config.yml` で値を下げて `/railway reload` してください。

??? failure "プレイヤーが駅をたくさん作れない"
    `station.max-per-player`（既定 3）が上限です。値を増やして `/railway reload` してください。`/railway station info` で表示される `オーナー` はその駅を作ったプレイヤーです。

??? failure "鎖でトロッコをクリックしても反応しない"
    手に持っているのが **`IRON_CHAIN`**（Minecraft 26.x で `CHAIN` から改名）であることを確認してください。オフハンドのクリックは無視されます（`EquipmentSlot.HAND` のみ処理）。`customrailway.train.connect`（既定 true）が剥奪されていないかも確認してください。

??? failure "ハサミで切り離せない"
    `customrailway.train.connect` 権限が必要です（既定 true）。先頭トロッコをハサミで右クリックすると編成全体が解散される仕様です。

??? failure "速度が変わらない"
    `train.allow-speed-change` が `true` か、線路の **2 ブロック下** に対象ブロック（GOLD_BLOCK 等）が置かれているかを確認してください。速度判定は `Minecart` の現在位置から `y-1` のさらに下（`y-2`）のブロックを参照します。

??? failure "駅GUIをクリックしても何も起きない"
    `RailwayGuiHolder` で識別された GUI 内のクリックはキャンセル＋アクション処理が走る仕様です。`Material.RAIL`／`POWERED_RAIL`（駅アイテム）・`PLAYER_HEAD`（自分の駅のみ）・`COMPASS`（全駅）・`ENDER_PEARL`（テレポート、管理者のみ）・`BARRIER`（削除）・`ARROW`（戻る）以外はクリックしても効果がありません。

??? failure "テレポートボタン（ENDER_PEARL）が見えない"
    駅詳細GUIのテレポートボタンは `customrailway.admin` 権限保持者にのみ表示されます。削除ボタン（BARRIER）はオーナー本人または管理者にだけ表示されます。

??? failure "`stations.yml` を直接編集したのに反映されない"
    プラグイン稼働中の駅情報はメモリ上にあり、`/railway reload` か再起動でファイルから再読み込みされます。逆に駅追加／削除があると `saveStations()` で `stations.yml` が上書きされるため、手編集は再起動前に行ってください。

## 実装・ドキュメント上の注意

!!! warning "未実装・想定外の挙動に注意"
    現バージョンには **コメント／設定上は存在するが未実装** の機能があります。運営アナウンス時は誤解を避けるため明示してください。

- **`route` サブコマンド** … `plugin.yml` の `usage` に `route` の記載があり、`customrailway.route.create` 権限も定義されているが、コマンドハンドラには未実装。
- **駅作成コスト** … `station.creation-cost`（既定 1000）はコメント上 EmeraldBank 連携想定だが、`StationManager#createStation` 内では参照されていない（消費処理なし）。
- **自動運転（autopilot）** … `autopilot.enabled` などの設定はあるが、`TrainCart#setAutoPilot` を呼ぶロジックや関連コマンドは未提供。
- **駅到着パーティクル** … `particles.arrival` は設定可能だが、駅到着イベントの実装は現状確認できない（連結時 `HEART`、切り離し／速度変更時 `CLOUD` は使用箇所あり）。
- **運賃 `baseFare`** … `Station` クラスに `baseFare` フィールドはあるが、設定するコマンド／処理は未実装（駅情報には `0 エメラルド` と表示）。

---

[👤 プレイヤー向けページへ](player.md){ .md-button }
[← CustomRailway 概要へ](index.md){ .md-button }
