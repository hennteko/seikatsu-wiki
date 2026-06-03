<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# EnchantPack ― OP・運営ガイド { .page-op #enchantpack-op }

EnchantPack の導入・共通設定（`config.yml`）・モジュール構成（`modules/*.yml`）・統合管理コマンド・権限・移行手順・トラブルシューティングをまとめます。各モジュールの個別仕様の細部は、必要に応じて旧 EnchantPlus / CustomEnchants の WIKI ページも参照してください。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | EnchantPack |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインクラス | `jp.henry.enchantpack.EnchantPack` |
| 構成 | 2モジュール（enchantplus / customenchants） |
| softdepend | CasinoPlugin, LuckPerms |
| 設定ファイル | `plugins/EnchantPack/config.yml` ＋ `plugins/EnchantPack/modules/<id>.yml` |

!!! info "統合プラグインです"
    EnchantPack は、旧 **EnchantPlus**（カスタム金床GUI ＋ 19種の独自エンチャント）と旧 **CustomEnchants**（村人取引で配布される6種の別系統独自エンチャント）を1つの jar に統合した新プラグインです。`ModuleRegistry` パターン（TerrainTools / CasinoPlugin / MobBall と同様）で、`config.yml` の `modules.<id>.enabled` を見て有効なモジュールだけを起動します。あるモジュールの起動に失敗しても他のモジュールは動き続ける設計です。**旧2プラグインのコマンド・権限ノード・既存配布済みアイテムはすべてそのまま動作します**。

## 導入手順

1. ビルドした `EnchantPack-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. （既存サーバーから移行する場合）`plugins/EnchantPlus*.jar` と `plugins/CustomEnchants*.jar` を削除する。
3. サーバーを起動すると `plugins/EnchantPack/config.yml` と `plugins/EnchantPack/modules/{enchantplus,customenchants}.yml` が自動生成される。
4. `config.yml` の `modules:` ブロックで、使いたいモジュールだけを `enabled: true` にする。
5. 各モジュールの個別設定（エンチャントごとの最大Lv・基礎値・村人取引設定など）は `modules/<id>.yml` を編集する。
6. 設定を変更したら、共通部分は `/enchantpack reload`、各モジュールの config は `/enchant reload`（EnchantPlus）／`/customenchants reload`（CustomEnchants）で再読み込みする。

## config.yml（共通）

`config.yml` は **全体設定とモジュール有効フラグのみ** を扱います。エンチャント個別の設定は `modules/<id>.yml` に分離されています。

```yaml
modules:
  enchantplus:
    enabled: true
  customenchants:
    enabled: true

debug:
  verbose: false
