<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# MobBall ― OP・運営ガイド { .page-op #mobball-op }

MobBall の導入・モジュール構成・`config.yml`・capture モジュール設定・コマンド・権限をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | MobBall |
| api-version | 26.1.2（Paper 1.26.x） |
| メインクラス | `jp.henry.mobball.MobBall` |
| 依存プラグイン | なし（`softdepend: [CasinoPlugin]`） |
| 設定ファイル | `plugins/MobBall/config.yml` ＋ `plugins/MobBall/modules/capture.yml` |

!!! info "統合プラグインです"
    MobBall は、生活鯖で別々に動いていた **VillagerBall** と **Monsterball** を 1 つの jar に統合し、さらに **全モブ対応の単一捕獲ボール** へと一新したものです。内部は `ModuleRegistry` パターンで **capture モジュール** 1 つに集約されており、`config.yml` のフラグで ON / OFF できます（TerrainTools / CasinoPlugin と同じ ModuleRegistry パターン）。捕獲時にエンティティの全 NBT をバイト列で保存するため、村人の取引データを含むあらゆる情報がそのまま保持され、旧 Monsterball の手動 restore は不要になりました。

## 導入手順

1. ビルドした `MobBall` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/MobBall/config.yml` と `plugins/MobBall/modules/capture.yml` が自動生成される。
3. `config.yml` の `modules.capture.enabled` でモジュールを ON / OFF する（既定 `true`）。
4. ボールの素材・成功率・クールダウン・メッセージなどの詳細は `modules/capture.yml` を編集する。
5. 設定を変更したら `/mb reload` で再読み込みする（`config.yml` と `modules/capture.yml` の両方が再読込されます）。

!!! note "config の自動生成について"
    `config.yml` は `saveDefaultConfig()` で初回展開されます。`modules/capture.yml` は capture モジュール起動時に `ConfigUtil` が jar 同梱のデフォルトをコピーし、未指定キーには jar 内デフォルトをフォールバックとして設定します。そのため、既存の `modules/capture.yml` に新キーが無くても、コード側は既定値で動作します。

## モジュール一覧

| モジュール ID | 内容 | 個別設定ファイル |
|---|---|---|
| `capture` | `ENDER_PEARL` ベースの単一ボールで、あらゆるモブ（ボス・プレイヤー除く）を捕獲・解放。全 NBT を保存し解放時に復元 | `modules/capture.yml` |

## config.yml 主要項目

`config.yml` は **モジュール有効フラグと全体設定のみ** を扱います。capture モジュールの個別設定は `modules/capture.yml` に分離されています。

```text title="modules.capture.enabled（既定: true） ― capture モジュールの有効化フラグ"
modules:
  capture:
    enabled: true
```

```text title="debug.verbose（既定: false） ― 詳細ログ出力（現状未使用・将来拡張用）"
debug:
  verbose: false
```

!!! note "モジュールを止めるとどうなるか"
    `modules.capture.enabled: false` にすると `onEnable` が呼ばれず、**リスナーが一切登録されません**。捕獲・解放が無効になり、既存配布済みの捕獲ボールも反応しなくなります。なお `/mb give` は capture モジュールが無効だと「capture モジュールが無効です。」と表示され付与できません。

## capture モジュール設定（`modules/capture.yml`）

### アイテム設定

```text title="item.material（既定: ENDER_PEARL） ― 捕獲ボールの素材（空・中身入り共通）"
item:
  material: ENDER_PEARL
```

```text title="item.glow（既定: true） ― エンチャント風の光沢を付与するか"
item:
  glow: true
```

```text title="item.empty.* ― 空ボールの表示名・カスタムモデルデータ・lore"
item:
  empty:
    name: "&6捕獲ボール &7(空)"
    custom-model-data: 0
    lore:
      - "&7モブに右クリックで捕獲"
