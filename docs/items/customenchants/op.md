<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# CustomEnchants ― OP・運営ガイド { .page-op #customenchants-op }

CustomEnchants の導入・config・全エンチャント定義・トレード村人セットアップ・権限・管理コマンド・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | CustomEnchants |
| バージョン | `${project.version}`（plugin.yml はビルド時に解決） |
| 作者 | CustomEnchantsTeam |
| api-version | 26.1.2 |
| 説明 | Custom enchantments for armor with configurable effects |
| メインコマンド | `/customenchants`（エイリアス `/ce`・`/cenchants`） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/CustomEnchants/config.yml` |

!!! warning "`/ce` エイリアスが CaveEraser と衝突します"
    CustomEnchants の `/ce` は同カテゴリの **CaveEraser** プラグインの `/ce` と **完全に衝突** します。Bukkit のプラグイン読み込み順に応じて、どちらか一方の `/ce` が無効化されます。運用ではエイリアスではなく **正式名の `/customenchants`** か、衝突しない別エイリアスの **`/cenchants`** を使うようプレイヤーに案内してください。確実に CustomEnchants 側を呼ぶには `/customenchants:ce` のように `プラグイン名:コマンド` 形式が使えます。詳細は本ページ末の「トラブルシューティング」を参照してください。

## 導入手順

1. ビルドした `CustomEnchants-x.x.x.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/CustomEnchants/config.yml` が自動生成される。
3. 一般プレイヤーは既定で `customenchants.trade` を持つため、村人取引はそのまま利用可能。
4. 管理者には `customenchants.admin` を付与する（OP は既定で保有）。
5. 設定を変更したら `/customenchants reload` で再読み込みする。

!!! note "動作要件"
    Paper の `api-version: 26.1.2` を要求します。各エンチャントは Paper 26.1 以降の Attribute 名（`GENERIC_` プレフィックス削除版：`MOVEMENT_SPEED`・`KNOCKBACK_RESISTANCE`・`STEP_HEIGHT`・`BLOCK_INTERACTION_RANGE`）に対応しています。古い Bukkit/Paper では起動に失敗する可能性があります。

## config.yml

`plugins/CustomEnchants/config.yml` の全項目です（プラグインに同梱されている初期値）。

```yaml
# 各エンチャントのレベル1あたりの効果値
# レベルNの効果 = base_value * N

enchantments:
  movement_speed:
    enabled: true
    base_value: 2.0
    max_level: 5
  knockback_resistance:
    enabled: true
    base_value: 1.0
    max_level: 5
  step_height:
    enabled: true
    base_value: 0.1
    max_level: 5
  jump_boost:
    enabled: true
    base_value: 0.1
    max_level: 5
  block_range:
    enabled: true
    base_value: 0.1
    max_level: 5
  experience_multiplier:
    enabled: true
    base_value: 5.0
    max_level: 5

# 村人取引設定
villager_trades:
  enabled: true
  emerald_cost: 64
  enchant_level: 1

