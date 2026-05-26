<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# CustomVillagerTrade ― OP・運営ガイド { .page-op #customvillagertrade-op }

CustomVillagerTrade の導入・config・プリセット・`/vtrade` 各サブコマンド・権限・トラブルシュートをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | CustomVillagerTrade |
| バージョン | 1.1.9 |
| api-version | 26.1.2 |
| メインクラス | `com.henry.customvillagertrade.CustomVillagerTrade` |
| メインコマンド | `/vtrade`（エイリアス `/villagertrade`・`/cvt`） |
| 任意依存（softdepend） | `CasinoPlugin`（EmeraldAPI 連携） |
| 設定ファイル | `plugins/CustomVillagerTrade/config.yml`、`messages.yml`、`villagers.yml`、`presets/*.json` |

!!! info "CasinoPlugin との連携（softdepend）"
    `plugin.yml` に `softdepend: [CasinoPlugin]` が指定されています。CasinoPlugin が読み込まれていない環境でも本プラグインは正常に動作しますが、**エメラルド残高を使った取引機能** は無効化されます（ログに `CasinoPlugin が見つかりません。経済連携機能は無効化されます。` と出力）。CasinoPlugin がある場合は `jp.casinoplugin.api.EmeraldAPI` をリフレクションで呼び出し、`getBalance(UUID)`／`withdraw(UUID,long)`／`deposit(UUID,long)`／`isReady()` を使用します。BankModule の `EmeraldAPI.bind()` 完了前は連携が無効になる点に注意してください。

## 導入手順

1. ビルドした `CustomVillagerTrade-1.1.9.jar`（または該当バージョン）を `plugins/` に配置する。
2. （任意）`CasinoPlugin` を導入する。エメラルド残高での支払いを使いたい場合は先に CasinoPlugin が起動するよう配置する。
3. サーバーを起動すると `plugins/CustomVillagerTrade/` に `config.yml`・`messages.yml`・`presets/` フォルダ・`villagers.yml` が自動生成される。
4. デフォルトプリセット（`farmer`・`librarian`・`weaponsmith`・`armorer`・`butcher`）が `presets/` 配下に作成される。
5. 必要に応じて権限を付与（既定では `customvillagertrade.use` が全員に許可）。
6. OPが `/vtrade spawn <プリセット名>` などで村人を配置する。

!!! note "自動保存"
    村人データ（`villagers.yml`）は **5分ごと**（6000 ticks 間隔）に非同期で自動保存されます。手動保存時にはサーバー停止直前にも保存され、保存前には `villagers.yml.backup` としてバックアップが作成されます。

## ファイル構成

```
plugins/CustomVillagerTrade/
├── config.yml             # プラグイン設定
├── messages.yml           # 表示メッセージ（日本語）
├── villagers.yml          # 登録済み村人データ（自動生成・自動保存）
├── villagers.yml.backup   # 直近の保存前バックアップ
└── presets/
    ├── farmer.json        # デフォルトプリセット
    ├── librarian.json
    ├── weaponsmith.json
    ├── armorer.json
    ├── butcher.json
    └── <ユーザー保存>.json
```

- **`villagers.yml`** … 登録村人のUUID・名前・職業・レベル・取引リストを YAML で保存。アイテムは Base64 シリアライズ。
- **`presets/*.json`** … プリセット1件 = 1ファイル（JSON）。Gson で整形保存（PrettyPrinting）。ファイル名はプリセット名を小文字化したもの。

## config.yml 主要項目

```yaml
language: ja

database:
  type: sqlite        # 現状 SQLite のみ（MySQL は将来対応予定）
  file: villagers.db

villager:
  level-system: true  # 村人レベルシステムの有効化
  level-up-trades: 10 # レベルアップに必要な取引回数（実装上は level×10）
  max-level: 5        # 最大レベル

trade:
  sound: true         # 取引時の効果音
  particle: true      # 取引完了時のパーティクル
  statistics: true    # 取引統計の記録

# 経済プラグイン連携 (旧: EmeraldBank / 新: CasinoPlugin の EmeraldAPI)
# キー名は後方互換のため "emeraldbank" を据え置き
emeraldbank:
  enabled: true               # CasinoPlugin が読み込まれている場合のみ実効
  allow-balance-payment: true # エメラルド残高での購入を許可

gui:
  title-prefix: "§6[CVT] "    # GUIタイトルのプレフィックス

debug: false
```

