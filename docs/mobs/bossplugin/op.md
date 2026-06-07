<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# BossPlugin ― OP・運営ガイド { .page-op #bossplugin-op }

BossPluginの導入・召喚コマンド・各ボスのconfig・権限・トラブルシュートをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | BossPlugin |
| バージョン | 1.0 |
| api-version | 26.1.2 |
| メインクラス | `com.yourname.bossplugin.BossPlugin` |
| メインコマンド | `/bossspawn <zombie\|skeleton\|spider> [hp]` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/BossPlugin/zombie_boss.yml` / `skeleton_boss.yml` / `spider_boss.yml` |

## 導入手順

1. ビルドした `BossPlugin-1.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/BossPlugin/` 配下に3つの設定ファイルが自動生成される。
3. 必要に応じて各YAMLを編集する。設定の動的リロードコマンドは **未実装**（後述）のため、変更後はサーバー再起動が必要。
4. OPで `/bossspawn <type> [hp]` を実行してボスを召喚する。

!!! note "設定ファイルの構成"
    プラグイン全体共通の `config.yml` は存在しません。各ボスごとに独立したYAML（`zombie_boss.yml` / `skeleton_boss.yml` / `spider_boss.yml`）で設定します。これらはサーバー起動時に1回だけ読み込まれます。

## `/bossspawn` コマンド

```text title="ボスを召喚する（実行者の現在地）"
/bossspawn <zombie|skeleton|spider> [hp]
```

- **プレイヤー専用**（コンソールからは実行不可、「このコマンドはプレイヤーのみ実行できます。」と返ります）。
- 実行者の **現在位置にボスを召喚** します。
- `[hp]` を省略すると、各YAMLの `default-hp` が使用されます。
- HPは `0` 以下や非数値だとエラー、正の数値のみ受理。
- TAB補完：第1引数は `zombie / skeleton / spider`、第2引数は `500 / 1000 / 2000 / 5000 / 10000` を候補として提示。

!!! warning "実体エンティティのHPは最大1024でクランプ"
    内部HP（プラグイン管理値）は `[hp]` で指定した値そのままですが、Bukkitエンティティの `MAX_HEALTH` は **1024.0が上限**（`Math.min(1024.0, maxHp)`）として扱われます。表示HPは内部HPに比例してスケーリングされます。

### 召喚されるエンティティ

| ボスタイプ | 実体 | 既定名（color codeは `&` 形式） |
|---|---|---|
| `zombie` | `Giant`（巨大ゾンビ） | `&c&lExplosive Boss` |
| `skeleton` | `WitherSkeleton` | `&6&lSkeleton Archer` |
| `spider` | `Spider`（SCALE属性で2.0倍に拡大） | `&5&lVenom Broodmother` |

## 各ボスの既定パラメータ

### Zombie Boss（`zombie_boss.yml`）

| 項目 | 既定値 |
|---|---|
| `default-hp` | **10000.0** |
| 攻撃方式 | 上空から落下するファイヤーボール（地形破壊なし、半径5の爆発、最大10ダメージ） |
| Phase1 (HP100-70%) | 攻撃間隔30tick・3発・落下速度1.0 |
| Phase2 (HP70-30%) | 攻撃間隔20tick・6発・範囲1.5倍 |
| Phase3 (HP30-0%) | 攻撃間隔10tick・9発＋5秒ごとの地上爆発（3ヶ所） |
| バリア | 30秒ごとに10秒間、被ダメ90%軽減 |
| ノックバック | 半径5ブロック・強度2.0 |
| ミニオン | 75%/50%/25%でゾンビ・スケルトンを召喚（人数 × 5、上限`cap: 10`） |
| 定期スポーン | 60秒ごとに半径10内へ ZOMBIE×3・SKELETON×2・SPIDER×1 |
| 距離制限 | 50ブロック以内に誰もいないと毎tick HPの1%回復 |
| HPバー色 | Phase1=GREEN / Phase2=YELLOW / Phase3=RED |

### Skeleton Boss（`skeleton_boss.yml`）

| 項目 | 既定値 |
|---|---|
| `default-hp` | **8000.0** |
| 装備 | ダイヤフルセット＋エンチャ付き弓（POWER III＋FLAME I）、ドロップ率0 |
| 通常矢 | 16本・拡散角15度・ダメージ5・速度3 |
| 追尾矢 | 30%の確率、追尾速度0.1、持続100tick |
| 爆発矢 | 20%の確率、半径3・8ダメージ |
| 放射攻撃 | 3秒ごと32本・ダメージ5・速度2.5 |
| 矢の雨 | 3秒ごと50本・半径15・落下高度30・10ダメージ |
| 連射モード | 7.5秒おき、2秒間 毎秒5本・5ダメージ・速度4 |
| スピードブースト | 5秒ごとに3秒、速度倍率2.5 |
| 矢シールド | 5秒ごとに5秒、24本・被ダメ50%軽減 |
| Phase2 | 攻撃間隔10tick、矢の数1.5倍 |
| Phase3 | 攻撃間隔10tick、矢の数2倍 |
| ミニオン | 75%/50%でスケルトン、25%でスケルトン＋ストレイ（人数 × 5、上限`cap: 10`） |
| HPバー色 | Phase1=BLUE / Phase2=PURPLE / Phase3=RED |

### Spider Boss（`spider_boss.yml`）

| 項目 | 既定値 |
|---|---|
| `default-hp` | **1200.0** |
| サイズ倍率 | 2.0（SCALE属性） |
| 巣エリア | 半径30、外に5秒で強制テレポート |
| 糸フィールド | 5秒ごと3個ずつ、最大50個、半径15 |
| 糸拘束（Web Shot） | 3秒ごと、射程15、速度低下III・5秒 |
| 毒噴射（Venom Spray） | 4秒ごと、扇60度・射程10、ダメージ4＋毒II（5秒） |
| 毒の霧（Poison Cloud） | 6秒ごと、半径8・持続5秒、毎tick0.5ダメージ |
| 糸引き寄せ（Web Pull） | 5秒ごと、射程20、引き強度2.0 |
| 卵産み | 7.5秒ごと、卵3個、孵化10秒（DRAGON_EGG） |
| 天井戦闘 | HP70%以下から、10秒地上→5秒天井(+15)を反復 |
| 怒り状態 | HP30%以下、攻撃間隔×0.6、召喚数×2.0 |
| 毒レベル | 最大5、10秒で1減衰、L1=毒I・L3=毒II・L5=毒III |
| ミニオン | 75%でケイブスパイダー×3、50%でスパイダー×4、25%で両方（速度上昇II付与、上限`cap: 10`） |
| HPバー色 | Phase1=GREEN / Phase2=YELLOW / Phase3=RED |

!!! tip "ミニオン数は人数で乗算される"
    各HPしきい値で召喚される数は **`min(オンライン人数, cap) × YAMLの値`** です。`cap` の既定は10。100人接続している環境でも上限10倍までで打ち止まります。

## 共通仕様（全ボス）

- **ダメージ処理**：`EntityDamageByEntityEvent` を `setCancelled(true)` で打ち消し、プラグイン内部のHPを直接削ります。Bukkitエンティティの `MAX_HEALTH` は1024.0でクランプ。
- **撃破時**：`EntityDeathEvent` で **通常ドロップを全消去**、経験値オーブを **150** ドロップ。召喚済みミニオンも消去。
- **距離制限**：全ボス共通で50ブロック以内に誰もいないと毎tick HPの1%回復。
- **HPバー**：召喚時に半径50内のプレイヤーへ自動表示。新規ログインも `PlayerJoinEvent` で同距離以内なら追加。
- **`onDisable()`**：サーバー停止／リロード時、登録中の全ボスを `removeAllBosses()` で削除します（リスナー解除・タスクキャンセル・BossBar非表示も同時実行）。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `bossplugin.spawn` | op | `/bossspawn` の使用許可。`plugin.yml` の `permission` と `BossSpawnCommand` 内の `hasPermission` チェックが同名で揃っています。 |

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/bossspawn <type> [hp]` | `bossplugin.spawn` | ボス召喚（実行者の現在地） |