# デバッグモード
debug: false
```

### エンチャント個別設定

各エンチャントは `enchantments.<configKey>` 配下に次の3キーを持ちます。

| キー | 型 | 既定値 | 説明 |
|---|---|---|---|
| `enabled` | bool | `true` | エンチャントの有効／無効。`false` にすると村人取引・効果適用・`/ce list` で「無効」表示。 |
| `base_value` | double | 各種 | レベル1あたりの効果量。実効果は `base_value × 合計レベル`。 |
| `max_level` | int | `5` | 最大レベル（表示・補完での上限。内部の効果計算には強制クランプなし）。 |

### 村人取引設定（`villager_trades`）

| キー | 型 | 既定値 | 説明 |
|---|---|---|---|
| `villager_trades.enabled` | bool | `true` | 村人取引機能を有効にする。`false` だと `setupTrades` がスキップされる。 |
| `villager_trades.emerald_cost` | int | `64` | 取引に必要なエメラルド数。 |
| `villager_trades.enchant_level` | int | `1` | 取引で得られるエンチャント本のレベル。 |

### デバッグ

| キー | 型 | 既定値 | 説明 |
|---|---|---|---|
| `debug` | bool | `false` | エンチャント登録・効果適用・経験値倍率などの詳細ログを出力。 |

!!! note "設定変更後は `/customenchants reload`"
    `config.yml` を編集したら `/customenchants reload` を実行してください。エンチャントの有効状態・最大レベル・効果量・村人取引が再読み込みされ、オンラインプレイヤーへの効果も更新されます。

## 全エンチャント定義

ソース（`com.customenchants.enchantments.*`）に登録されている全6種類です。

| 表示名 | ID／configKey | 実装方式 | Lv1あたり | 最大Lv | 備考 |
|---|---|---|---|---|---|
| 移動速度上昇 | `movement_speed` | `Attribute.MOVEMENT_SPEED` を `MULTIPLY_SCALAR_1` 乗算 | +2.0% | 5 | 計算は `base_value / 100` を倍率として加算 |
| ノックバック耐性 | `knockback_resistance` | `Attribute.KNOCKBACK_RESISTANCE` を `ADD_NUMBER` 加算 | +1.0% | 5 | 計算値は `min(1.0)` でクランプ（最大 100%） |
| 段差上限解除 | `step_height` | `Attribute.STEP_HEIGHT` を `ADD_NUMBER` 加算 | +0.1ブロック | 5 | Attribute 非対応バージョンでは無効化 |
| ジャンプ力上昇 | `jump_boost` | `PotionEffectType.JUMP_BOOST` を無限時間で付与 | +0.1ブロック相当 | 5 | ポーションレベル = `ceil(effect × 5) - 1`、アイコン表示あり |
| ブロック探索範囲 | `block_range` | `Attribute.BLOCK_INTERACTION_RANGE` を `ADD_NUMBER` 加算 | +0.1ブロック | 5 | Attribute 非対応バージョンでは無効化 |
| 経験値取得倍率 | `experience_multiplier` | `PlayerExpChangeEvent`／`EntityDeathEvent`／`BlockBreakEvent`／`PlayerFishEvent` で `ceil(exp × (1 + base/100 × level))` | +5.0% | 5 | Attribute ではなくイベントで実装 |

!!! note "効果値の合算"
    効果値はプレイヤーの **全防具スロットのカスタムエンチャントレベル合計** で計算されます（`EnchantmentManager.getTotalEnchantmentLevels`）。例えばヘルメット `movement_speed II` ＋ ブーツ `movement_speed III` は合計レベル5として `base_value × 5 = 10%` の移動速度上昇になります。

!!! warning "max_level の解釈"
    `max_level` は `/ce list` の表示と `give` コマンドのタブ補完の上限に使われます。`give` 自体は「1 以上」しか検査せず、`max_level` を超える値も付与可能です。効果値の計算式も `base_value × level` で青天井のため、運営上の上限として周知してください。

## トレード村人セットアップ

CustomEnchants は **既存のバニラ村人に独自取引を追加する仕組みはありません**。取引は `VillagerTradeManager` がプラグイン起動時に「取引レシピのテンプレート」を構築するだけで、プラグインがこれを村人に注入する処理は同梱されていません。

- `setupTrades()` は起動時とリロード時に呼ばれ、`getTrades()` で `MerchantRecipe` のリストを取得できます。
- 取引は **エメラルド `emerald_cost` 個 ＋ 本1冊 → カスタムエンチャント本（レベル `enchant_level`）** で構成されます。
- 取引回数の上限は `Integer.MAX_VALUE`（実質無制限）。`setVillagerExperience(10)`・`setExperienceReward(true)`。
- 取引対象は **`enabled: true` のエンチャントのみ**。

### 運用方法

エンチャント村人をワールドに用意するには、次のいずれかの方法をとってください。

1. **手動で村人を配置し、別のプラグイン（例: ShopKeepers, VillagerMarket 等）でレシピを設定する。** その際、本ページの「全エンチャント定義」と同等のレシピを再現するか、`VillagerTradeManager#createEnchantmentBook(enchant, level)` 相当の Lore／PDC を持つアイテムを `Material.ENCHANTED_BOOK` ＋ Custom NBT で配布する設計にしてください。
2. **追加のブリッジ実装を作る。** CustomEnchants の `getVillagerTradeManager().getTrades()` を呼んで、配置済み村人の `setRecipes(...)` に流し込む小さな拡張を別途追加する運用も可能です。