| キー | 既定値 | 説明 |
|---|---|---|
| `language` | `ja` | 言語（現状は `ja` のみ提供） |
| `villager.level-system` | `true` | レベルアップ機能の有効化 |
| `villager.level-up-trades` | `10` | レベルアップ判定の基準値（実装は `level × 10` 取引でアップ、上限5） |
| `trade.sound` | `true` | 取引・レベルアップ時のサウンド再生 |
| `emeraldbank.enabled` | `true` | CasinoPlugin との EmeraldAPI 連携の有効化 |
| `emeraldbank.allow-balance-payment` | `true` | エメラルド残高での支払いを許可 |
| `gui.title-prefix` | `§6[CVT] ` | 編集GUIのタイトル先頭に付く文字列 |
| `debug` | `false` | 詳細ログを出力 |

## /vtrade コマンド一覧

`usage` は `/vtrade [edit|register|spawn|preset|clone|list]`、エイリアスは `/villagertrade`・`/cvt`。コマンド本体の `permission` は `customvillagertrade.admin`。

| サブコマンド | 必要権限 | 説明 |
|---|---|---|
| `/vtrade` | `customvillagertrade.admin` | ヘルプを表示 |
| `/vtrade edit` | `customvillagertrade.edit` | 見ている **登録済み村人** の取引編集GUIを開く |
| `/vtrade register <名前>` | `customvillagertrade.admin` | 見ている **未登録の村人** を新規登録 |
| `/vtrade spawn <プリセット名>` | `customvillagertrade.spawn` | プリセットから新規にカスタム村人をスポーン |
| `/vtrade preset list` | `customvillagertrade.preset` | プリセット一覧を表示 |
| `/vtrade preset save <名前> [説明]` | `customvillagertrade.preset` | 見ている村人の取引をプリセット保存 |
| `/vtrade preset load <名前>` | `customvillagertrade.preset` | 見ている村人にプリセットを適用 |
| `/vtrade preset delete <名前>` | `customvillagertrade.admin` | プリセットを削除（デフォルトは削除不可） |
| `/vtrade clone` | `customvillagertrade.admin` | 未実装（`§eクローン機能は今後実装予定です。` と表示） |
| `/vtrade list` | `customvillagertrade.admin` | 登録済みカスタム村人の一覧を表示 |
| `/vtrade help` | `customvillagertrade.admin` | ヘルプを表示 |

### サブコマンドの詳細

#### `/vtrade register <名前>`

- 見ている村人（5ブロック以内）に **管理用の名前** を付けて登録します。
- 登録時に村人の **AIが無効化** され、その場に固定されます。`§6` 付きのカスタム名で表示されます。
- 名前の制約: 32文字以内、`< > [ ] { } \ |` は使用不可、同名重複不可。

#### `/vtrade edit`

- 見ている **登録済み** カスタム村人の取引編集GUI（`TradeListGUI`）を開きます。
- GUI上で取引の追加・編集・削除、村人設定、プリセット保存／読み込み、エクスポートを操作できます。
- 取引編集画面では **材料1・材料2（任意）・結果アイテム**・最大取引回数・経験値・価格倍率を設定可能。

!!! note "GUI由来の取引パラメータ既定値"
    新規取引の既定値は `maxUses=999`、`experience=0`、`priceMultiplier=1.0`、`enabled=true`。`MerchantRecipe` 生成時に `uses=0`・`demand=0`・`specialPrice=0` を強制し、Bedrock/Geyser 互換のためのロック防止と価格固定を行っています。

#### `/vtrade spawn <プリセット名>`

