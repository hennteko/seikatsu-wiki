<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# TravelTools ― OP・運営ガイド { .page-op #traveltools-op }

TravelTools の導入・共通設定・モジュール構成・統合管理コマンド・権限・既存サーバーからの移行手順をまとめます。各モジュールの細かい仕様（追跡コンパスのロア仕様、`render-mode` の各値、要塞検索半径など）は、必要に応じて旧プラグインの個別ページも参照してください。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | TravelTools |
| バージョン | 1.0.0 |
| 対象バージョン | Paper 1.26.x（api-version 26.1.2） |
| メインクラス | `jp.henry.traveltools.TravelTools` |
| 作者 | へんりー |
| softdepend（任意連携） | `Multiverse-Portals` / `CasinoPlugin` / `LuckPerms` |
| 設定ファイル | `plugins/TravelTools/config.yml` ＋ `plugins/TravelTools/modules/<id>.yml` |
| 生成 jar | `target/TravelTools-1.0.0.jar` |

!!! info "統合プラグインです"
    TravelTools は、生活鯖の旧 **Spawn / Playercompass / PortalVisualizer / Netherfortress** の4プラグインを1つの jar に統合し、さらに **一括破壊（鉱脈式）= veinmine モジュール** を追加したものです。各機能は `TravelToolsModule` を実装する **モジュール** として `ModuleRegistry` 経由で起動・停止され、`config.yml` の `modules.<id>.enabled` で個別に ON / OFF できます（現在は spawn / compass / portalvis / nether / veinmine の **5モジュール**）。あるモジュールの起動が失敗しても他のモジュールは動き続ける設計です（TerrainTools / CasinoPlugin / MobBall / EnchantPack と同じパターン）。

## 導入手順

1. ビルドした `TravelTools-1.0.0.jar` を `plugins/` フォルダに配置する。
2. `portalvis` モジュールを使う場合は **Multiverse-Portals** を先に導入しておく（softdepend。未導入でも本体は起動しますが、`portalvis` モジュールだけが自動で無効化されます）。
3. サーバーを起動すると `plugins/TravelTools/config.yml` と `plugins/TravelTools/modules/*.yml` が自動生成される。
4. `config.yml` の `modules:` ブロックで、使いたいモジュールだけを `enabled: true` にする（既定は5つとも true）。
5. 旧 PortalVisualizer の `config.yml` を引き継ぐ場合は、後述の「移行手順」を参照。
6. 起動ログに `[TravelTools] v1.0.0 を有効化しました。` が出れば成功。

### ビルド（参考）

```text
cd "C:\Users\hennt\IdeaProjects\【1.26.2】\【1.26.2】生活鯖WIKI\【1.26.2】TravelTools\TravelTools"
mvn clean package
```

`libs/multiverse-core-5_3_3.jar` と `libs/multiverse-portals-5_1_1.jar` は既に同梱されているため、追加配置は不要です。

## 含まれるモジュール一覧

| モジュール ID | 旧プラグイン | 内容 | 個別設定ファイル |
|---|---|---|---|
| `spawn` | Spawn | `/spawn` で現在ワールドのスポーン地点へテレポート | `modules/spawn.yml`（プレースホルダのみ） |
| `compass` | Playercompass | `/compass <player>` でプレイヤー追跡コンパスを発行 | `modules/compass.yml`（プレースホルダのみ） |
| `portalvis` | PortalVisualizer | Multiverse-Portals のポータル領域をパーティクル可視化（**Multiverse-Portals 必須**） | `modules/portalvis.yml`（旧 PortalVisualizer の config を完全互換） |
| `nether` | Netherfortress | 初回ネザー入場時に最寄り要塞の座標をチャット通知（コマンド無し） | `modules/nether.yml`（プレースホルダのみ） |
| `veinmine` | （新機能） | スニーク中に原木/鉱石/砂などを正しいツールで掘ると連結した同種ブロックを一括破壊（コマンド無し） | `modules/veinmine.yml`（実設定あり） |

!!! note "spawn / compass / nether は config なしでそのまま動く"
    `spawn` / `compass` / `nether` の3モジュールは旧プラグインが config を持たなかったため、`modules/*.yml` も将来拡張用のプレースホルダになっています（編集不要）。`portalvis` は旧 `PortalVisualizer/config.yml` 由来の項目（`render-mode` / `view-distance` / `particle-*` / `default-enabled` / ポータル個別カラーなど）を引き継いでいます。新規追加の `veinmine` は `modules/veinmine.yml` に実際の設定項目（最大破壊数・対象グループ・ツール判定・耐久消費など）を持ちます。

## config.yml 主要項目

