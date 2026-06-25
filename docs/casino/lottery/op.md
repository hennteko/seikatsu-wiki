<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 宝くじ ― OP・運営ガイド { .page-op #lottery-op }

宝くじ（lottery モジュール）の有効化・`lottery.yml` の景品テーブル設定・管理コマンド・権限をまとめます。宝くじは CasinoPlugin の1モジュールであり、専用jarの導入は不要です。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `lottery` |
| 所属プラグイン | CasinoPlugin（`jp.casinoplugin.CasinoPlugin`） |
| メインコマンド | `/kuzi`（エイリアス `/lottery`） |
| 設定ファイル | `plugins/CasinoPlugin/modules/lottery.yml` |
| 有効化フラグ | `plugins/CasinoPlugin/config.yml` の `modules.lottery.enabled` |
| 共通通貨 | エメラルド（bank モジュールの口座残高） |
| 抽選券の価格 | 1枚 10エメラルド（実装で固定） |

!!! info "CasinoPlugin のモジュールです"
    宝くじは単独プラグインではなく、CasinoPlugin に統合された **lottery モジュール** です。導入手順・共通設定（`config.yml`）・モジュール構成の全体像は [CasinoPlugin の OP・運営ガイド](../casino-plugin/op.md) を参照してください。このページは lottery モジュール固有の設定のみを扱います。

## 有効化

宝くじは CasinoPlugin に内蔵されているため、**専用jarの導入は不要** です。CasinoPlugin 本体が導入されていれば、有効化フラグを切り替えるだけで使えます。

1. `plugins/CasinoPlugin/config.yml` を開く。
2. `modules:` ブロックの `lottery` を `enabled: true` にする。
3. サーバーを起動（または再起動）すると `plugins/CasinoPlugin/modules/lottery.yml` が自動生成される。
4. 必要に応じて `lottery.yml` の景品テーブルを編集する。

```yaml
modules:
  lottery:
    enabled: true
```

!!! warning "bank モジュールが必須"
    lottery モジュールは起動時に EmeraldAPI（bank モジュール）が準備済みかを確認します。`bank` モジュールが無効だと lottery モジュールは起動に失敗します。`modules.bank.enabled` は true 固定で運用してください。

!!! note "無効化したとき"
    `modules.lottery.enabled: false` にすると、`/kuzi` コマンドと抽選券リスナーが一切登録されません。プレイヤーには「不明なコマンド」と表示されます。

## lottery.yml 設定項目

`lottery.yml` は **景品テーブル（`prizes`）のみ** を定義します。抽選券の価格（1枚10エメラルド）はソース側で固定されており、設定ファイルでは変更できません。

`prizes` の下に5つのカテゴリー（`ore` / `food` / `function` / `build` / `other`）があり、各カテゴリーの下に `マテリアル名: 個数` の形式で景品を列挙します。

```yaml
prizes:
  ore:
    DIAMOND: 64
    GOLD_INGOT: 64
    IRON_INGOT: 64
    COAL: 64
    COBBLESTONE: 64
    LAPIS_LAZULI: 64
    ANCIENT_DEBRIS: 1
```

| キー | 説明 |
|---|---|
| `prizes.<カテゴリー>` | 抽選カテゴリー。`ore` / `food` / `function` / `build` / `other` の5種類が読み込まれる |
| `prizes.<カテゴリー>.<マテリアル名>` | 景品アイテム。値（整数）が **抽選の重み** であると同時に **当選時に付与される個数** を兼ねる |

### 個数 ＝ 重み ＝ 付与数

このモジュールの最大の特徴は、各景品に指定した **数値が「重み」と「付与個数」の両方を兼ねる** 点です。

- カテゴリー内の全景品の数値を合計した値が「総重み」になります。
- 抽選では総重みの範囲で乱数を引き、当たった景品の数値ぶんのアイテムをそのまま付与します。
- つまり `DIAMOND: 64` は「重み64（当たりやすい）かつ当選時にダイヤを64個渡す」、`ANCIENT_DEBRIS: 1` は「重み1（当たりにくい）かつ当選時に古代の残骸を1個渡す」を意味します。

例: `ore` カテゴリーが上記の標準設定（64×6 + 1 = 385）なら、古代の残骸が当たる確率は `1 / 385` です。

!!! warning "レア景品は『少ない個数』で表現する"
    付与数と確率が連動するため、レアな景品ほど数値を小さくする必要があります。「エリトラ1枚をレア当たりにしたい」場合は `ELYTRA: 1`（重み1・付与1個）とします。重みを下げる目的で大きい数値にすると、当たりやすさと付与数の両方が増えてしまいます。

### 標準設定の景品テーブル

初回生成される `lottery.yml` の内容です。

| カテゴリー | 標準の景品（マテリアル: 個数兼重み） |
|---|---|
| `ore` | DIAMOND:64 / GOLD_INGOT:64 / IRON_INGOT:64 / COAL:64 / COBBLESTONE:64 / LAPIS_LAZULI:64 / ANCIENT_DEBRIS:1 |
| `food` | COOKED_BEEF:64 / COOKED_PORKCHOP:64 / ENCHANTED_GOLDEN_APPLE:8 / GOLDEN_APPLE:16 / GOLDEN_CARROT:64 / BREAD:64 / PUMPKIN:64 |
| `function` | GLOWSTONE:64 / TINTED_GLASS:64 / BOOKSHELF:64 / SPONGE:64 / SHROOMLIGHT:64 / ICE:64 / SLIME_BLOCK:64 |
| `build` | MOSSY_COBBLESTONE:64 / DIORITE:64 / ANDESITE:64 / TUFF:64 / QUARTZ_BLOCK:64 / PURPUR_BLOCK:64 / END_STONE:64 |
| `other` | FIREWORK_ROCKET:64 / ELYTRA:1 / EXPERIENCE_BOTTLE:64 / WITHER_SKELETON_SKULL:1 / SHULKER_SHELL:2 / TOTEM_OF_UNDYING:1 / TROPICAL_FISH_BUCKET:2 |

!!! note "カテゴリー名は固定"
    読み込まれるカテゴリーは `ore` / `food` / `function` / `build` / `other` の5種類で固定です。これ以外の名前でセクションを追加しても抽選プールには読み込まれません。各カテゴリーの中身（マテリアルと個数）は自由に編集できます。

!!! danger "マテリアル名は正確に"
    マテリアル名は Bukkit の `Material` 列挙名（大文字）です。存在しない名前を書くと、そのアイテムだけ読み込みがスキップされ、起動ログに「Invalid material name」と severe ログが出ます。あるカテゴリーのセクションごと存在しない場合は warning ログが出て、そのカテゴリーは抽選に出てきません。

## 購入看板の設置

一般プレイヤーは `[宝くじ]` 看板の右クリックで **購入GUI** を開き、そこから抽選券を購入します。看板を設置し、看板を見ながら（6ブロック以内）以下のコマンドを実行すると自動で整形されます。

```text title="購入看板を登録（引数なし）"
/kuzi setsign
```

看板の内容は次のとおりです（プラグインが自動で書き込みます）。

| 行 | 内容 |
|---|---|
| 1行目 | `[宝くじ]` |
| 2行目 | `クリックで` |
| 3行目 | `購入GUIを開く` |
| 4行目 | （空白） |

!!! note "看板は購入GUIを開く専用です"
    現在の仕様では、`[宝くじ]` 看板はクリックすると **購入GUI** が開く共通のものです（種類・枚数を看板ごとに指定する旧仕様は廃止されました）。購入GUIでは各くじ種類を 1 / 5 / 10 / 32 / 64 枚 単位で購入できます。`/kuzi gui` でも同じGUIを開けます。

## 管理コマンド

```text title="lottery.yml の景品テーブルを再読み込み"
/kuzi reload
```

```text title="購入GUIを開く（OP専用）"
/kuzi gui
```

```text title="抽選券を直接購入（OP専用。例: ore を5枚）"
/kuzi ore 5
```

```text title="購入GUIを開く（全プレイヤー可）"
/kuzi join
```

| コマンド | 説明 |
|---|---|
| `/kuzi reload` | `lottery.yml` の景品テーブルを再読み込みする |
| `/kuzi setsign` | 視線先の看板を `[宝くじ]` 購入看板（購入GUIを開く）として登録する |
| `/kuzi gui` | 購入GUIを開く（OP専用） |
| `/kuzi <種類> <枚数>` | 抽選券を直接購入する（OP専用） |
| `/kuzi join` | 購入GUIを開く（**全プレイヤー実行可**。看板の代わりに使える） |

!!! note "/kuzi join は全プレイヤーが使える"
    `/kuzi join` は OP 判定の前に処理されるため、**権限を問わず全プレイヤー** が実行でき、`[宝くじ]` 看板と同じ購入GUIを開きます。`/kuzi gui` は OP 専用ですが、`/kuzi join` は一般プレイヤーにも開放されている点が異なります。

`/kuzi reload` はコンソール、OP、または `kuzi.reload` 権限を持つプレイヤーが実行できます。`lottery.yml` を編集したらこのコマンドで反映してください（サーバー再起動でも反映されます）。

!!! tip "TAB 補完"
    `/kuzi` の第1引数で TAB を押すと、**全プレイヤー** に `join`（と読み込み済みの各くじ種類）が表示されます。OP または `kuzi.reload` 権限保持者にはさらに `reload`、`kuzi.admin` 保持者には `setsign` と `gui` も表示されます。

## 権限ノード

`/kuzi` コマンドは **OP専用** です（reload を除く）。一般プレイヤーは `[宝くじ]` 看板から購入し、抽選券の右クリック使用は全員が利用できます。

| 権限 | 既定 | 用途 |
|---|---|---|
| `kuzi.reload` | OP | `/kuzi reload` の実行可否 |
| `kuzi.admin` | OP | `/kuzi` コマンド（購入・`setsign`・`gui`）の実行可否 |

!!! note "kuzi.reload について"
    `kuzi.reload` は plugin.yml で `default: op` として宣言されています。OP は権限の有無に関わらず `/kuzi reload` を実行できます。一般プレイヤーにも reload を許可したい場合は、LuckPerms 等の権限プラグインで `kuzi.reload` を付与してください。

## トラブルシューティング

??? failure "`/kuzi` が「不明なコマンド」になる"
    `config.yml` の `modules.lottery.enabled` が `false` になっている可能性があります。`true` に変更してサーバーを再起動してください。起動ログに「module [lottery] は config で無効化されています」が出ていないか確認します。

??? failure "起動ログに「module [lottery] の有効化に失敗しました」と出る"
    lottery モジュールは起動時に EmeraldAPI（bank モジュール）の準備を確認します。`bank` モジュールが無効、または bank の起動に失敗していると lottery も起動できません。`modules.bank.enabled: true` を確認してください。

??? failure "特定の景品が出ない / カテゴリーが空"
    `lottery.yml` のマテリアル名に誤りがあると、その景品だけ読み込まれません。起動ログに「Invalid material name in config」が出ていないか確認してください。カテゴリーごとセクションが無い場合は「Config section 'prizes.<名前>' not found」の warning が出ます。

??? failure "レア景品なのに大量に当たる / 確率が高すぎる"
    景品の数値は **付与個数と抽選重みを兼ねる** 仕様です。数値を大きくすると当たりやすさも付与数も増えます。レア景品は数値を小さく（例: `ELYTRA: 1`）設定してください。

??? failure "lottery.yml を編集したのに反映されない"
    `/kuzi reload` を実行するか、サーバーを再起動してください。reload は景品テーブル（`prizes`）を再読み込みします。

??? failure "旧 Takarakuzi 時代の抽選券が使えない"
    抽選券の識別タグ（NamespacedKey）は CasinoPlugin が所有プラグインになります。旧 Takarakuzi プラグインで発行された抽選券は再認識されず、右クリックしても抽選されません（仕様）。新たに `/kuzi` で購入し直してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 宝くじ 概要へ](index.md){ .md-button }