```

```text title="item.filled.* ― 中身入りボールの表示名プレフィックス・カスタムモデルデータ・lore"
item:
  filled:
    name-prefix: "&6捕獲ボール: &f"
    custom-model-data: 1
    lore:
      - "&7右クリックで解放"
```

!!! note "アイテム判定の仕組み"
    空・中身入りは **同一素材** で、PDC（`mobball:capture_ball_type` = `empty` / `filled`）で区別します。中身入りボールは捕獲エンティティの全 NBT を `mobball:capture_entity_data`（BYTE_ARRAY）、表示名を `mobball:capture_entity_name`（STRING）として保持します。素材を揃えただけの自作アイテムは捕獲ボールと判定されません。`custom-model-data` は 0 より大きい値のときのみ付与されます。`name-prefix` の後ろに捕獲したモブ名が連結されて表示名になります。

### ゲームプレイ設定

```text title="gameplay.capture-success-rate（既定: 1.0） ― 捕獲成功率（1.0 = 100%）"
gameplay:
  capture-success-rate: 1.0
```

```text title="gameplay.allow-capture-while-trading（既定: false） ― 取引中の村人を捕獲できるか"
gameplay:
  allow-capture-while-trading: false
```

```text title="gameplay.exclude-bosses（既定: true） ― ボス（Boss）を捕獲対象から除外するか"
gameplay:
  exclude-bosses: true
```

```text title="gameplay.cooldown.* ― 捕獲/解放クールダウン（秒）。enabled が false の間は無効"
gameplay:
  cooldown:
    enabled: false
    capture: 3
    release: 1
```

```text title="gameplay.effects.capture.* ― 捕獲時のサウンド・パーティクル"
gameplay:
  effects:
    capture:
      sound: ENTITY_ENDERMAN_TELEPORT
      particle: PORTAL
```

```text title="gameplay.effects.release.* ― 解放時のサウンド・パーティクル"
gameplay:
  effects:
    release:
      sound: ENTITY_ENDERMAN_TELEPORT
      particle: PORTAL
```

!!! tip "サウンド・パーティクル名の表記"
    サウンドは旧形式（`ENTITY_ENDERMAN_TELEPORT`）も新形式（`entity.enderman.teleport`）も受け付けます。内部で大文字／アンダースコアを小文字／ドットに変換し、Paper 1.26.x の Registry 経由で解決します。パーティクルは小文字キー（例: `portal`）として Registry で解決されます。不明な名前を指定するとサーバーログに警告が出ます。

### メッセージ設定

```text title="messages.* ― プレフィックスと各種メッセージ（&カラーコード対応・%name% はモブ名に置換）"
messages:
  prefix: "&6[MobBall] &r"
  capture-success: "&a%name% を捕獲しました！"
  capture-failed: "&c%name% の捕獲に失敗しました。"
  release-success: "&a%name% を解放しました！"
  release-failed: "&c%name% の解放に失敗しました。"
  no-permission: "&cこのアクションを実行する権限がありません。"
  invalid-target: "&cこのエンティティは捕獲できません。"
  boss-target: "&cボスは捕獲できません。"
  cannot-capture-trading: "&c取引中の村人は捕獲できません。"
  inventory-full: "&cインベントリがいっぱいだったため、アイテムをドロップしました。"
  ball-given: "&a捕獲ボールを受け取りました！"