`config.yml` は **全体設定とモジュール有効フラグのみ** を扱います。各モジュールの個別設定は `modules/<id>.yml` に分離されています。

| キー | 既定値 | 説明 |
|---|---|---|
| `modules.spawn.enabled` | `true` | `/spawn` モジュールの有効化 |
| `modules.compass.enabled` | `true` | `/compass` 系モジュールの有効化 |
| `modules.portalvis.enabled` | `true` | `/pv` モジュールの有効化（Multiverse-Portals が必要） |
| `modules.nether.enabled` | `true` | 初回ネザー入場通知モジュールの有効化 |
| `modules.veinmine.enabled` | `true` | 一括破壊（鉱脈式）モジュールの有効化 |
| `debug.verbose` | `false` | 詳細ログ（現状未使用、将来拡張用） |

!!! tip "Multiverse-Portals が未導入の環境"
    `modules.portalvis.enabled: true` のままでも、Multiverse-Portals が未導入の場合は `PortalVisModule.onEnable()` 冒頭でその不在を検知し、警告ログを出して **そのモジュールだけ自動で無効化** します。`spawn` / `compass` / `nether` / `veinmine` は通常稼働します。

!!! warning "既存サーバーの config.yml への新キー自動追記について"
    本体は `saveDefaultConfig()` のみを使用しており、**既存の `config.yml` に新しいキーを自動マージしません**。そのため veinmine モジュール追加前から `config.yml` を持っているサーバーでは、`modules.veinmine.enabled` 行は **自動では追記されません**。ただしコード側 (`ModuleRegistry`) は当該キーが無い場合の既定を `true` として扱うため、**キーが無くても veinmine は有効化されます**。明示的に無効化したい場合は、`config.yml` の `modules:` ブロックに `veinmine:` ＋ `enabled: false` を手動で追記してください。なお `modules/veinmine.yml` 自体は新規ファイルのため、初回起動時に jar 同梱デフォルトが自動生成されます（既存環境でも追加生成される）。

### `portalvis.yml`（旧 PortalVisualizer 互換）

`plugins/TravelTools/modules/portalvis.yml` は、旧 `plugins/PortalVisualizer/config.yml` と **完全互換** です。主要キーは以下のとおり。

| キー | 既定値 | 説明 |
|---|---|---|
| `render-mode` | `outline` | 描画モード（`outline` / `corners` / `fill`） |
| `particle-interval` | `15` | パーティクル描画間隔（tick） |
| `view-distance` | `30` | ポータルが見える距離（ブロック） |
| `particle-color.{red,green,blue}` | 128 / 0 / 255 | パーティクル色（RGB 0–255） |
| `particle-density` | `0.5` | パーティクル密度（小さいほど密） |
| `particle-size` | `1.2` | パーティクルサイズ（0.5–3.0） |
| `default-enabled` | `true` | ログイン時に既定で可視化 ON にするか |
| `portals.<name>.{color,size}` | ― | ポータル個別の色・サイズ上書き |

### `veinmine.yml`（一括破壊 / 鉱脈式）

`plugins/TravelTools/modules/veinmine.yml` は新規追加モジュールの設定ファイルです。初回起動時に jar 同梱のデフォルトが自動展開されます。

| キー | 既定値 | 説明 |
|---|---|---|
| `require-sneak` | `true` | スニーク中のみ発動する。`false` にすると常時発動 |
| `max-blocks` | `64` | 1回の連鎖で破壊する最大ブロック数（起点を含む）。最小1 |
| `consume-durability` | `true` | 連鎖破壊した分だけツール耐久を消費する（耐久無限は確率で考慮） |
| `respect-correct-tool` | `true` | 対応ツール（鉱石=ツルハシ / 原木=斧 / 砂=シャベル）を持つ時だけ発動 |
| `search-diagonals` | `true` | 斜めも連結対象に含める（`true`=26近傍 / `false`=6近傍） |
| `fire-break-events` | `false` | 連鎖破壊ごとに `BlockBreakEvent` を発火し保護プラグインに委ねる（負荷増） |
| `groups.logs` | `true` | 全種類の原木（`Tag.LOGS`）を対象にする |
| `groups.ores` | `true` | 各種鉱石（`*_ORE`）＋古代の残骸（`ANCIENT_DEBRIS`）を対象にする |
| `groups.sand` | `true` | 砂・赤い砂を対象にする |
| `extra-blocks` | `[]` | グループ外で個別追加するブロック（Material 名。任意ツールで発動） |
| `exclude-worlds` | `[]` | 一括破壊を無効化するワールド名 |

