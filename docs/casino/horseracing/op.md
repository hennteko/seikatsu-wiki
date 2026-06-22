<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 競馬 ― OP・運営ガイド { .page-op #horseracing-op }

競馬（horse モジュール）の有効化・`horse.yml` の設定・コースのセットアップ・看板・権限・管理コマンドをまとめます。競馬は CasinoPlugin に内蔵されたモジュールで、専用 jar の導入は不要です。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `horse` |
| 所属プラグイン | CasinoPlugin（`jp.casinoplugin.CasinoPlugin`） |
| メインコマンド | `/horseracing <subcommand>` |
| モジュール設定ファイル | `plugins/CasinoPlugin/modules/horse.yml` |
| 内部データファイル | `plugins/CasinoPlugin/horse/data.yml`（コース設定・馬数・速度倍率を保存） |
| 共通通貨 | エメラルド（bank モジュールの口座残高） |
| 依存モジュール | bank（エメラルド口座機能。必須） |

!!! info "CasinoPlugin の horse モジュール"
    競馬は単独プラグインではなく、CasinoPlugin に統合された **horse モジュール** です。`config.yml` で有効化すると、`/horseracing` コマンド・看板リスナー・GUI リスナーなどが登録されます。賭け金・配当はすべて bank モジュールのエメラルド口座を通して処理されるため、**bank モジュールが有効である必要があります**。CasinoPlugin 全体の導入・共通設定は [CasinoPlugin OP・運営ガイド](../casino-plugin/op.md) を参照してください。

## 有効化

専用 jar は不要です。競馬は CasinoPlugin 本体に内蔵されています。

1. CasinoPlugin が `plugins/` に導入され、起動済みであることを確認する。
2. `plugins/CasinoPlugin/config.yml` を開く。
3. `modules:` ブロックの `horse` を `enabled: true` にする。

```yaml
modules:
  bank:
    enabled: true   # 競馬の賭け金処理に必須
  horse:
    enabled: true   # 競馬モジュールを有効化
```

4. サーバーを再起動（またはリロード）すると、`plugins/CasinoPlugin/modules/horse.yml` が自動生成される。

!!! warning "bank モジュールが必要"
    競馬の賭け金・配当は bank モジュールのエメラルド口座を通して処理されます。`modules.bank.enabled` が false だと horse モジュールは起動に失敗します（`EmeraldAPI not bound` エラー）。bank は通常 true 固定で運用してください。

## horse.yml 設定項目

`plugins/CasinoPlugin/modules/horse.yml` で、馬券システム・レース速度・馬のステータス生成範囲を調整します。

### 馬券システム（`betting`）

| キー | 既定値 | 説明 |
|---|---|---|
| `betting.takeout_rate` | `0.2` | 控除率（同梱されているが、**現在のコードでは参照されていません**） |
| `betting.minimum_odds` | `1.1` | 最低オッズ（同梱されているが、**現在のコードでは参照されていません**。最低オッズは券種ごとに内部固定） |
| `betting.virtual_bet_amount` | `5000` | 同梱されているが、**実際に読み込まれるのは `race.virtual_bet_amount`** です（下記参照） |

!!! warning "実際に効くオッズ調整キーは `race.virtual_bet_amount`"
    オッズのバラつき（仮想ベットの合計エメラルド額）を制御する有効なキーは **`race.virtual_bet_amount`**（既定 `5000`）です。同梱 `horse.yml` の `betting.virtual_bet_amount` はコードが参照しないため、こちらを編集してもオッズは変わりません。`race.virtual_bet_amount` は初回起動時に horse モジュールが `horse.yml` へ自動追記します（既存ファイルにも未設定なら追記されます）。値を変えたいときは `race:` セクションの `virtual_bet_amount` を編集してください。なお `betting.takeout_rate` と `betting.minimum_odds` は現状のコードでは使われていません（オッズの控除・下限は内部ロジックで固定）。

### レース基本設定（`race`）

| キー | 既定値 | 説明 |
|---|---|---|
| `race.base_speed_multiplier` | `1.0` | 馬の移動速度に掛かる全体の倍率。大きくするとレースが速く進む |
| `race.virtual_bet_amount` | `5000` | オッズにバラつきを持たせるための仮想ベットの合計エメラルド額（**実際に読み込まれるのはこのキー**）。大きいほどオッズの変動が緩やかになる |

### 馬のステータス乱数生成範囲（`horse_stats`）

レースごとに各馬へランダム生成される3ステータスの、最小値・最大値を設定します。

| キー | 既定値 | 説明 |
|---|---|---|
| `horse_stats.strength.min` | `5` | 脚力の最小値 |
| `horse_stats.strength.max` | `10` | 脚力の最大値 |
| `horse_stats.stamina.min` | `4` | スタミナの最小値 |
| `horse_stats.stamina.max` | `9` | スタミナの最大値 |
| `horse_stats.intelligence.min` | `3` | 賢さの最小値 |
| `horse_stats.intelligence.max` | `8` | 賢さの最大値 |

!!! note "オッズと能力スコアの関係"
    馬のオッズは「脚力 ×3 ＋ スタミナ ×2 ＋ 賢さ ×1」で算出した能力スコアをもとに決まります。ステータスの範囲を狭めると馬の実力差が小さくなり、広げると本命・穴馬の差が大きくなります。仮想ベット額（`virtual_bet_amount`）が大きいほどオッズの変動が緩やかになります。

## セットアップ手順

馬のスタート地点・チェックポイント・出走頭数などは `/horseracing` の管理コマンドで設定します。設定内容は `plugins/CasinoPlugin/horse/data.yml` に保存されます。座標を設定するコマンドは **実行したプレイヤーの現在地** が保存されるため、設定したい地点に立って実行してください。

**① 出走頭数を決める**（1〜18頭）

```text title="出走馬の数を設定（例: 6頭）"
/horseracing sethorse 6
```

**② 各馬のスタート地点を設定** — 1番馬のスタートさせたい位置に立って実行。2番馬以降も番号を変えて全頭ぶん実行します。

```text title="1番馬のスタート地点を現在地に設定（2頭目以降は番号を変える）"
/horseracing sethousepoint 1
```

**③ チェックポイントを設定** — コースの通過地点に立って順に登録します。

```text title="チェックポイント1を現在地に設定（2つ目以降は番号を変える）"
/horseracing setlocation 1
```

**④ 速度倍率を調整（任意）**

```text title="コース全体の速度倍率を設定（例: 1.0）"
/horseracing setspeed 1.0
```

**⑤ ロビー・スポーン地点を設定**

```text title="ロビー地点を現在地に設定"
/horseracing setup lobby
```

```text title="退出先（会場外）を現在地に設定"
/horseracing setup spawn
```

**⑥ 看板を設置（任意）** — 移動用の看板を設置します（後述）。

**⑦ 動作確認** — `/horseracing start` でベット期間を開始し、馬が正しくスポーンするか確認します。

!!! tip "レースの進め方"
    `/horseracing start` はトグル式です。停止中に実行すると馬を召喚して **ベット期間** を開始し、ベット期間中にもう一度実行すると **レースを発走** させます。レース中に `start` を実行しても発走済みのため何も起きません。

### 競馬看板

看板を設置し、看板を見ながら（6ブロック以内）以下のコマンドを実行すると、テキストが自動で整形されます。

```text title="ロビー看板"
/horseracing setsign lobby
```

```text title="離脱看板"
/horseracing setsign leave
```

```text title="馬券販売看板"
/horseracing setsign getitem
```

```text title="レース進行看板（クリックで /horseracing start 相当・OP用）"
/horseracing setsign start
```

```text title="看板の設定を解除"
/horseracing setsign remove
```

| 種類 | クリック時の動作 |
|---|---|
| ロビー看板（`lobby`） | クリックしたプレイヤーをロビー地点へテレポート |
| 離脱看板（`leave`） | クリックしたプレイヤーをスポーン地点へテレポート |
| 馬券販売看板（`getitem`） | クリックしたプレイヤーに競馬ベット券（右クリックでベット画面）を配布 |
| レース進行看板（`start`） | クリックでレースを進行（`/horseracing start` と同じ。ベット開始 → 発走の順に進む） |

手書き設置（1行目に `[HorseRacing]`、2行目に種類を入力）もサポートされています（`lobby` / `leave` / `getitem` / `start`）。看板の作成には `horseracing.admin` または `horseracing.sign.create` 権限が必要です。ロビー地点・スポーン地点は事前に `/horseracing setup` で設定しておいてください。`/horseracing setsign remove` で看板の設定を解除できます。

## レース運営コマンド

```text title="レース進行のトグル（1回目: ベット期間開始 → 2回目: 発走）"
/horseracing start
```

```text title="レースを緊急停止（馬を撤去し賭け金を全額返金）"
/horseracing stop
```

```text title="競馬ベット券を入手（プレイヤー名指定で配布）"
/horseracing getbetitem
```

```text title="指定プレイヤーにベット券を配布（枚数: 1〜64）"
/horseracing givebetitem <プレイヤー名> <枚数>
```

## 管理コマンド一覧

| コマンド | 権限 | 説明 |
|---|---|---|
| `/horseracing start` | `horseracing.admin` | レース進行のトグル（停止中→ベット期間開始、ベット期間中→発走） |
| `/horseracing stop` | `horseracing.admin` | レースを緊急停止し、馬を撤去して賭け金を全額返金 |
| `/horseracing sethorse <頭数>` | `horseracing.admin` | 出走馬の数を設定（1〜18頭） |
| `/horseracing sethousepoint <馬番号>` | `horseracing.admin` | 指定馬番号のスタート地点を現在地に設定（プレイヤー専用） |
| `/horseracing setlocation <番号>` | `horseracing.admin` | チェックポイントを現在地に設定（プレイヤー専用） |
| `/horseracing setspeed <倍率>` | `horseracing.admin` | コース全体の速度倍率を設定 |
| `/horseracing setup <spawn\|lobby>` | `horseracing.admin` | スポーン地点／ロビー地点を現在地に設定（プレイヤー専用） |
| `/horseracing getbetitem [対象]` | OP（対象指定時は `horseracing.admin` または `horseracing.giveitem`） | 競馬ベット券を入手。プレイヤー名を指定すると配布 |
| `/horseracing givebetitem <対象> [枚数]` | OP（`horseracing.admin` または `horseracing.giveitem`） | 指定プレイヤーに競馬ベット券を配布（1〜64枚） |
| `/horseracing bet [対象]` | OP（`horseracing.admin`） | ベット画面を開く。プレイヤーは `[HorseRacing] getitem` 看板で取得した馬券アイテムを右クリックして開く |
| `/horseracing setsign <lobby\|leave\|getitem\|start\|remove>` | `horseracing.admin` または `horseracing.sign.create` | 視線先の看板を競馬看板として登録/解除する |

!!! note "レース進行と緊急停止"
    レースの基本フローは「`start`（ベット期間開始）→ `start`（発走）→ 自動でゴール・配当」です。途中でやめたいときは `/horseracing stop` を使うと、馬が撤去され、購入済みの馬券の賭け金が全プレイヤーへ全額返金されます。CasinoPlugin の無効化時にも自動で緊急停止が走ります。

## 権限ノード

CasinoPlugin の `plugin.yml` で宣言されている競馬の権限ノードは次の3つです。

| 権限 | 既定 | 用途 |
|---|---|---|
| `horseracing.admin` | OP | 競馬の管理コマンド全権限（start / stop / sethorse / sethousepoint / setlocation / setspeed / setup / givebetitem / bet など） |
| `horseracing.bet` | OP | `/horseracing bet` の実行権限。プレイヤーは `[HorseRacing] getitem` 看板で取得した馬券アイテムを右クリックしてベット画面を開く |
| `horseracing.sign.create` | OP | `[HorseRacing]` 看板（lobby / leave / getitem / start）の設置権限 |

!!! note "細分化された権限ノードについて"
    内部実装上、管理コマンドには `horseracing.config.horse` / `horseracing.config.point` / `horseracing.config.speed` / `horseracing.config.stop` / `horseracing.setup` / `horseracing.giveitem` といった、機能ごとに分かれた権限ノードも参照されています。これらは plugin.yml には明示宣言されていないため、特定の管理機能だけを一部スタッフに委譲したい場合は、権限プラグイン（LuckPerms 等）で個別に付与してください。`horseracing.admin` を持っていれば、これら個別ノードがなくてもすべての管理機能を使えます。

## トラブルシューティング

??? failure "`/horseracing` コマンドが「不明なコマンド」になる"
    horse モジュールが有効になっているか確認してください。`config.yml` の `modules.horse.enabled` が `true` になっていないと、コマンド・リスナーが一切登録されません。起動ログで horse モジュールが正常に起動しているかも確認してください。

??? failure "horse モジュールが起動に失敗する（EmeraldAPI not bound）"
    競馬は bank モジュールのエメラルド口座機能に依存します。`modules.bank.enabled` を `true` にしてから起動してください。bank が無効だと horse モジュールは `EmeraldAPI not bound` で起動できません。

??? failure "レース準備で「スタート地点が未設定」と出る / 馬がスポーンしない"
    出走頭数ぶんのスタート地点が登録されていない可能性があります。`/horseracing sethorse <頭数>` で設定した頭数すべてについて、`/horseracing sethousepoint <番号>` でスタート地点を登録してください。地点のワールドが読み込まれていない場合もスポーンに失敗します。

??? failure "看板を作成できない"
    `[HorseRacing]` 看板の作成には `horseracing.admin` 権限が必要です。1行目に `[HorseRacing]`、2行目に `lobby` または `leave` を正しく入力しているかも確認してください。

??? failure "ロビー／離脱看板を押しても移動しない"
    ロビー地点・スポーン地点が未設定の可能性があります。それぞれ `/horseracing setup lobby`・`/horseracing setup spawn` を、目的の地点に立った状態で実行してください。

??? failure "オッズの幅が極端 / 配当を調整したい"
    `horse.yml` の `betting.virtual_bet_amount` を大きくするとオッズの変動が緩やかになり、`betting.takeout_rate` で控除率を調整できます。馬の実力差は `horse_stats` の各 min / max で調整します。設定変更後はサーバーの再起動で反映してください。

??? failure "ベットを受け付けない"
    ベットできるのは `/horseracing start` でベット期間を開始してから、発走（2回目の `start`）までの間だけです。レース中・停止中はベット画面を開けません。プレイヤーはコマンドではなく `[HorseRacing] getitem` 看板から馬券アイテムを受け取り、右クリックでベット画面を開く流れです。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 競馬 概要へ](index.md){ .md-button }
