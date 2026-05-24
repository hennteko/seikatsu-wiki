<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# EnchantPlus ― OP・運営ガイド { .page-op #enchantplus-op }

EnchantPlus の導入・config・権限・管理コマンド・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | EnchantPlus |
| バージョン | 2.0.0（plugin.yml の表記。ドキュメント上は v2.1.0 機能を含む） |
| api-version | 26.1.2 |
| メインコマンド | `/enchantplus`（`/ep`・`/eanvil`）、`/enchant`（`/ce`・`/customenchant`）、`/enchantvillager`（`/evillager`・`/enchantmerchant`） |
| 依存プラグイン | なし（権限付与に LuckPerms 等の権限プラグインを推奨） |
| 設定ファイル | `plugins/EnchantPlus/config.yml` |

!!! warning "OP権限だけではコマンドを実行できません"
    EnchantPlus は **OP権限のみではコマンドを実行できません**。`/enchantplus` を含むすべてのコマンドは個別の権限ノードを参照します。LuckPerms などの権限プラグインで権限を付与してください（後述「権限ノード」参照）。

## 導入手順

1. ビルドした `EnchantPlus-2.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/EnchantPlus/config.yml` が自動生成される。
3. LuckPerms 等で `enchantplus.player`・`enchantplus.moderator`・`enchantplus.admin` などの権限を付与する。
4. 設定を変更したら `/enchant reload` で再読み込みする。

!!! note "動作要件"
    Paper 1.21 系のサーバーで動作します。pom.xml では Java 25／paper-api 26.1.2 系を参照しています。導入前にサーバーのバージョンとの整合を確認してください。

## config.yml 主要項目

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

各エンチャントは `enchantments.<ID>` 以下に次のキーを持ちます。

| キー | 説明 |
|---|---|
| `enabled` | エンチャントの有効／無効 |
| `max_level` | 最大レベル（全エンチャント既定値 25） |
| `base_value` | レベルごとの効果量（エンチャントごとに異なる） |
| `bedrock_compatible` | 統合版（Geyser/Bedrock）対応フラグ |

!!! note "設定変更後は `/enchant reload`"
    `config.yml` を編集したら `/enchant reload` を実行してください。エンチャントの有効状態・最大レベル・効果量などが再読み込みされます。

## 権限ノード

EnchantPlus は多階層の権限ノードを持ちます。すべて既定値は `false` です。実用上は下記のプリセットグループを付与するのが簡単です。

### プリセットグループ

| 権限ノード | 対象 | 含まれる内容 |
|---|---|---|
| `enchantplus.player` | 一般プレイヤー | GUI使用・一覧表示・商人と取引・全エンチャント使用 |
| `enchantplus.moderator` | モデレーター | player の全権限 ＋ 自分への本付与・商人召喚 |
| `enchantplus.admin` | 管理者 | `enchantplus.*`（全権限） |

### 個別権限（ワイルドカード）

| 権限ノード | 説明 |
|---|---|
| `enchantplus.*` | 全権限 |
| `enchantplus.gui.*` | GUI関連の全権限 |
| `enchantplus.command.*` | コマンド関連の全権限 |
| `enchantplus.villager.*` | 村人関連の全権限 |
| `enchantplus.enchant.*` | 全19エンチャントの使用権限 |

### GUI・コマンド・村人権限

| 権限ノード | 説明 |
|---|---|
| `enchantplus.gui.use` | カスタム金床GUIを開く |
| `enchantplus.gui.unlimited` | 経験値コストを無制限（常に0レベル）にする |
| `enchantplus.command.list` | `/enchant list` を使用 |
| `enchantplus.command.give` | 自分にエンチャント本／ポータブル金床を付与 |
| `enchantplus.command.give.others` | 他プレイヤーに付与 |
| `enchantplus.command.reload` | `/enchant reload` を使用 |
| `enchantplus.command.debug` | デバッグ用権限（plugin.yml に定義あり） |
| `enchantplus.villager.summon` | エンチャント商人を召喚 |
| `enchantplus.villager.trade` | エンチャント商人と取引 |

### エンチャント個別使用権限

`enchantplus.enchant.<ID>` の形式で19種類あります。`enchantplus.enchant.*` で一括付与できます。

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

!!! example "LuckPerms 設定例"
    ```bash
    # 一般プレイヤー全員に基本権限
    /lp group default permission set enchantplus.player true

    # モデレーター・管理者
    /lp group moderator permission set enchantplus.moderator true
    /lp group admin permission set enchantplus.admin true
    ```

