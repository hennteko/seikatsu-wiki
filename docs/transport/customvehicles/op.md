<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# CustomVehicles ― OP・運営ガイド { .page-op #customvehicles-op }

CustomVehicles の導入・config・サブコマンド・権限・CasinoPlugin 連携・トラブルシュートをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | CustomVehicles |
| バージョン | `${project.version}`（pom.xml で注入） |
| api-version | 26.1.2 |
| メインコマンド | `/vehicle`（エイリアス `/v`・`/car`） |
| softdepend | `CasinoPlugin`（任意。エメラルド決済に使用） |
| 設定ファイル | `plugins/CustomVehicles/config.yml` |
| データベース | SQLite（`plugins/CustomVehicles/vehicles.db`） |

!!! note "プレイヤー向け看板リスナー"
    `[Vehicle]` 看板（1行目）を設置すると、リスナーが整形し右クリックでカタログGUIが開きます（プレイヤー向けの代替操作）。看板の設置自体には権限不要です。

!!! info "CasinoPlugin は softdepend（任意）"
    `plugin.yml` で `softdepend: [CasinoPlugin]` が指定されています。CasinoPlugin が **無くてもプラグインは起動** しますが、車両の購入・アップグレード・カラー変更・ブースト/ジャンプ時の燃料消費といった **エメラルド決済機能はすべて動作しません**（残高は常に 0 として扱われ、購入や決済は失敗します）。本プラグインを実運用する場合は CasinoPlugin の導入を推奨します。

## 導入手順

1. ビルドした `CustomVehicles-<version>.jar` をサーバーの `plugins/` フォルダに配置する。
2. （推奨）エメラルド決済を使う場合は **CasinoPlugin** も `plugins/` に配置する。
3. サーバーを起動すると `plugins/CustomVehicles/config.yml` と `vehicles.db`（SQLite）が自動生成される。
4. 起動ログに次のいずれかが出力されることを確認する。
    - 成功時: `CasinoPlugin (EmeraldAPI) 連携: 成功`
    - 未導入時: `CasinoPlugin が見つかりません (旧 EmeraldBank の後継)` / `エメラルド機能 (購入/アップグレード/燃料消費) は動作しません`
5. 設定を変更したら `/vehicle reload` で再読み込みする。

!!! note "EmeraldBank からの移行について"
    内部実装上、CasinoPlugin との連携は **旧 EmeraldBank 連携クラスを名前そのままで CasinoPlugin の `jp.casinoplugin.api.EmeraldAPI` にリフレクションで差し替えた** 構造になっています（`EmeraldBankAPI` クラス名が残っていますが、参照先は CasinoPlugin です）。EmeraldBank をいったん導入する必要はありません。

## config.yml

`plugins/CustomVehicles/config.yml`

### データベース

| キー | 既定値 | 説明 |
|---|---|---|
| `database.file` | `vehicles.db` | SQLiteファイル名 |

### 車両価格

| キー | 既定値 | 説明 |
|---|---|---|
| `prices.sports_car` | 500 | スポーツカーの購入価格（エメラルド） |
| `prices.suv` | 800 | SUV の購入価格 |
| `prices.van` | 1200 | バン の購入価格 |

!!! warning "現状の価格はGUIにハードコード"
    `config.yml` には `prices` セクションがありますが、`PurchaseGUI` の購入GUI（カタログ右クリック）では **500／800／1200 が直接コードに書かれています**。`config.yml` の `prices` を変更しても購入価格に反映されません。価格を変えるには現状ソース改修が必要です。

### 燃料・クールダウン・特殊能力

| キー | 既定値 | 説明 |
|---|---|---|
| `fuel.consumption_rate` | 100 | 移動距離あたりの消費量（ブロック/エメラルド）※定義値（実走行消費は未確認） |
| `fuel.boost_cost` | 2 | ブースト1回あたりに引き落とされるエメラルド |
| `fuel.jump_cost` | 1 | ジャンプ1回あたりに引き落とされるエメラルド |
| `cooldowns.boost` | 10 | ブーストのクールダウン（秒） |
| `cooldowns.jump` | 5 | ジャンプのクールダウン（秒） |
| `boost.speed_multiplier` | 1.5 | ブースト中の速度倍率 |
| `boost.duration` | 3 | ブースト継続時間（秒）※実時間は「3 + 車両のブーストLv」秒 |
| `jump.height` | 3.0 | ジャンプの高さ（ブロック相当） |

### 耐久・修理

| キー | 既定値 | 説明 |
|---|---|---|
| `durability.damage_reduction` | 5 | ダメージを受けた時の耐久減少量 |
| `durability.distance_reduction_rate` | 1000 | 移動距離あたりの耐久減少レート（1000ブロックごとに 1） |
| `repair.cost_per_durability` | 1 | 失った耐久1ポイントあたりの修理費（エメラルド） |

### メッセージ（`messages`）