!!! warning "リロード／停止／削除コマンドは未実装"
    現在の実装には `/boss kill` や `/bossplugin reload` のような管理コマンドはありません。召喚中のボスを安全に取り除く正規手段は **サーバー停止（`onDisable` がトリガ）** か、エンティティを直接Kill（プラグインがdeath eventを受けて後処理を行う）するしかありません。

## トラブルシューティング

??? failure "`/bossspawn` を非OPに渡したのに「権限がありません」と言われる"
    `plugin.yml` の `permission: bossplugin.spawn` と `BossSpawnCommand` 内の `hasPermission("bossplugin.spawn")` は同一ノードを参照しています。OP は既定で通り、非OP に渡す場合は LuckPerms 等で `bossplugin.spawn` を直接付与してください。

??? failure "ボスを倒したのにアイテムが何も出ない"
    仕様です。`BossListener.onBossDeath` で `event.getDrops().clear()` を実行し、ドロップは経験値150のみに固定しています。報酬を変えるにはソース改修が必要です。

??? failure "ボスのHPが減らない／勝手に回復する"
    バリア／矢シールドなどの軽減フェーズか、50ブロック以内にプレイヤーがいない状態の自動回復です。距離制限（`distance-limit.heal-rate`）を `0.0` に下げるか、`enabled: false` で無効化できます。

??? failure "Spiderボスから一定距離以上離れられない"
    `spider_boss.nest-area`（半径30、5秒で強制テレポート）の仕様です。回避戦闘ができないため、無効化したい場合は `nest-area.enabled: false` に変更してください。

??? failure "ボスの実体HPが1024で頭打ちになっている"
    Bukkitエンティティの `MAX_HEALTH` は1024.0でクランプされる仕様です（`Math.min(1024.0, maxHp)`）。`/bossspawn zombie 50000` のように指定しても、内部HPは50000で管理されますが実体HPバーは1024相当にスケーリングされます。ボスバー（プラグイン側）は正しい比率で表示されます。

??? failure "config.yml を編集しても反映されない"
    各YAMLは **サーバー起動時に1回だけ読み込まれ**、各ボスのコンストラクタで全値をキャッシュします。動的リロードは未実装なので、サーバーを再起動してください。

??? failure "サーバー停止時にボスが残っている／クラッシュする"
    `onDisable()` で `removeAllBosses()` が走るため、通常は全ボスが消えるはずです。プラグイン無効化が異常終了した場合のみ、Giant/WitherSkeleton/Spiderの本体エンティティが残る可能性があります。`/kill @e[type=...]` で個別に削除してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← BossPlugin 概要へ](index.md){ .md-button }