!!! warning "起動ログの確認"
    起動時のサーバーログに `Setup N villager trades.`（N = 有効エンチャント数）と `Villager trades are disabled.` のどちらかが出力されます。`enabled` エンチャントが0個だと取引も0件になります。

### エンチャント本の構造（参考）

`VillagerTradeManager#createEnchantmentBook` が作成するアイテムは以下のとおりです。

- 種別: `Material.ENCHANTED_BOOK`
- 表示名: `<表示名> <ローマ数字>`（AQUA、イタリック解除）
- Lore: 「カスタムエンチャント」「<効果説明>」「（空行）」「金床で防具に適用可能」
- PDC: `customenchants:enchant_id_0` = エンチャントID（STRING）／`customenchants:enchant_level_0` = レベル（INTEGER）

`AnvilListener` はこの PDC を判定して金床での適用・統合を行います。Lore だけ模倣しても適用されません。

## 管理コマンド

`plugin.yml` 上のコマンドは `/customenchants` のみで、`reload`・`list`・`give`・`help` の4サブコマンドを持ちます。

| コマンド | 必要権限 | 説明 |
|---|---|---|
| `/customenchants list` | なし | 6種類のエンチャント状態（有効／無効、基礎値、最大Lv）を表示 |
| `/customenchants give <プレイヤー> <エンチャントID> <レベル>` | `customenchants.admin` | カスタムエンチャント本を作成して付与（レベル≥1必須） |
| `/customenchants reload` | `customenchants.admin` | `config.yml` 再読み込み＋オンラインプレイヤーの効果更新＋村人取引再構築 |
| `/customenchants help` | なし | コマンドヘルプを表示 |

エイリアス: `/ce`・`/cenchants`。タブ補完で `list`・`give`・`reload`・`help` と、`give` 第2引数のオンラインプレイヤー名、第3引数のエンチャントID、第4引数の `1`〜`5` を補完します。

!!! example "give の使用例"
    ```text
    /customenchants give Steve movement_speed 3
    /cenchants give Alex experience_multiplier 5
    ```

## 権限

`plugin.yml` に定義されている権限は **2つだけ** です。細粒度の権限ノードはありません。

| 権限ノード | 既定 | 説明 |
|---|---|---|
| `customenchants.admin` | `op` | `/customenchants reload`・`/customenchants give` の実行を許可 |
| `customenchants.trade` | `true`（全員） | エンチャント村人との取引許可（コード上は実装側で判定されないが、`plugin.yml` で定義されているため LuckPerms 等で個別撤回可能） |

!!! note "`customenchants.trade` の扱い"
    `plugin.yml` には `customenchants.trade` が `default: true` で定義されていますが、ソース上で `hasPermission("customenchants.trade")` を参照している箇所はありません。実運用では取引制限としては機能しないため、取引を禁止したい場合は `villager_trades.enabled: false` か、村人を配置しない運用で制御してください。

## `/ce` エイリアス衝突注意（重要）

CustomEnchants の `/ce` エイリアスは、同カテゴリの **CaveEraser** プラグインの `/ce` と衝突します。

- **症状**: `/ce` を打つと、どちらか一方のプラグインのコマンドだけが動作する。もう一方は `:` 付き完全修飾形でしか呼べなくなる。
- **どちらが優先されるか**: Bukkit のプラグインロード順（通常はアルファベット順）に依存します。`CaveEraser` が先にロードされる環境では `/ce` が CaveEraser に取られる、その逆も同様です。
- **`/cenchants` は競合なし**（CustomEnchants 専用）。