| キー | 既定値（抜粋） | 説明 |
|---|---|---|
| `prefix` | `§a[CustomVehicles]§r ` | 各メッセージの先頭に付くプレフィックス |
| `purchase_success` | `§a{vehicle_type}を購入しました! (残高: {balance}エメラルド)` | 購入成功時 |
| `insufficient_balance` | `§cエメラルドが不足しています (必要: {required}, 所持: {balance})` | 残高不足 |
| `vehicle_spawned` / `vehicle_not_found` / `already_riding` | 該当場面のメッセージ | 召喚／不在／重複召喚 |
| `boost_activated` / `boost_cooldown` | ブースト関連 | クールダウン残秒は `{seconds}` で差し込み |
| `jump_activated` / `jump_cooldown` | ジャンプ関連 | 同上 |
| `no_fuel` / `repair_success` / `vehicle_destroyed` | 燃料切れ・修理成功・破壊通知 | `repair_success` は `{cost}` を差し込み |

プレースホルダ（`{vehicle_type}`・`{balance}`・`{required}`・`{seconds}`・`{cost}`）はメッセージごとに置換されます。

!!! note "vehicles.yml は存在しません"
    車種の定義は **`VehicleType` enum**（`SPORTS_CAR`・`SUV`・`VAN`）でハードコードされており、`vehicles.yml` のような外部ファイルはありません。座席数・カスタム上限の変更には改修が必要です。

## サブコマンド詳細

メインコマンドは `/vehicle`（`/v`・`/car`）です。共通仕様としてプレイヤー専用で、コンソールからは実行できません。

### `/vehicle catalog`

- **権限**: `customvehicles.catalog`（既定 OP）
- **動作**: カタログアイテム（PAPER、緑色「車両カタログ」、PDC `customvehicles:vehicle_catalog`）をインベントリに付与します。プレイヤーはこれを右クリックすると購入GUIが開きます。

### `/vehicle list`

- 自分が所有する全車両を「ID・表示名・耐久値・燃料」付きで列挙します。

### `/vehicle spawn <車両ID>`

- 指定車両を周囲に召喚します。
- 所有者本人でない場合・燃料0・既に召喚中のいずれかで失敗します。
- 座席が1のスポーツカーは運転席へ自動着席、SUV/バンは座席選択GUIが約5tick遅延で開きます。

### `/vehicle upgrade`

- **アップグレードGUI**（36スロット、タイトル「★ 車両アップグレード ★」）を開きます。
- 対象は **所持車両リストの先頭1台**（複数所持時の選択GUIは未実装、コード上 `TODO`）。
- GUIから エンジン／タイヤ／サスペンション／ブースト／カラー／修理 を操作できます。
- アップグレード費用: Lv.1→2 = 100E、Lv.2→3 = 200E、Lv.3→4 = 400E、Lv.4→5 = 800E（最大時は 0）。
- カラー変更: 50E（12色）。
- 修理: `(100 − 現耐久) × repair.cost_per_durability` E。

### `/vehicle info [車両ID]`

- IDなし: 使用法案内のみ。
- IDあり: 該当車両の **車種・カスタム名・座席数・耐久値・燃料・全アップグレードレベル・カスタム上限・総走行距離・ブースト使用回数** を表示します。

### `/vehicle delete <車両ID> [confirm]`

- `confirm` 無し: 警告メッセージと、`confirm` 付きの再実行コマンドを表示。
- `confirm` あり: SQLite から該当車両レコードを削除します（取り消し不可）。

### `/vehicle reload` ※管理者

- **権限**: `customvehicles.reload`（既定 OP）
- **動作**: `config.yml` の再読み込み（`Bukkit#reloadConfig`）。messages・cooldowns・repair などはこれで反映されます。GUI・カタログのハードコード価格は再起動でも変わりません。

## 権限ノード

| 権限 | 既定 | 内容 |
|---|---|---|
| `customvehicles.use` | true（全員） | 看板／カタログアイテム経由の GUI 操作（リスナー内チェック） |
| `customvehicles.admin` | op | `/vehicle` コマンド本体の使用権。子として `use` / `catalog` / `give` / `reload` を内包 |
| `customvehicles.catalog` | op | `/vehicle catalog`（カタログアイテム配布） |
| `customvehicles.give` | op | （現状コマンドは未実装。権限ノードのみ定義） |
| `customvehicles.reload` | op | `/vehicle reload` |

!!! info "コマンドは OP 限定 / プレイヤーは看板・カタログ"
    `/vehicle` コマンド自体に `permission: customvehicles.admin`（既定 op）が設定されています。プレイヤーは `[Vehicle]` 看板または OP が配布したカタログアイテムから購入・召喚・カスタマイズを行います。`permission-message` は「§cこのコマンドを使用する権限がありません (プレイヤーは [Vehicle] 看板またはカタログアイテムを使用してください)」です。

## CasinoPlugin 連携（EmeraldAPI）