- プレイヤーの **目線方向2ブロック先** にカスタム村人をスポーンし、即座にプリセットを適用します。
- スポーン直後の管理名は `<プリセット名>_<UNIXタイムスタンプms>` です。必要に応じて配置後に `/vtrade register` を使い直すか、`villagers.yml` で確認してください（再登録不要、内部UUIDで管理）。
- プリセットには **職業（Profession）・村人レベル・取引リスト** が含まれます。

#### `/vtrade preset save <名前> [説明]`

- 見ているカスタム村人の取引リスト・職業・村人レベルを **プリセットJSON** として `presets/<name>.json` に保存します。
- 既に取引が0件の場合は保存できません（`§cこの村人には取引が設定されていません。`）。
- 説明を省略すると `カスタムプリセット` がセットされます。

#### `/vtrade preset load <名前>`

- 見ている **登録済み** 村人にプリセットを適用します（既存取引はクリアされ、プリセット内容で上書き）。
- 職業と村人レベルもプリセットの値で上書きされます。

#### `/vtrade preset list`

- 読み込み済みのすべてのプリセット名・説明・職業・レベル・取引数を一覧表示します。

#### `/vtrade preset delete <名前>`

- プリセットJSONを削除します。
- **デフォルトプリセット**（`farmer`・`librarian`・`weaponsmith`・`armorer`・`butcher`）は削除できません。

#### `/vtrade list`

- 登録済みカスタム村人をインデックス・名前・取引数の形式で一覧表示します。

### デフォルトプリセット

初回起動時、プリセットフォルダが空であれば次のプリセットが自動生成されます。

| プリセット名 | 職業 | レベル | 取引数 | 主な内容 |
|---|---|---|---|---|
| `farmer` | FARMER | 5 | 5 | 小麦/ニンジン/ジャガイモ → エメラルド、エメラルド → パン・ケーキ |
| `librarian` | LIBRARIAN | 5 | 5 | 紙/本 → エメラルド、エメラルド → 本棚・ランタン・名札 |
| `weaponsmith` | WEAPONSMITH | 5 | 5 | 石炭/鉄インゴット → エメラルド、エメラルド → 鉄の剣/斧・ダイヤの剣 |
| `armorer` | ARMORER | 5 | 5 | 石炭/鉄インゴット → エメラルド、エメラルド → 鉄ヘルメット/胸当て・ダイヤ胸当て |
| `butcher` | BUTCHER | 5 | 4 | 生豚肉/生鶏肉 → エメラルド、エメラルド → 焼き豚・焼き鳥 |

## 権限ノード

| 権限ノード | 既定値 | 説明 |
|---|---|---|
| `customvillagertrade.admin` | `op` | プラグインの全機能にアクセス（コマンド本体権限） |
| `customvillagertrade.use` | `true` | カスタム村人との取引（全プレイヤー） |
| `customvillagertrade.edit` | `op` | `/vtrade edit` で取引編集GUIを開く |
| `customvillagertrade.spawn` | `op` | `/vtrade spawn` でカスタム村人を生成 |
| `customvillagertrade.preset` | `op` | `/vtrade preset save/load/list` を実行 |

!!! warning "コマンド本体権限と個別権限の二段構え"
    `/vtrade` 自体に `customvillagertrade.admin` が設定されているため、個別権限（`edit`・`spawn`・`preset`）を付与しても **`customvillagertrade.admin` が無いとコマンドそのものが実行できません**。一般スタッフに編集だけを許可する場合は、`customvillagertrade.admin` も併せて付与するか、LuckPerms 等で `customvillagertrade.admin` を `true` にしてください。`preset delete` と `register` は内部で別途 `customvillagertrade.admin` をチェックします。

## 取引挙動の仕様メモ

