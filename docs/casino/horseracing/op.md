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
| `betting.takeout_rate` | `0.2` | 控除率（0.2 = 20%）。賭け金プールから運営側が差し引く割合 |
| `betting.minimum_odds` | `1.1` | 馬券に適用される最低オッズ |
| `betting.virtual_bet_amount` | `5000` | オッズにバラつきを持たせるための仮想ベットの合計エメラルド額。少人数でもオッズが極端にならないようにするためのプール |

### レース基本設定（`race`）

| キー | 既定値 | 説明 |
|---|---|---|
| `race.base_speed_multiplier` | `1.0` | 馬の移動速度に掛かる全体の倍率。大きくするとレースが速く進む |

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

1. **出走頭数を決める** — `/horseracing sethorse <頭数>` で出走馬の数を設定します（1〜18頭）。
2. **各馬のスタート地点を設定** — 1番馬のスタートさせたい位置に立ち `/horseracing sethousepoint 1` を実行。2番馬以降も同様に番号を変えて全頭ぶん実行します。
3. **チェックポイントを設定** — コースの通過地点に立ち `/horseracing setlocation 1`、次の地点で `2` … と順に登録します。
4. **速度倍率を調整（任意）** — `/horseracing setspeed <倍率>` でコース全体の速度倍率を設定します（例: `1.0`）。
5. **ロビー・スポーン地点を設定** — 競馬会場のロビーに立ち `/horseracing setup lobby`、会場外（退出先）に立ち `/horseracing setup spawn` を実行します。
6. **看板を設置（任意）** — 移動用の看板を設置します（後述）。
7. **動作確認** — `/horseracing start` でベット期間を開始し、馬が正しくスポーンするか確認します。

!!! tip "レースの進め方"
    `/horseracing start` はトグル式です。停止中に実行すると馬を召喚して **ベット期間** を開始し、ベット期間中にもう一度実行すると **レースを発走** させます。レース中に `start` を実行しても発走済みのため何も起きません。

### 競馬看板

看板の1行目に `[HorseRacing]`、2行目に種類を入力すると、競馬用の看板になります。

| 2行目 | 種類 | クリック時の動作 |
|---|---|---|
| `lobby` | ロビー看板 | クリックしたプレイヤーをロビー地点へテレポート |
| `leave` | 離脱看板 | クリックしたプレイヤーをスポーン地点へテレポート |

看板の作成には `horseracing.admin` 権限が必要です。ロビー地点・スポーン地点は事前に `/horseracing setup` で設定しておいてください。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/horseracing start` | `horseracing.admin` | レース進行のトグル（停止中→ベット期間開始、ベット期間中→発走） |
| `/horseracing stop` | `horseracing.admin` | レースを緊急停止し、馬を撤去して賭け金を全額返金 |
| `/horseracing sethorse <頭数>` | `horseracing.admin` | 出走馬の数を設定（1〜18頭） |
| `/horseracing sethousepoint <馬番号>` | `horseracing.admin` | 指定馬番号のスタート地点を現在地に設定（プレイヤー専用） |
| `/horseracing setlocation <番号>` | `horseracing.admin` | チェックポイントを現在地に設定（プレイヤー専用） |
| `/horseracing setspeed <倍率>` | `horseracing.admin` | コース全体の速度倍率を設定 |
| `/horseracing setup <spawn\|lobby>` | `horseracing.admin` | スポーン地点／ロビー地点を現在地に設定（プレイヤー専用） |
| `/horseracing getbetitem [対象]` | 全員（対象指定は `horseracing.admin`） | 競馬ベット券を入手。プレイヤー名を指定すると配布（管理者権限が必要） |
| `/horseracing givebetitem <対象> [枚数]` | `horseracing.admin` | 指定プレイヤーに競馬ベット券を配布（1〜64枚） |
| `/horseracing bet [対象]` | `horseracing.bet` | ベット画面を開く。プレイヤー名を指定可能（コマンドブロック対応） |

!!! note "レース進行と緊急停止"
    レースの基本フローは「`start`（ベット期間開始）→ `start`（発走）→ 自動でゴール・配当」です。途中でやめたいときは `/horseracing stop` を使うと、馬が撤去され、購入済みの馬券の賭け金が全プレイヤーへ全額返金されます。CasinoPlugin の無効化時にも自動で緊急停止が走ります。

## 権限ノード

CasinoPlugin の `plugin.yml` で宣言されている競馬の権限ノードは次の2つです。

| 権限 | 既定 | 用途 |
|---|---|---|
| `horseracing.admin` | OP | 競馬の管理コマンド全権限（start / stop / sethorse / sethousepoint / setlocation / setspeed / setup / givebetitem / 看板作成 など） |
| `horseracing.bet` | 全員 | プレイヤーが競馬にベットできる |

!!! note "細分化された権限ノードについて"
    内部実装上、管理コマンドには `horseracing.config.horse`・`horseracing.config.point`・`horseracing.config.speed`・`horseracing.config.stop`・`horseracing.setup`・`horseracing.giveitem`・`horseracing.sign.create` といった、機能ごとに分かれた権限ノードも用意されています。これらは plugin.yml には明示宣言されていないため、特定の管理機能だけを一部スタッフに委譲したい場合は、権限プラグイン（LuckPerms 等）で個別に付与してください。`horseracing.admin` を持っていれば、これら個別ノードがなくてもすべての管理機能を使えます。

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
    ベットできるのは `/horseracing start` でベット期間を開始してから、発走（2回目の `start`）までの間だけです。レース中・停止中はベット画面を開けません。プレイヤーに `horseracing.bet` 権限があるかも確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 競馬 概要へ](index.md){ .md-button }