CustomVehicles は CasinoPlugin が公開する `jp.casinoplugin.api.EmeraldAPI` を **リフレクションで参照** します。連携で使われるメソッドは次の4つです。

```text
long    EmeraldAPI.getBalance(UUID)
boolean EmeraldAPI.withdraw (UUID, long)
boolean EmeraldAPI.deposit  (UUID, long)
boolean EmeraldAPI.isReady ()
```

### 連携が使われる場面

| 機能 | 処理 |
|---|---|
| 車両購入（PurchaseGUI） | 残高チェック → `withdraw`（失敗時は `deposit` で返金） |
| アップグレード（UpgradeGUI 各種） | 残高チェック → `withdraw` |
| カラー変更（ColorGUI） | 残高チェック → `withdraw`（50E） |
| 修理（UpgradeGUI 修理ボタン） | 残高チェック → `withdraw` |
| ブースト/ジャンプ発動（VehicleControlListener） | 残高チェック → `withdraw`（`fuel.boost_cost`／`fuel.jump_cost`） |

`isReady()` が false の場合、`EmeraldBankAPI.isAvailable()` も false を返し、各処理は失敗または無償フォールバックします（ブースト/ジャンプは燃料コストが請求されません）。

## トラブルシューティング

??? failure "起動時に「CasinoPlugin が見つかりません」と警告が出る"
    `softdepend` 指定のため CasinoPlugin が未導入でもプラグインは起動しますが、エメラルド機能は無効になります。CasinoPlugin を `plugins/` に追加して再起動してください。導入済みでも警告が出る場合は CasinoPlugin が他の理由で無効化されていないかログを確認してください。

??? failure "起動時に「CasinoPlugin EmeraldAPI の取得に失敗しました」と出る"
    CasinoPlugin が `jp.casinoplugin.api.EmeraldAPI` を公開していない／メソッドシグネチャが想定と違う可能性があります。スタックトレースをログから確認し、CasinoPlugin のバージョンが本プラグイン想定（`getBalance(UUID) long` / `withdraw(UUID, long) boolean` / `deposit(UUID, long) boolean` / `isReady() boolean`）と一致しているか確認してください。

??? failure "車両カタログを右クリックしても購入GUIが開かない"
    プレイヤーが `customvehicles.use` を持っているか確認してください（既定 true）。また、カタログは **PDCタグ `customvehicles:vehicle_catalog` で判定** しているため、`/give` などで作った見た目だけのカタログでは開きません。`/vehicle catalog` で正規のカタログを配布してください。

??? failure "GUIで購入をクリックしても残高不足になる"
    支払い元は CasinoPlugin の残高です。Vault やバニラ残高ではありません。CasinoPlugin 内の残高を確認してください。価格は GUI 内ハードコード（500/800/1200）で `config.yml` の `prices` は現状反映されません。

??? failure "ブースト・ジャンプが効かない"
    1) 運転席（座席0）に座っているか、2) クールダウン中でないか、3) 右クリック時に手に持っているアイテムがブロック／対話可能アイテムでないか、4) CasinoPlugin の残高が `fuel.boost_cost`／`fuel.jump_cost` 以上あるか、を順に確認してください。

??? failure "`/vehicle upgrade` で別の車両を強化したい"
    現状 `/vehicle upgrade` は所持車両の先頭1台のみが対象です（コード上 `TODO: 複数車両がある場合は選択GUIを表示`）。対応するまでは、対象でない車両を `/vehicle delete <ID> confirm` で整理してから実行するか、ソース側の改修で対応してください。

??? failure "`config.yml` を変更しても挙動が変わらない"
    多くの設定は `/vehicle reload` で反映されますが、**車両価格（500/800/1200）と各車種の座席数・カスタム上限はコードにハードコード** されています。これらは設定変更では変わらず、ソース改修が必要です。

??? failure "車両エンティティが残ったまま消えない／チャンクをまたぐとおかしい"
    プラグイン無効化時（`onDisable`）は `VehicleManager#removeAllVehicles()` で全車両を削除します。意図せず残った場合はサーバー再起動で消えます。Shift で運転席から降りると車両は削除されます（仕様）。

## 実装状況メモ

- 実装済み: カタログ→購入GUI→SQLite登録→召喚→運転→ブースト/ジャンプ→アップグレード/カラー/修理（GUI）→削除→情報表示。
- `/vehicle upgrade` は所持車両の先頭1台のみが対象（複数車両選択GUIは未実装、ソース上 `TODO`）。
- 価格・座席数・カスタム上限など一部パラメータは現状コード固定。`config.yml` の `prices`・`fuel.consumption_rate`・`boost.speed_multiplier`／`boost.duration`／`jump.height` のうち、ブースト継続時間は **「3 + ブーストLv」秒** で計算され、`boost.duration` の値は使われていません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← CustomVehicles 概要へ](index.md){ .md-button }