### 運用推奨

| 案内 | 内容 |
|---|---|
| プレイヤー向け | `/customenchants` または `/cenchants` を使うよう案内 |
| OP向け | スクリプト・マクロでは必ず `/customenchants` 正式名を使う |
| どうしても `/ce` を CustomEnchants に向けたい | `/customenchants:ce` のように `プラグイン名:コマンド` 形式で呼ぶ |
| 完全に衝突を回避したい | どちらか一方の `plugin.yml` から `aliases` を削除したカスタムビルドを使用（要メンテナンス） |

!!! warning "リロード時の挙動にも注意"
    `/reload` などで再ロードすると、`/ce` がどちらに割り当てられるかが変わる可能性があります。本番運用ではエイリアスに依存せず正式名を使うのが安全です。

## トラブルシューティング

??? failure "プレイヤーが取引できる村人がいない"
    CustomEnchants 単体では **村人の自動配置・既存村人へのレシピ注入は行いません**。`VillagerTradeManager` はレシピを保持するだけです。ShopKeepers 等の外部プラグインや独自スクリプトで、`getVillagerTradeManager().getTrades()` のレシピを村人に設定してください。

??? failure "`/ce` を打っても別のプラグインが反応する"
    CaveEraser など他プラグインと `/ce` が衝突しています。`/customenchants` または `/cenchants` を使うか、`/customenchants:ce` で完全修飾してください（詳細は上記「`/ce` エイリアス衝突注意」）。

??? failure "エンチャントの効果が反映されない"
    まず防具に正しく適用されているか確認します（Lore に「カスタムエンチャント:」が表示されるはず）。装備し直す、`/customenchants reload` を実行する、`config.yml` で対象エンチャントの `enabled: true` を確認する、`debug: true` で詳細ログを取る、の順で切り分けてください。

??? failure "段差上限解除・ブロック探索範囲が効かない"
    `Attribute.STEP_HEIGHT`・`Attribute.BLOCK_INTERACTION_RANGE` は Paper 1.20.5 以降の Attribute です。古いサーバーや、サーバー側でこれらの Attribute が無効化されている場合は適用がスキップされます（`debug: true` で警告ログが出ます）。

??? failure "ジャンプ力のアイコンが消えない／別のジャンプ力上昇と競合する"
    `JumpBoostEnchant` は `JUMP_BOOST` ポーション効果を `hasIcon=true / isAmbient=false` で付与し、`removeEffect` 時もこの組み合わせのみを削除します。バニラやビーコン由来のジャンプ力上昇とは個別に管理されます。表示されるアイコンは仕様です。

??? failure "経験値倍率が二重にかかる気がする"
    `PlayerExpChangeEvent`・`EntityDeathEvent`・`BlockBreakEvent`・`PlayerFishEvent` の4種を `priority = HIGH` で改変しています。他プラグインがさらに後段で経験値を変更すると、その分は CustomEnchants の倍率対象外です。逆に他プラグインが先に変更した値に対して CustomEnchants の倍率が乗ります。

??? failure "金床で適用できない／結果スロットが空になる"
    `AnvilListener` は「左=防具・右=本」「左=本・右=防具」「同じ部位の防具同士」のいずれかで動作します。本の PDC に `enchant_id_0` が無い（CustomEnchants 製でない）アイテムは認識されません。`/customenchants give` で作った本を使い、防具のスロット位置と種類を確認してください。

??? failure "プレイヤーをリロードした直後に効果がない"
    `PlayerJoinEvent`／`PlayerRespawnEvent` は5tick遅延、`PlayerItemHeldEvent`／`InventoryClickEvent`／ドロップ・拾得は1tick遅延で効果を再計算します。極端に重いサーバーではこの遅延中の数tickだけ効果が無い瞬間が生じます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← CustomEnchants 概要へ](index.md){ .md-button }