- **取引イベント** … `PlayerInteractEntityEvent`（priority: LOWEST）で村人を右クリックすると、`updateVillagerTrades()` により `MerchantRecipe` を再生成します。画面はクライアント/Geyser に委ねるため、サーバー側からは明示的に開きません。
- **スニーク + 編集権限** … スニーク中で `customvillagertrade.edit` 権限を持つプレイヤーが右クリックすると、取引画面の更新自体をスキップします（編集モードを邪魔しないため）。
- **Bedrock/Geyser 対応** … `setUses(0)`・`setDemand(0)`・`setSpecialPrice(0)` で価格変動・ロックを抑制し、`priceMultiplier` と `villagerExperience` はカスタム値を反映。
- **レベルアップ** … `CustomVillager.checkLevelUp()` は `level × 10` 取引でレベルアップ（最大5）。レベルアップ時に効果音 `ENTITY_PLAYER_LEVELUP` を再生。
- **取引成功音** … `trade.sound: true` の場合 `ENTITY_VILLAGER_YES` を再生。

## トラブルシューティング

??? failure "`/vtrade` を実行しても何も表示されない"
    `customvillagertrade.admin` 権限がありません。`/vtrade` 本体に必要な権限なので、OPまたは LuckPerms 等で付与してください。OPで動かない場合はサーバーの権限プラグイン設定を確認します。

??? failure "「村人を見てからコマンドを実行してください。」と出る"
    `/vtrade edit`・`register`・`preset save/load` はプレイヤーの **目線レイトレース（5ブロック以内）** で村人を判定します。視線が他のエンティティに遮られていないか、距離が遠すぎないかを確認してください。

??? failure "登録済み村人が `/vtrade edit` で「登録されていません」と言われる"
    村人が再スポーン（チャンク再生成・/kill 等）されてUUIDが変わっている可能性があります。一度 `/vtrade list` で登録一覧を確認し、必要なら再登録してください。

??? failure "CasinoPlugin の連携が有効化されない"
    起動ログに `CasinoPlugin が見つかりません。経済連携機能は無効化されます。` または `CasinoPlugin は読み込まれていますが、EmeraldAPI がまだ準備できていません。` と出ていないか確認してください。後者の場合、CasinoPlugin の **BankModule** が `EmeraldAPI.bind()` を呼ぶ前に CustomVillagerTrade が起動しています。サーバーを再起動して順序を整えるか、`config.yml` の `emeraldbank.enabled` を `false` にして連携を切り離してください。

??? failure "プリセットがロードされない／無効と表示される"
    `presets/<name>.json` の JSON 構文エラー、`name` や `trades` が空、`isValid()` チェック失敗のいずれかが原因です。`debug: true` にして再起動するとファイル名と原因（JSON フォーマット／プリセット名なし／取引データなし／復元失敗）が `[WARN]` でログに出ます。

??? failure "村人が動いてしまう／消えてしまう"
    登録時に `setAI(false)` を呼んでいるためカスタム村人は本来動きません。再ログイン後やワールド再読み込み後に動いてしまう場合は、サーバー側のエンティティ最適化プラグインや `villager.setAI(false)` を覆すプラグインがないか確認してください。チャンクアンロード後の挙動については Bukkit / Paper の仕様に依存します。

??? failure "`/vtrade clone` が動かない"
    仕様です。`§eクローン機能は今後実装予定です。` のメッセージのみ返ります。プリセット機能で代替してください。

## 実装に関する注意

- `language: ja` 以外の言語ファイルは未提供。`messages.yml` を直接編集することで他言語化は可能ですが、フォーマット崩れに注意。
- `database.type: sqlite` と書かれていますが、現状の永続化は **`villagers.yml` および `presets/*.json` ベース** で実装されています。SQLite ファイル（`villagers.db`）は実際には使用されません（コメント通り将来対応予定）。
- `EmeraldBankIntegration` クラスは互換性維持のため旧名のまま据え置き、内部は CasinoPlugin の `jp.casinoplugin.api.EmeraldAPI` を **リフレクション** で呼び出します。引数は `Player → UUID`、金額は `double → long`（切り捨て）に変換されます。
- 自動保存は **5分ごと** に非同期実行。手動 `saveData()` も非同期。サーバー停止時には同期的に `saveAll()` が走ります。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← CustomVillagerTrade 概要へ](index.md){ .md-button }