```

## コマンド体系

```text title="/mobball give [プレイヤー] [個数] ― 空の捕獲ボールを付与（mobball.give）"
/mobball give [プレイヤー] [個数]
```

```text title="/mobball modules ― モジュール一覧（ENABLED/DISABLED）を表示（mobball.admin）"
/mobball modules
```

```text title="/mobball reload ― config.yml と modules/capture.yml をリロード（mobball.admin）"
/mobball reload
```

```text title="/mobball version ― バージョンを表示"
/mobball version
```

!!! info "コマンド仕様の補足"
    - エイリアスは `/mb` です。
    - `give` の `[プレイヤー]` 省略時は、実行者自身が対象になります（コンソールからはプレイヤー名の指定が必須）。
    - `[個数]` は **1〜64** の範囲。インベントリが満杯のときは足元にドロップします。
    - タブ補完: 第 1 引数は `give` / `modules` / `reload` / `version`、`give` の第 2 引数はオンラインプレイヤー名、第 3 引数は `1` / `8` / `16` / `32` / `64` を候補表示します。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `mobball.admin` | op | `/mb modules`・`/mb reload`（統合管理） |
| `mobball.give` | op | `/mb give`（捕獲ボールの付与） |
| `mobball.capture` | true | 空のボールでモブを捕獲 |
| `mobball.release` | true | 中身入りボールから解放 |

!!! note "権限判定はサブコマンド単位"
    `plugin.yml` ではコマンド自体に `permission` を付けず、各サブコマンドの実装内で `hasPermission(...)` を判定しています。LuckPerms 等で上記の権限ノードを付与すれば、宣言どおりに制御できます。

## 捕獲・解放の挙動（内部仕様）

!!! info "API バージョン差異の吸収"
    捕獲は `Entity#serializeAsBytes()`、無ければ `UnsafeValues#serializeEntity(Entity)` を使い、解放は `Server#deserializeEntity(...)` 系（3 引数 → 2 引数）→ `UnsafeValues#deserializeEntity(...)` 系の順にリフレクションで探索して呼び分けます。どちらも見つからない環境では起動ログに「利用可能なエンティティ全 NBT 保存 API がありません」と severe を出力し、捕獲・解放は機能しません。解放時は復元したエンティティを `spawnAt(..., SpawnReason.CUSTOM)`（旧 API 互換時は `teleport`）で出現させます。

## トラブルシューティング

??? failure "コマンドが「不明なコマンド」になる / 捕獲・解放が反応しない"
    `config.yml` の `modules.capture.enabled` が `false` になっていないか確認してください。無効モジュールはリスナーが登録されず、既存配布済みのボールも反応しません。`/mb modules` または起動ログで状態を確認できます。

??? failure "起動ログに「module [capture] の有効化に失敗しました」と出る"
    capture モジュールの `onEnable` で例外が発生しています。`ModuleRegistry` は例外を捕捉してログに残します。スタックトレースと `modules/capture.yml` の設定不備を確認してください。

??? failure "起動ログに「エンティティ全 NBT 保存 API がありません」と出る"
    そのサーバー種別／バージョンに、利用可能なエンティティ保存 API（`serializeAsBytes` / `deserializeEntity` 系）が存在しません。Paper 1.26.x 系での運用を確認してください。この状態では捕獲・解放は機能しません。

??? failure "モブを右クリックしてもボールに入らない"
    手に持っているのが **空の捕獲ボール**（PDC `mobball:capture_ball_type=empty`）か確認してください。素材だけ揃えた自作エンダーパールでは判定されません。`/mb give` で配布されたボールを使い、`mobball.capture` 権限・捕獲クールダウン・取引中かどうか（村人）・捕獲成功率・ボス除外設定も確認してください。

??? failure "中身入りボールを右クリックしても解放されない"
    解放は **空中またはブロックへの右クリック** で発動します。モブや他のエンティティへの右クリックでは発動しません。`mobball.release` 権限・解放クールダウンも確認してください。保存データが破損している場合は「解放に失敗しました。」と表示され、サーバーログに復元失敗のエラーが出力されます。

??? failure "OP なのに `/mb give` が使えない"
    `mobball.give`（既定 `op`）の権限が必要です。OP 権限を付与しているか、LuckPerms 等で `mobball.give` を直接付与しているか確認してください。

??? failure "捕獲ボールが投げられてしまう / テレポートする"
    本来は MobBall が捕獲ボールの右クリック投擲を抑止します。投擲が起きる場合は、手に持っているアイテムが MobBall の PDC を持つ正規の捕獲ボールか（自作のただのエンダーパールでないか）を確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← MobBall 概要へ](index.md){ .md-button }