```

| キー | 既定値 | 説明 |
|---|---|---|
| `modules.enchantplus.enabled` | `true` | EnchantPlus モジュール（カスタム金床GUI + 19種エンチャント + `/enchant` `/enchantvillager`）の有効化フラグ |
| `modules.customenchants.enabled` | `true` | CustomEnchants モジュール（PDC ベースの独自エンチャント6種 + 村人取引注入 + `/customenchants`）の有効化フラグ |
| `debug.verbose` | `false` | 詳細ログ出力（現状未使用、将来拡張用） |

!!! tip "モジュールの ON / OFF"
    `enabled: false` にしたモジュールは onEnable が呼ばれず、コマンド・リスナーも一切登録されません。「片方だけ動かしたい」運用にも対応できます。両方 `true` が標準構成です。

## enchantplus モジュール（`modules/enchantplus.yml`）

旧 `plugins/EnchantPlus/config.yml` と **完全互換** です。既存設定はそのままリネームコピーで移行できます。

### GUI・金床設定

| キー | 既定値 | 説明 |
|---|---|---|
| `gui-title` | `§6§lEnchantPlus` | カスタム金床GUIのタイトル |
| `cost-multiplier` | 5 | コスト倍率（現状未使用） |
| `allow-all-players` | true | 全プレイヤーへの使用許可フラグ |
| `debug` | false | デバッグログの出力 |

### 金床クリック（`anvil-click`）

| キー | 既定値 | 説明 |
|---|---|---|
| `anvil-click.enabled` | true | 金床ブロックの右クリックでGUIを開く |
| `anvil-click.require-permission` | true | `enchantplus.gui.use` 権限を必須にする |
| `anvil-click.allow-vanilla-on-sneak` | true | スニーク中はバニラの金床GUIを開く |

### ポータブル金床（`portable-anvil`）

| キー | 既定値 | 説明 |
|---|---|---|
| `portable-anvil.enabled` | true | ポータブル金床アイテム機能の有効化 |
| `portable-anvil.require-permission` | true | `enchantplus.gui.use` 権限を必須にする |

### 村人取引（`villager_trades`）

| キー | 既定値 | 説明 |
|---|---|---|
| `villager_trades.enabled` | true | エンチャント商人の取引機能 |
| `villager_trades.emerald_cost` | 64 | 取引に必要なエメラルド数 |
| `villager_trades.enchant_level` | 1 | 取引で入手できるエンチャント本のレベル |

### エンチャント個別設定（`enchantments`）

19種類のエンチャントが `enchantments.<ID>` に並びます。各エンチャントは次の4キーを持ちます。

| キー | 説明 |
|---|---|
| `enabled` | エンチャントの有効／無効 |
| `max_level` | 最大レベル（全エンチャント既定値 25） |
| `base_value` | レベルごとの効果量（エンチャントごとに異なる） |
| `bedrock_compatible` | 統合版（Geyser/Bedrock）対応フラグ |

ID 一覧: `movement_speed` / `knockback_resistance` / `step_height` / `jump_boost` / `block_range` / `experience_multiplier` / `mining_speed` / `luck` / `item_repair` / `fire_resistance` / `regeneration` / `absorption` / `armor_toughness` / `thorns` / `water_breathing` / `night_vision` / `invisibility` / `saturation` / `slow_falling`

!!! note "設定変更後は `/enchant reload`"
    `modules/enchantplus.yml` を編集したら `/enchant reload` を実行してください。エンチャントの有効状態・最大レベル・効果量などが再読み込みされます。

## customenchants モジュール（`modules/customenchants.yml`）

旧 `plugins/CustomEnchants/config.yml` と **完全互換** です。

```yaml
enchantments:
  movement_speed:        { enabled: true, base_value: 2.0, max_level: 5 }
  knockback_resistance:  { enabled: true, base_value: 1.0, max_level: 5 }
  step_height:           { enabled: true, base_value: 0.1, max_level: 5 }
  jump_boost:            { enabled: true, base_value: 0.1, max_level: 5 }
  block_range:           { enabled: true, base_value: 0.1, max_level: 5 }
  experience_multiplier: { enabled: true, base_value: 5.0, max_level: 5 }

villager_trades:
  enabled: true
  emerald_cost: 64
  enchant_level: 1

