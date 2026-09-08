<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# BossPlugin ― OP・運営ガイド { .page-op #bossplugin-op }

BossPluginの導入・召喚コマンド・襲撃（WAVE）モードの構築・各ボスのconfig・権限・トラブルシュートをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | BossPlugin |
| バージョン | 1.0 |
| api-version | 26.1.2 |
| メインクラス | `com.yourname.bossplugin.BossPlugin` |
| コマンド | `/bossspawn <zombie\|skeleton\|spider> [hp]` ／ `/bossraid`（別名 `/raid`） |
| 依存プラグイン | なし |
| ボス定義ファイル | `plugins/BossPlugin/zombie_boss.yml` / `skeleton_boss.yml` / `spider_boss.yml` |
| 襲撃モード設定 | `config.yml` / `waves.yml` / `difficulty.yml` / `rewards.yml`（自動生成） |
| 自動生成データ | `arenas.yml`（アリーナ定義） / `records.yml`（クリア記録） |

## 導入手順

1. ビルドした `BossPlugin-1.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/BossPlugin/` 配下に各設定ファイルが自動生成される（`saveDefaultConfig()` で `config.yml`、各リソースは初回参照時に `saveResource(..., false)` で展開）。
3. 必要に応じて各YAMLを編集する。襲撃モードの設定（`config.yml` / `difficulty.yml` / `rewards.yml` / `arenas.yml` / `records.yml`）は **`/bossraid reload`** で動的に反映できる。各ボス定義YAML（`zombie_boss.yml` 等）はサーバー起動時に1回だけ読み込まれるため、変更後はサーバー再起動が必要。
4. OPで `/bossspawn <type> [hp]` を実行して単体ボスを召喚する。または `/bossraid` で襲撃アリーナを構築・プレイする（[アリーナ構築手順](#raid-setup)）。

!!! note "設定ファイルの構成"
    各ボスの細かい挙動は独立したYAML（`zombie_boss.yml` / `skeleton_boss.yml` / `spider_boss.yml`）で設定します（起動時に1回だけ読み込み）。一方、襲撃（WAVE）モードはプラグイン共通の `config.yml` と、`waves.yml`（出現テーブル）・`difficulty.yml`（難易度プリセット）・`rewards.yml`（報酬）で設定します。`config.yml` は `saveDefaultConfig()` で生成され、`/bossraid reload` で再読み込みできます。

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
- **`onDisable()`**：サーバー停止／リロード時、登録中の全ボスを `removeAllBosses()` で削除します（リスナー解除・タスクキャンセル・BossBar非表示も同時実行）。襲撃モードも `shutdownAll()` で全試合を後始末します。

## 襲撃（WAVE）モード ― 概要 { #raid }

専用アリーナで雑魚→オオモノ→最終ボスのウェーブを殲滅する協力コンテンツです。`/bossraid`（別名 `/raid`）はフラット形式のサブコマンドで、**プレイヤー全員が使えるコマンド**（参加・離脱・開始・状況確認・ランキング・記録）と、**`bossraid.admin` 権限が必要な管理コマンド**（アリーナ構築・強制停止・リロード）に分かれます。コマンドレベルでは権限を設定せず、コード側でサブコマンドごとに判定します。

### 全員が使えるコマンド

```text title="アリーナに参加（プレイヤー専用）"
/bossraid join <arena>
```

```text title="参加中のアリーナから離脱（プレイヤー専用）"
/bossraid leave
```

```text title="試合開始（難易度を省略するとプレイヤーは選択GUIが開く）"
/bossraid start <arena> [難易度]
```

```text title="難易度プリセットの一覧を表示"
/bossraid difficulties
```

```text title="設定状況を表示（arena省略で全体サマリ）"
/bossraid status [arena]
```

```text title="スコア上位ランキングを表示（上位10名）"
/bossraid ranking
```

```text title="自分のクリア記録を表示（プレイヤー専用）"
/bossraid record
```

!!! note "start の難易度フォールバック"
    `/bossraid start <arena> <難易度>` で未定義の難易度を指定すると、警告を出して **Normal（または DifficultyManager の既定）にフォールバック** して開始します。コンソールから実行する場合は難易度の省略不可（GUIを開けないため）です。

### 管理コマンド（要 `bossraid.admin`）

```text title="アリーナを新規作成"
/bossraid create <arena>
```

```text title="全アリーナ共通の『初期スポーン』を現在地に設定（離脱・終了時の戻り先）"
/bossraid setstartspawn
```

```text title="ロビースポーン（参加時の待機地点）を現在地に設定"
/bossraid setlobby <arena>
```

```text title="ゲームスポーン（開始時のスタート地点・ボス出現基点）を現在地に設定"
/bossraid setspawn <arena>
```

```text title="フィールド範囲の角を現在地に設定（1と2の2点で直方体を定義）"
/bossraid setfield <arena> <1|2>
```

```text title="敵の湧き地点を現在地に追加（複数登録可）"
/bossraid setspawner <arena>
```

```text title="視線の先の看板を参加／離脱／開始看板として登録（6ブロック以内）"
/bossraid setsign <join|leave|start|delete> <arena> [識別子]
```

```text title="アリーナの試合を強制停止"
/bossraid stop <arena>
```

```text title="設定（config/arenas/difficulty/rewards/records）を再読み込み"
/bossraid reload
```

!!! tip "看板の登録"
    `setsign` は **視線の先（6ブロック以内）の既存の看板** を対象にします。`join`／`leave` はアリーナ名を、`start` は識別子（難易度ID、省略時 `default`）を指定して登録すると、看板の文面が自動で書き換わります。`setsign delete` は識別子不要で、視線の先の登録済み看板の登録を解除します。プレイヤーは登録された看板をクリックして参加・離脱・開始ができます。

### アリーナ構築手順 { #raid-setup }

1. `/bossraid create <arena>` でアリーナを作成。
2. `/bossraid setlobby <arena>`（参加待機地点）と `/bossraid setspawn <arena>`（開始時のスタート＆ボス出現基点）を設定。
3. `/bossraid setfield <arena> 1` と `/bossraid setfield <arena> 2` でフィールド範囲の対角2点を設定。
4. `/bossraid setspawner <arena>` を複数地点で実行し、敵の湧き地点を登録（最低1つ。未登録時はゲームスポーンから湧きます）。
5. 任意で `/bossraid setstartspawn`（全体共通の戻り先）と各種看板を設定。
6. `/bossraid status <arena>` で「準備OK」になっていることを確認。ロビー・ゲームスポーン・フィールド範囲・湧き地点が揃うと参加・開始が可能になります。

!!! warning "アリーナが『準備OK』にならないと開始できない"
    ロビー・ゲームスポーン・フィールド範囲が未設定だと `isReady()` が false になり、参加・開始が拒否されます。`/bossraid status <arena>` で未設定項目（赤表示）を確認してください。

## 襲撃モードの設定ファイル

### `config.yml`（襲撃の全体設定）

```text title="試合中はアリーナ保護のためアドベンチャーモードにする（終了時に元のモードへ復元）"
raid.adventure-mode-in-game: true
```

```text title="持込装備の耐久を削る攻撃（酸攻撃など）を無効化する"
raid.protect-durability: true
```

```text title="開始に必要な最低人数（0人のみ拒否、1人開始を許可）"
raid.min-players: 1
```

### `difficulty.yml`（難易度プリセットとレーティング）

難易度は **10段階のプリセット**（peaceful / easy / normal / hard / expert / extreme / nightmare / inferno / apocalypse）と、開始時に上乗せできる **レーティング（掛け金）** で構成されます。各プリセットは以下のキーを持ちます。

```text title="プリセットの主なキー（difficulty.yml の presets.<id> 配下）"
display-name           # 表示名（例: 標準）
hp-mult                # 敵HP倍率
damage-mult            # 敵与ダメージ倍率
attack-interval-mult   # 攻撃間隔倍率（小さいほど速い）
minion-mult            # 雑魚総数（budget）の倍率
boss-count             # 最終ウェーブのボス数（1〜3）
revive-count           # 復帰回数（※現状ゲーム未使用・将来用の値）
wave-count             # 想定ウェーブ数（※実際の数は waves.yml の定義数で決まる）
rating-max             # レーティング上限
reward-mult            # 報酬（経験値）倍率
default-mutators       # 既定で付与する変異の数
```

```text title="レーティング計算（difficulty.yml の rating 配下・既定値）"
rating.step: 10              # 何ポイントごとに加算するか
rating.hp-per-step: 0.10     # 1ステップあたり HP倍率に加算
rating.damage-per-step: 0.05 # 1ステップあたり 与ダメ倍率に加算
rating.reward-per-step: 0.15 # 1ステップあたり 報酬倍率に加算
rating.mutator-step: 25      # 何ポイントごとに変異1つ解放するか
```

実効倍率は `プリセット値 ×(1 + ステップ数 × per-step)` で算出され、変異数は `default-mutators +（レーティング ÷ mutator-step）` です。

!!! info "プリセット既定値の早見（抜粋）"
    | ID | 表示名 | HP× | 与ダメ× | ボス | rating上限 | 報酬× | 既定変異 |
    |---|---|---|---|---|---|---|---|
    | peaceful | 平穏 | 0.5 | 0.5 | 1 | 0 | 0.0 | 0 |
    | normal | 標準 | 1.0 | 1.0 | 1 | 25 | 1.0 | 0 |
    | hard | 上級 | 1.5 | 1.3 | 1 | 50 | 1.5 | 0 |
    | expert | 熟練 | 2.0 | 1.6 | 1 | 75 | 2.0 | 1 |
    | extreme | 極限 | 3.0 | 2.0 | 2 | 100 | 3.0 | 2 |
    | nightmare | 悪夢 | 4.5 | 2.5 | 2 | 150 | 4.5 | 3 |
    | inferno | 煉獄 | 6.5 | 3.0 | 3 | 200 | 6.5 | 4 |
    | apocalypse | 終焉 | 10.0 | 4.0 | 3 | 300 | 10.0 | 5 |

### `rewards.yml`（クリア報酬とランク）

```text title="クリア基礎経験値（実付与 = clear-exp-base × 難易度の報酬倍率 × ランク倍率）"
clear-exp-base: 100
```

```text title="ランク（S/A/B）ごとの報酬倍率。ランクはダウン回数で決定（0回=S, 2回以下=A, それ以上=B）"
rank-mult:
  S: 2.0
  A: 1.5
  B: 1.0
```

### `waves.yml`（ウェーブ出現テーブル）

`sets.default` 配下に `wave-1`, `wave-2`, … をキー名昇順で並べ、各ウェーブで出現する敵を定義します。**実際の総ウェーブ数はこの定義数**（`plan.size()`）で決まります。

```text title="各ウェーブの主なキー"
type        # NORMAL=雑魚殲滅 / BOSS=最終ボス
budget      # そのウェーブの雑魚総数（難易度の minion-mult でスケール）
event       # none / fog / rush / geyser / tornado（特殊イベント）
zako        # 雑魚ID -> 出現重み（相対比率で抽選）
oomono      # 同時出現させるオオモノIDの一覧（steelhead / scrapper / stinger）
boss-pool   # BOSSウェーブで抽選する最終ボスID（zombie_boss / skeleton_boss / spider_boss / blaze_king）
```

```text title="利用可能な雑魚ID"
chum cohock archer shield froster jumper poisoner flyer bomber splitter stealth
```

!!! note "敵の構成（参考）"
    雑魚はバニラMobを土台に難易度倍率（HP・攻撃力）を適用。オオモノ（バクダン／テッパン／タワー）は弱点制で、それぞれ頭の弱点・背後側面・最上段のみ有効。最終ボスは3種カスタムボス（HPは各 `default-hp × hpMult`）に加え `blaze_king`（業火の巨王・基礎HP4000）を抽選で出現させます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `bossplugin.spawn` | op | `/bossspawn` の使用許可。`plugin.yml` の `permission` と `BossSpawnCommand` 内の `hasPermission` チェックが同名で揃っています。 |
| `bossraid.admin` | op | 襲撃アリーナの構築（create/set系）・`stop`・`reload` の使用許可。`/bossraid` のコマンドレベルには権限を設けず、コード側で管理サブコマンドのみこのノードを判定します。参加・開始・ランキング等は全員が使えます。 |

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/bossspawn <type> [hp]` | `bossplugin.spawn` | 単体ボス召喚（実行者の現在地） |
| `/bossraid join\|leave\|start\|status\|difficulties\|ranking\|record` | なし（全員） | 襲撃モードの参加・進行・確認系 |
| `/bossraid create\|setstartspawn\|setlobby\|setspawn\|setfield\|setspawner\|setsign\|stop\|reload` | `bossraid.admin` | 襲撃アリーナの構築・強制停止・リロード |

!!! success "単体ボスのリロードは未実装／襲撃モードはリロード可"
    `/bossspawn` で召喚した単体ボスには `/boss kill` 相当の管理コマンドはありません。召喚中のボスを取り除くには **サーバー停止（`onDisable` がトリガ）** か、エンティティを直接Kill（プラグインがdeath eventを受けて後処理を行う）します。一方、襲撃モードの設定は `/bossraid reload` で動的に再読み込みでき、試合は `/bossraid stop <arena>` で強制停止できます。

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

??? failure "（襲撃）`/bossraid join` や `start` が拒否される"
    アリーナが「準備OK」になっていない可能性があります。ロビー・ゲームスポーン・フィールド範囲が未設定だと参加・開始できません。`/bossraid status <arena>` で未設定項目（赤表示）を確認し、`setlobby`／`setspawn`／`setfield 1`／`setfield 2` を設定してください。湧き地点（`setspawner`）が0でも開始はできますが、その場合はゲームスポーンから敵が湧きます。

??? failure "（襲撃）`/bossraid reload` を実行しても挙動が変わらない"
    `reload` は `config.yml`・`arenas.yml`・`difficulty.yml`・`rewards.yml`・`records.yml` を再読み込みします。`waves.yml` は試合開始時（`WaveManager` 生成時）に読み込まれるため、進行中の試合には反映されません。次の試合から反映されます。各ボス定義YAML（`zombie_boss.yml` 等）は起動時のみ読み込みのため、変更にはサーバー再起動が必要です。

??? failure "（襲撃）difficulty.yml の revive-count / wave-count を変えても効果がない"
    `revive-count` は値として読み込まれますが、現状のゲームロジックでは未使用です（ダウン者は試合終了まで観戦のまま）。`wave-count` も参考値で、**実際のウェーブ数は `waves.yml` の定義数** で決まります。ウェーブ数を変えたい場合は `waves.yml` の `wave-N` エントリを増減してください。

??? failure "（襲撃）特攻シャケ（bomber）の爆発で地形が壊れる／装備が削れる"
    襲撃の敵の爆発は `EntityExplodeEvent` で `blockList().clear()` し、地形破壊を抑止しています。装備の耐久を削る攻撃は `config.yml` の `raid.protect-durability: true` で無効化できます。試合中はアリーナ保護のため既定でアドベンチャーモード（`raid.adventure-mode-in-game`）です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← BossPlugin 概要へ](index.md){ .md-button }