!!! warning "保護プラグイン併用時は `fire-break-events: true`"
    既定の `fire-break-events: false` では連鎖破壊分に `BlockBreakEvent` を発火しないため、WorldGuard 等の保護判定が連鎖部分には効きません。保護プラグインで土地保護をしているサーバーでは `fire-break-events: true` を推奨します（その分わずかに負荷が増えます）。保護プラグインを使っていなければ `false` のままで構いません。

## 統合管理コマンド `/traveltools`（エイリアス `/travel`）

統合プラグイン用の管理コマンドです。各モジュールのコマンドは旧プラグインのまま維持されています。

| コマンド | 必要権限 | 説明 |
|---|---|---|
| `/travel` | （誰でも） | ヘルプを表示 |
| `/travel modules` | `traveltools.admin` | 登録されているモジュールの ENABLED / DISABLED 一覧を表示 |
| `/travel reload` | `traveltools.admin` | メイン `config.yml` をリロード（各モジュール個別 config は `/pv reload` 等を使用） |
| `/travel version` | （誰でも） | バージョン表示 |

!!! warning "`/tt` エイリアスは使えません"
    `/tt` は **TerrainTools** が既に占有しているため、TravelTools 側のエイリアスとしては採用していません。短縮コマンドとしては **`/travel`** を使用してください。

### 各モジュール由来のコマンド一覧

`/spawn` のみ全員使用可（権限 `traveltools.spawn.use`、既定 true）です。`/compass` 系と `/portalvisualizer` は `plugin.yml` 上の `permission` が **`traveltools.admin`（既定 op）** に設定されており、プレイヤーは `[Compass]` / `[PortalVis]` 看板から利用します。

| コマンド | エイリアス | 由来モジュール | 必要権限 | 説明 |
|---|---|---|---|---|
| `/spawn` | ― | spawn | `traveltools.spawn.use`（既定 true / 全員可） | 現在ワールドのスポーン地点へテレポート（プレイヤー専用） |
| `/compass <player>` | `/pc` / `/playercompass` | compass | `traveltools.admin` | 指定プレイヤーを追跡する「追跡コンパス」を発行 |
| `/compasslist` | `/pclist` | compass | `traveltools.admin` | オンラインプレイヤー一覧（クリックで `/compass` 実行） |
| `/compassreset` | `/pcreset` | compass | `traveltools.admin` | 手に持っている追跡コンパスをリセット |
| `/portalvisualizer <toggle\|on\|off\|reload\|info>` | `/pv` | portalvis | `traveltools.admin`（コマンド本体）<br>`toggle` / `on` / `off` は内部で `portalvisualizer.use` を、`reload` / `info` は `portalvisualizer.admin` を確認 | ポータル可視化の設定 |

### プレイヤー向け看板リスナー

| 看板1行目 | 対応モジュール | 動作 |
|---|---|---|
| `[Spawn]` | spawn | 右クリックで現在ワールドのスポーン地点へテレポート |
| `[Compass]` | compass | 右クリックでオンラインプレイヤー頭一覧 GUI を開き、選択で追跡コンパスを配布 |
| `[PortalVis]` | portalvis | 右クリックで自分のポータル可視化を ON/OFF（`portalvisualizer.use` を確認） |

看板の **設置自体には権限不要**。1行目を該当タグで書くとリスナーが整形して色付き見出しになります。

## 権限ノード

| 区分 | ノード | 既定 | 内容 |
|---|---|---|---|
| **新規** | `traveltools.admin` | op | `/travel modules` / `/travel reload`、および `/compass*` / `/portalvisualizer` コマンドの実行（plugin.yml でこれらのコマンド permission が `traveltools.admin` に統一） |
| **新規** | `traveltools.spawn.use` | true（全員） | `/spawn` の使用権限。個別に禁止したい場合は LuckPerms 等で `false` にする |
| **新規** | `traveltools.veinmine` | true（全員） | 一括破壊（鉱脈式）の使用権限。個別に無効化したい場合は LuckPerms 等で `false` にする |
| **旧 PortalVisualizer 維持** | `portalvisualizer.use` | true（全員） | `/pv toggle\|on\|off` および `[PortalVis]` 看板から個人の可視化 ON/OFF 切替（コマンド／看板リスナー内で確認） |
| **旧 PortalVisualizer 維持** | `portalvisualizer.admin` | op | `/pv reload` / `/pv info` の内部追加チェック |

!!! tip "LuckPerms 設定は無修正でOK"
    旧 `portalvisualizer.use` / `portalvisualizer.admin` の権限ノード名はそのまま維持しています。既存サーバーの LuckPerms 設定は **書き換えずにそのまま** 使えます。`/spawn`（`traveltools.spawn.use`）と一括破壊（`traveltools.veinmine`）はいずれも既定 true で全員使用可のため、特定プレイヤーだけ禁止したい場合のみ LuckPerms で該当ノードを `false` にしてください。Spawn / Playercompass / Netherfortress は元から権限ノードを持たないため、追加設定は不要です。