debug: false
```

各エンチャントは `enabled` / `base_value` / `max_level` の3キーを持ちます。実効果は `base_value × 合計レベル`（部位ごとの合算）です。`villager_trades.enabled: true` のときは、**村人スポーン時に独自取引が自動注入されます**（Phase E 改修で追加）。

!!! note "設定変更後は `/customenchants reload`"
    `modules/customenchants.yml` を編集したら `/customenchants reload` を実行してください。エンチャント有効状態・最大Lv・効果量・村人取引が再読み込みされ、オンラインプレイヤーへの効果も更新されます。

## 統合管理コマンド

EnchantPack は新たに統合管理コマンド `/enchantpack`（エイリアス `/ep`）を提供します。

| コマンド | 必要権限 | 説明 |
|---|---|---|
| `/enchantpack modules` | `enchantpack.admin` | 登録済みモジュールと ENABLED / DISABLED 状態を表示 |
| `/enchantpack reload` | `enchantpack.admin` | メイン `config.yml` を再読み込み（モジュール個別 config は対象外） |
| `/enchantpack version` | なし | EnchantPack のバージョンを表示 |

!!! note "モジュール個別 config のリロード"
    `/enchantpack reload` は **メイン `config.yml` のみ** を再読み込みします。`modules/enchantplus.yml` は `/enchant reload`、`modules/customenchants.yml` は `/customenchants reload` を使ってください。

## 既存コマンド一覧（モジュール由来）

旧プラグインのコマンドはすべて維持されています。

### enchantplus モジュール由来

| コマンド | エイリアス | コマンド自体の権限（plugin.yml） | 説明 |
|---|---|---|---|
| `/enchantplus` | `/eanvil` | `enchantplus.command.gui`（default: op） | カスタム金床GUIをコマンドから開く（プレイヤーは金床右クリックで代替） |
| `/enchant <list\|give\|portableanvil\|reload\|help>` | `/customenchant` | `enchantplus.command.use`（default: op） | エンチャント本給付・ポータブル金床・リロード等（サブコマンドごとに追加権限あり。`portableanvil` は `portable` でも可） |
| `/enchantvillager` | `/evillager`, `/enchantmerchant` | `enchantplus.villager.summon`（default: op） | 実行位置にエンチャント商人を召喚 |

### customenchants モジュール由来

| コマンド | エイリアス | コマンド自体の権限（plugin.yml） | 説明 |
|---|---|---|---|
| `/customenchants <list\|give\|reload\|help>` | `/cenchants` | `customenchants.admin`（default: op） | カスタムエンチャント本付与・リロード等（list/help はサブコマンド側で権限を問いません） |

!!! warning "`/ce` エイリアスは削除済み"
    旧 CustomEnchants の `/ce` エイリアスは **CaveEraser** と衝突するため、EnchantPack 統合時に削除されました。CustomEnchants 系のショートカットは `/cenchants` のみとなります。

## 権限ノード

旧2プラグインの権限ノードは **完全に維持** されています。LuckPerms 等の既存設定は無修正でそのまま動作します。

### EnchantPack 統合（新規）

| 権限ノード | 既定 | 説明 |
|---|---|---|
| `enchantpack.admin` | OP | `/enchantpack modules`・`/enchantpack reload` の実行を許可 |

### EnchantPlus モジュール由来

#### プリセットグループ

| 権限ノード | 対象 | 含まれる内容 |
|---|---|---|
| `enchantplus.player` | 一般プレイヤー | GUI使用・一覧表示・商人と取引・全エンチャント使用 |
| `enchantplus.moderator` | モデレーター | player の全権限 ＋ 自分への本付与・商人召喚 |
| `enchantplus.admin` | 管理者 | `enchantplus.*`（全権限） |

#### 個別権限（ワイルドカード）

| 権限ノード | 説明 |
|---|---|
| `enchantplus.*` | 全権限 |
| `enchantplus.gui.*` | GUI関連の全権限 |
| `enchantplus.command.*` | コマンド関連の全権限 |
| `enchantplus.villager.*` | 村人関連の全権限 |
| `enchantplus.enchant.*` | 全19エンチャントの使用権限 |

#### GUI・コマンド・村人権限

| 権限ノード | 説明 |
|---|---|
| `enchantplus.gui.use` | カスタム金床GUIを開く |
| `enchantplus.gui.unlimited` | 経験値コストを無制限（常に0レベル）にする |
| `enchantplus.command.list` | `/enchant list` を使用 |
| `enchantplus.command.give` | 自分にエンチャント本／ポータブル金床を付与 |
| `enchantplus.command.give.others` | 他プレイヤーに付与 |
| `enchantplus.command.reload` | `/enchant reload` を使用 |
| `enchantplus.command.debug` | デバッグ用権限 |
| `enchantplus.villager.summon` | エンチャント商人を召喚 |
| `enchantplus.villager.trade` | エンチャント商人と取引 |

#### エンチャント個別使用権限（19種）

`enchantplus.enchant.<ID>` の形式で19種類あります。`enchantplus.enchant.*` で一括付与できます。Phase E で `EnchantmentManager.canUseEnchant()` から実際にチェックされるようになりました（権限が無いプレイヤーは効果が無音で除去されます）。

| ID | エンチャント名 |
|---|---|
| `movement_speed` | 移動速度上昇 |
| `knockback_resistance` | ノックバック耐性 |
| `step_height` | 段差上限解除 |
| `jump_boost` | ジャンプ力上昇 |
| `block_range` | ブロック探索範囲 |
| `experience_multiplier` | 経験値取得倍率 |
| `mining_speed` | 採掘速度上昇 |
| `luck` | 幸運の祝福 |
| `item_repair` | アイテム修理 |
| `fire_resistance` | 炎耐性上昇 |
| `regeneration` | 再生能力 |
| `absorption` | 吸収シールド |
| `armor_toughness` | 防御力強化 |
| `thorns` | 棘の鎧強化 |
| `water_breathing` | 水中呼吸延長 |
| `night_vision` | 暗視能力 |
| `invisibility` | 透明化 |
| `saturation` | 満腹度維持 |
| `slow_falling` | 落下ダメージ無効 |

### CustomEnchants モジュール由来

| 権限ノード | 既定 | 説明 |
|---|---|---|
| `customenchants.admin` | `op` | `/customenchants reload`・`/customenchants give` の実行を許可 |
| `customenchants.trade` | `true`（全員） | エンチャント村人との取引許可（`plugin.yml` で定義、LuckPerms 等で個別撤回可能） |

!!! example "LuckPerms 設定例"
    ```bash
    # 一般プレイヤー全員に EnchantPlus 基本権限
    /lp group default permission set enchantplus.player true

    # モデレーター・管理者
    /lp group moderator permission set enchantplus.moderator true
    /lp group admin permission set enchantplus.admin true

    # EnchantPack 統合管理は default で OP 持ち
    # CustomEnchants admin も default で OP 持ち
    ```

!!! warning "既存サーバーからの移行"
    旧 EnchantPlus / CustomEnchants から EnchantPack に差し替える場合の手順は次の通りです。

    1. サーバーを停止する。
    2. `plugins/EnchantPlus*.jar` と `plugins/CustomEnchants*.jar` を削除する。
    3. `EnchantPack-1.0.0.jar` を `plugins/` に配置する。
    4. （任意）旧設定を引き継ぐ場合は次のようにリネームコピーする:
        - `plugins/EnchantPlus/config.yml` → `plugins/EnchantPack/modules/enchantplus.yml`
        - `plugins/CustomEnchants/config.yml` → `plugins/EnchantPack/modules/customenchants.yml`
    5. サーバーを起動する。

    **重要**: 既存配布済みのエンチャント本・防具は、NamespacedKey の owner を旧プラグイン名（`enchantplus` / `customenchants`）で維持しているため、**無修正で認識・動作します**。プレイヤーが持っているアイテムを作り直す必要はありません。**LuckPerms 等の権限設定も無修正で OK** です（旧 `enchantplus.*` / `customenchants.*` ノードがそのまま参照されます）。

## トラブルシューティング

??? failure "あるモジュールのコマンドが「不明なコマンド」になる"
    そのモジュールが `config.yml` の `modules.<id>.enabled: false` で無効化されている可能性があります。無効モジュールはコマンド・リスナーが一切登録されません。起動ログに「module [<id>] は config で無効化されています」が出ていないか確認してください。

??? failure "起動ログに「module [<id>] の有効化に失敗しました」と出る"
    そのモジュールの onEnable で例外が発生しています。`ModuleRegistry` は例外を捕捉してログに残すため、他モジュールは動き続けますが、該当モジュールは停止状態です。スタックトレースを確認し、`modules/<id>.yml` の設定不備などを疑ってください。

??? failure "OPなのに EnchantPlus 系コマンドが使えない"
    仕様です。EnchantPlus モジュールは OP権限ではなく個別の権限ノードを参照します。LuckPerms 等で `enchantplus.admin`（または該当する権限）を付与してください。

??? failure "金床を右クリックしてもカスタム金床GUIが開かない"
    `modules/enchantplus.yml` の `anvil-click.enabled` が `true` か、プレイヤーに `enchantplus.gui.use` 権限があるか（`require-permission: true` の場合）を確認してください。スニーク中はバニラ金床が開く仕様です。設定変更後は `/enchant reload` を実行します。

??? failure "CustomEnchants の村人取引が出現しない"
    `modules/customenchants.yml` の `villager_trades.enabled: true` を確認してください。EnchantPack では Phase E 改修で **村人スポーン時に setRecipes 注入** されるようになっており、`enabled: true` のエンチャントが1つ以上あれば自動で取引が設定されます。

??? failure "既存配布済みのエンチャント本が認識されない"
    本来は **NamespacedKey owner を旧名（`enchantplus` / `customenchants`）で維持** しているため認識されるはずです。もし発生する場合は、Phase 修正済みの EnchantPack-1.0.0.jar（NamespacedKey 修正版）が配置されているかを確認してください。古いビルド（owner が `enchantpack` のもの）では既存アイテムが認識されません。

??? failure "`/ep` を打つと別のプラグインが反応する"
    `/ep` は EnchantPack の `/enchantpack` のエイリアスですが、Bukkit のプラグインロード順により他プラグインの `/ep` と競合する可能性があります。確実に EnchantPack を呼ぶには `/enchantpack:ep` または正式名 `/enchantpack` を使ってください。

??? failure "エンチャントの効果が反映されない"
    防具に適用されているか、その防具を装備しているかを確認してください。EnchantPlus 側は `modules/enchantplus.yml` で `debug: true`、CustomEnchants 側は `modules/customenchants.yml` で `debug: true` にすると詳細ログが出力され、原因の切り分けに役立ちます。EnchantPlus 側は権限チェックが入るため、プレイヤーに `enchantplus.enchant.<ID>` 権限があるかも確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← EnchantPack 概要へ](index.md){ .md-button }