## 管理コマンド

| コマンド | 必要権限 | 説明 |
|---|---|---|
| `/enchant give <プレイヤー> <ID> <レベル>` | `enchantplus.command.give`（他人は `.give.others`） | エンチャント本を付与 |
| `/enchant portableanvil [プレイヤー]`（`portable`） | `enchantplus.command.give`（他人は `.give.others`） | ポータブル金床アイテムを付与 |
| `/enchant list` | `enchantplus.command.list` | 独自エンチャント一覧を表示 |
| `/enchant reload` | `enchantplus.command.reload` | config.yml を再読み込み |
| `/enchant help` | なし | コマンドヘルプを表示 |
| `/enchantvillager`（`/evillager`・`/enchantmerchant`） | `enchantplus.villager.summon` | 実行位置にエンチャント商人を召喚 |

!!! note "エンチャント商人の召喚"
    `/enchantvillager` を実行すると、その場に司書（職業 LIBRARIAN・レベル5）の村人が召喚され、19種類すべての取引が設定されます。商人はAIが無効化されその場に固定され、遠くに離れても消えません。

## トラブルシューティング

??? failure "OPなのにコマンドが使えない"
    仕様です。EnchantPlus は OP権限ではなく個別の権限ノードを参照します。LuckPerms 等で `enchantplus.admin`（または該当する権限）を付与してください。

??? failure "`/ce` が別のプラグインのコマンドになってしまう"
    `/ce` は EnchantPlus の `/enchant` のエイリアス（plugin.yml で `aliases: [ce, customenchant]`）ですが、**CustomEnchants・CaveEraser など他プラグインも `/ce` を使う場合があり、エイリアスが競合します**。どのプラグインが優先されるかはロード順に依存するため、運用ではエイリアスではなく **正式名の `/enchant` を使うよう案内** してください。確実に EnchantPlus を呼ぶには `/enchantplus:ce` のように `プラグイン名:コマンド` 形式が使えます。同様に `/ep`・`/eanvil`・`/evillager` 等も他プラグインと衝突する可能性があるため、競合時は正式コマンド名を周知してください。

??? failure "金床を右クリックしてもGUIが開かない"
    `anvil-click.enabled` が `true` か、プレイヤーに `enchantplus.gui.use` 権限があるか（`require-permission: true` の場合）を確認してください。スニーク中はバニラ金床が開く仕様です。設定変更後は `/enchant reload` を実行します。

??? failure "ポータブル金床が機能しない"
    `portable-anvil.enabled` が `true` か、`enchantplus.gui.use` 権限があるかを確認してください。ポータブル金床はPDCタグで判定されるため、`/enchant portableanvil` で正規に配布されたアイテムである必要があります。

??? failure "エンチャント商人の召喚に失敗する／取引が表示されない"
    取引はエンチャントが1つも有効でないと作成されません。`config.yml` で各エンチャントの `enabled` が `true` か確認し、`/enchant reload` 後に再度 `/enchantvillager` を実行してください。詳細はサーバーログに出力されます。

??? failure "エンチャントの効果が反映されない"
    防具に適用されているか、その防具を装備しているかを確認してください。`config.yml` で `debug: true` にすると詳細ログが出力され、原因の切り分けに役立ちます。

## 実装・ドキュメントについての注意

!!! warning "バージョン表記のずれ"
    `plugin.yml` の `version` は `2.0.0` ですが、ソースには19種類すべてのエンチャントクラスが登録されており、付属ドキュメントでは v2.1.0 として 13種類追加・金床右クリック・ポータブル金床が説明されています。実体は v2.1.0 相当ですが、バージョン番号が更新されていない点に注意してください。

- 統合版（Geyser/Bedrock）では `step_height`・`block_range` が非対応、`armor_toughness` が部分対応です。統合版プレイヤーが多い場合は config で該当エンチャントの扱いを検討してください。
- `/enchant` には `portableanvil` サブコマンドがありますが、`debug` サブコマンドはコマンド処理に実装されていません（`enchantplus.command.debug` 権限と `config.yml` の `debug` キーは存在します）。デバッグは `config.yml` の `debug: true` ＋ `/enchant reload` で切り替えてください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← EnchantPlus 概要へ](index.md){ .md-button }