## 既存サーバーからの移行

!!! warning "既存サーバーからの移行手順"
    1. **旧4 jar を削除する**
        - `plugins/Spawn.jar`
        - `plugins/Playercompass.jar`
        - `plugins/PortalVisualizer-1.0.0.jar`
        - `plugins/Netherfortress-1.0-SNAPSHOT.jar`
    2. **TravelTools jar を配置する**
        - `target/TravelTools-1.0.0.jar` を `plugins/` に置く
    3. **PortalVisualizer の既存設定を引き継ぐ（任意）**
        - 旧 `plugins/PortalVisualizer/config.yml` を `plugins/TravelTools/modules/portalvis.yml` に **リネームコピー** する（フォーマット完全互換）
        - 既定設定で良い場合はこの作業は不要
    4. **🟢 既存ネザー入場済みプレイヤーは再通知されない**
        - `nether` モジュールは PDC owner を旧プラグイン名の `netherfortress` のまま維持しています（コード側で deprecated コンストラクタを明示利用）
        - そのため、生活鯖で既に初回入場済みのプレイヤーには **TravelTools 導入後も再通知が走りません**
        - 移行時に最も重要な互換性ポイント
    5. **Multiverse-Portals 無し環境では portalvis のみ無効化**
        - `depend` ではなく `softdepend` 扱いのため、Multiverse-Portals が無くても本体は起動
        - `portalvis` モジュールのみ自動で無効化され、`spawn` / `compass` / `nether` は通常稼働
    6. **サーバー再起動して動作確認**
        - 旧コマンド（`/spawn` / `/compass` / `/pv`）が変わらず動くこと
        - 起動ログに `[nether] PDC key 'netherfortress:has_entered_nether' で初回ネザー入場を検知します` が出ること
        - 既存プレイヤーがネザーへ移動しても **再通知が出ないこと**

### ロールバック

旧4プラグインの jar はビルド成果物として残っているため、トラブル時は TravelTools jar を外し、旧4 jar を戻すだけで元に戻せます。旧 PortalVisualizer の `config.yml` も削除しなければそのまま再利用可能です。

## トラブルシューティング

??? failure "起動ログに `[portalvis] Multiverse-Portals が見つかりません` と出る"
    Multiverse-Portals が未導入・未起動の場合、`portalvis` モジュールだけが自動で無効化されます。仕様通りの挙動です。`/pv` を使いたければ Multiverse-Portals を先に導入してください。`spawn` / `compass` / `nether` は通常どおり動きます。

??? failure "`/spawn` `/compass` `/pv` のいずれかが「不明なコマンド」になる"
    対応するモジュールが `config.yml` の `modules.<id>.enabled: false` で無効化されている可能性があります。無効モジュールはコマンド・リスナーが一切登録されません。`/travel modules` で各モジュールの ENABLED / DISABLED を確認してください。

??? failure "既存プレイヤーにネザー要塞の通知が再度飛んでしまった"
    通常は **発生しません**。発生した場合、`NetherModule.java` の `LEGACY_OWNER` 定数が変更されていないか、`new NamespacedKey(this, …)` のような新 owner 付与に書き換わっていないか確認してください。正しいログは `[nether] PDC key 'netherfortress:has_entered_nether' で初回ネザー入場を検知します` です。owner が `netherfortress` になっていないと旧フラグを認識できません。

??? failure "起動ログに `module [<id>] の有効化に失敗しました` と出る"
    そのモジュールの `onEnable` で例外が発生しています。`ModuleRegistry` が例外をキャッチしてログに残すため、他モジュールは動き続けますが、該当モジュールは停止状態です。スタックトレースと `modules/<id>.yml` の設定不備を確認してください。

??? failure "`/travel` で何も起きない / `/travel reload` で「権限がありません」と表示される"
    `/travel modules` / `/travel reload` は `traveltools.admin`（既定 op）が必要です。OP 権限を付けるか、LuckPerms 等で `traveltools.admin` を付与してください。`/travel` 単体・`/travel version` は誰でも実行できます。

??? failure "`/tt` を打ったら TerrainTools が反応する"
    仕様です。`/tt` は **TerrainTools** が占有しているため、TravelTools 側は **`/travel`** を使ってください。

??? failure "`portalvis.yml` の編集後、設定が反映されない"
    `/pv reload`（権限 `portalvisualizer.admin`）でリロードしてください。`/travel reload` はメイン `config.yml` のみで、`portalvis.yml` には影響しません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← TravelTools 概要へ](index.md){ .md-button }
