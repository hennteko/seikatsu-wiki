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
| `betting.min-bet` | `1` | 1回のベットの最低額（エメラルド）。管理メニューの「最低ベット額」でも変更できる |

!!! warning "実際に効くオッズ調整キーは `race.virtual_bet_amount`"
    オッズのバラつき（仮想ベットの合計エメラルド額）を制御する有効なキーは **`race.virtual_bet_amount`**（既定 `5000`）です。同梱 `horse.yml` の `betting.virtual_bet_amount` はコードが参照しないため、こちらを編集してもオッズは変わりません。`race.virtual_bet_amount` は初回起動時に horse モジュールが `horse.yml` へ自動追記します（既存ファイルにも未設定なら追記されます）。値を変えたいときは `race:` セクションの `virtual_bet_amount` を編集してください。なお `betting.takeout_rate` と `betting.minimum_odds` は現状のコードでは使われていません。控除率（払戻率）は `odds.payout-rate`、最低オッズは `odds.types.<券種>.min` で設定します（いずれも後述の「オッズ設定」を参照）。

### レース基本設定（`race`）

| キー | 既定値 | 説明 |
|---|---|---|
| `race.min-betting-seconds` | `30` | 一般プレイヤーが開始看板／`/horseracing start` でレースを **発走** させられるまでの、ベット受付の最低秒数（誰かがすぐ発走させてベット期間を打ち切る荒らし対策）。OP・コンソールは即時発走可 |
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

### オッズ設定（`odds`）

7種類の馬券（単勝・複勝・馬連・馬単・ワイド・3連複・3連単）の払戻率・券種別オッズ範囲を設定します。未記載の項目は従来値で動作します。

| キー | 既定値 | 説明 |
|---|---|---|
| `odds.payout-rate` | `0.75` | 馬券7種の払戻率（残り約25%が控除）。`0.1`〜`1.0` にクランプ |
| `odds.legacy-divisor` | `2.0` | 従来オッズ（各馬の表示オッズ）の算出に使う除数。最小 `0.5` |
| `odds.legacy-min` | `0.5` | 従来オッズの下限。最小 `0.1` |
| `odds.virtual-pool-multiplier` | `30` | 仮想ベット倍率。小さいほど実際のベットがオッズに強く効く。最小 `1` |
| `odds.types.<券種>.base` | 券種ごと | 券種の基本係数（最小 `0.01`） |
| `odds.types.<券種>.min` | 券種ごと | 券種の最小オッズ（最小 `1.0`） |
| `odds.types.<券種>.max` | 券種ごと | 券種の最大オッズ（`min` 未満にはならない） |

券種別の既定値（`base` / `min` / `max`）は次のとおりです。

| 券種（キー） | base | min | max |
|---|---|---|---|
| 単勝（`WIN`） | `1.1` | `1.2` | `8.0` |
| 複勝（`PLACE`） | `0.15` | `1.1` | `2.0` |
| 馬連（`QUINELLA`） | `0.4` | `1.5` | `8.0` |
| 馬単（`EXACTA`） | `1.2` | `3.0` | `25.0` |
| ワイド（`WIDE`） | `0.3` | `1.3` | `5.0` |
| 3連複（`TRIO`） | `1.0` | `3.0` | `20.0` |
| 3連単（`TRIFECTA`） | `3.0` | `8.0` | `60.0` |

### 少人数・一人開催の固定オッズ（`fixed-odds`）

ベット受付が締まった時点の「実際に賭けた人数」が少ないレースは、賭け金の取り合い（パリミュチュエル）をやめ、馬の能力値から出した的中確率で払い戻します。一人でも券種ごとの配当が釣り合い、自分の賭け金で倍率が下がりません。予測と実際のずれは `/horseracing oddsstats`（後述）で確認できます。

| キー | 既定値 | 説明 |
|---|---|---|
| `fixed-odds.mode` | `AUTO` | `AUTO` = 下の人数以下で固定オッズ／`ALWAYS` = 常に固定オッズ／`NEVER` = 使わない |
| `fixed-odds.max-players` | `3` | `AUTO` のとき、ベットした人数がこの人数以下なら固定オッズに切り替える |
| `fixed-odds.payout-rate` | `0.85` | 還元率（オッズ ＝ 還元率 ÷ 的中確率） |
| `fixed-odds.sharpness` | `2.0` | 能力差を勝率にどれだけ反映するか（大きいほど本命が強い想定） |
| `fixed-odds.apply-longshot-bonus` | `false` | 固定オッズのレースで人気薄ボーナスを掛けるか（オッズに人気薄が反映済みのため既定 `false`） |

### 会場（観覧範囲 `venue`）

競馬は在籍（ロビー参加）の無い **観覧型** です。スコアボード・荒れレース告知などの進行表示は「今レースのベット者」と「会場にいる人」だけに出ます（レース結果だけは全体告知）。会場 ＝ ロビー地点・各馬のスタート地点・チェックポイントのいずれかから `venue.radius` ブロック以内（同じワールド）です。

| キー | 既定値 | 説明 |
|---|---|---|
| `venue.radius` | `96` | 会場（観覧範囲）の半径（ブロック）。この範囲内のプレイヤーに進行表示が出る |

### 配当ボーナス（`longshot` / `wild` / `streak`）

的中配当に加算される3種類のボーナスを設定します。いずれも `enabled` で個別に無効化できます。

| キー | 既定値 | 説明 |
|---|---|---|
| `longshot.enabled` | `true` | 人気薄ボーナスの有効/無効 |
| `longshot.rank-from` | `2` | 人気下位この順位以降の馬が1着なら対象（最小 `1`） |
| `longshot.bonus-percent` | `50` | その馬が絡む的中配当への加算率（%） |
| `wild.enabled` | `true` | 荒れレースの有効/無効 |
| `wild.chance-percent` | `15` | 発走前に荒れレースが宣言される確率（%、`0`〜`100`） |
| `wild.strength-variance` | `0.25` | 荒れレース時、各馬の速度に掛かる隠し補正（±25%、`0`〜`0.9`） |
| `wild.max-odds-multiplier` | `1.5` | 荒れレース時の最大オッズ拡大倍率（最小 `1.0`） |
| `streak.enabled` | `true` | 連続的中ボーナスの有効/無効 |
| `streak.bonus-percent` | `20` | 前レースに続けて的中した人の配当への加算率（%） |

!!! info "サーバー全体イベント「競馬オッズアップデー」"
    CasinoPlugin 共通の `config.yml` にある `events.horse-odds-up` を有効化すると、開催中は的中配当に `bonus-percent`（既定 `50`%）が加算されます。既定は `enabled: false`。`check-interval-minutes`・`chance-percent`・`duration-minutes`・`cooldown-minutes` で開催頻度を調整します（slot-fever と同じ抽選方式）。これは horse.yml ではなく **共通 `config.yml`** 側の設定です。

## セットアップ手順

馬のスタート地点・チェックポイント・出走頭数などは `/horseracing` の管理コマンドで設定します。設定内容は `plugins/CasinoPlugin/horse/data.yml` に保存されます。座標を設定するコマンドは **実行したプレイヤーの現在地** が保存されるため、設定したい地点に立って実行してください。

**① 出走頭数を決める**（1〜18頭）

```text title="出走馬の数を設定（例: 6頭）"
/horseracing sethorse 6
```

**② 各馬のスタート地点を設定** — 1番馬のスタートさせたい位置に立って実行。2番馬以降も番号を変えて全頭ぶん実行します。

```text title="1番馬のスタート地点を現在地に設定（2頭目以降は番号を変える）"
/horseracing setstart 1
```

**③ チェックポイントを設定** — コースの通過地点に立って順に登録します。

```text title="チェックポイント1を現在地に設定（2つ目以降は番号を変える）"
/horseracing setcheckpoint 1
```

**④ 速度倍率を調整（任意）**

```text title="コース全体の速度倍率を設定（例: 1.0）"
/horseracing setspeed 1.0
```

**⑤ ロビー・スポーン地点を設定**

```text title="ロビー地点を現在地に設定"
/horseracing setlobby
```

```text title="退出先（会場外・初期スポーン）を現在地に設定"
/horseracing setstartspawn
```

**⑥ 看板を設置（任意）** — 移動用の看板を設置します（後述）。

**⑦ 動作確認** — `/horseracing start` でベット期間を開始し、馬が正しくスポーンするか確認します。

!!! tip "レースの進め方"
    `/horseracing start` はトグル式です。停止中に実行すると馬を召喚して **ベット期間** を開始し、ベット期間中にもう一度実行すると **レースを発走** させます。レース中に `start` を実行しても発走済みのため何も起きません。

### 競馬看板

看板を設置し、看板を見ながら以下のコマンドを実行すると、テキストが自動で整形されます。

```text title="参加（ロビー）看板"
/horseracing setsign join
```

```text title="離脱看板"
/horseracing setsign leave
```

```text title="馬券販売看板"
/horseracing setsign shop
```

```text title="レース進行看板（クリックで /horseracing start 相当・全員可、一般は発走まで最低30秒）"
/horseracing setsign start
```

```text title="看板の設定を解除"
/horseracing setsign delete
```

| 種類 | クリック時の動作 |
|---|---|
| 参加（ロビー）看板（`join`） | クリックしたプレイヤーをロビー地点へテレポート |
| 離脱看板（`leave`） | クリックしたプレイヤーをスポーン地点へテレポート |
| 馬券販売看板（`shop`） | クリックしたプレイヤーに競馬ベット券（右クリックでベット画面）を配布 |
| レース進行看板（`start`） | クリックでレースを進行（`/horseracing start` と同じ。ベット開始 → 発走の順に進む） |

看板は必ず上記の `/horseracing setsign <種類>` で登録してください。登録座標と種類は `horse.yml` の `signs.registry` に保存され、クリック判定は看板の文字ではなく登録座標で行われます（**1行目 `[HorseRacing]` ＋ 2行目に種類を書くだけの手書き設置は廃止されました**。既存の手書き看板をクリックすると OP には登録を促す案内が表示されます）。看板の作成には `horseracing.admin` 権限（OP）が必要です。ロビー地点・スポーン地点は事前に `/horseracing setlobby`・`/horseracing setstartspawn` で設定しておいてください。`/horseracing setsign delete` で看板の登録を解除できます。

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

```text title="固定オッズの予測と実際のずれを表示（reset で実績リセット）"
/horseracing oddsstats [reset]
```

```text title="管理ツールを受け取る"
/horseracing wand
```

```text title="管理メニューを開く"
/horseracing menu
```

```text title="設定を再読み込み"
/horseracing reload
```

```text title="預かり中の持ち物を手動復元（force で強制）"
/horseracing lobbyfix <プレイヤー名> [force]
```

## プレイヤーも実行できるコマンド

次の4つは権限不要で **一般プレイヤーも実行可能** です（看板の代わりに使えます）。

| コマンド | 説明 |
|---|---|
| `/horseracing join` | ロビー地点へテレポート（`lobby` 看板と同等） |
| `/horseracing leave` | スポーン地点へテレポート（`leave` 看板と同等） |
| `/horseracing start` | レースを進行（`start` 看板と同等）。一般プレイヤーは開始から `race.min-betting-seconds`（既定30秒）経過後まで発走させられない（OP は即時） |
| `/horseracing status` | レースの設定状況（地点・看板）を表示（閲覧専用） |

## 管理コマンド一覧

| コマンド | 権限 | 説明 |
|---|---|---|
| `/horseracing start` | 全員可（一般は発走まで最低30秒） | レース進行のトグル（停止中→ベット期間開始、ベット期間中→発走）。コマンドブロック／コンソールからも実行可 |
| `/horseracing stop` | `horseracing.admin` | レースを緊急停止し、馬を撤去して賭け金を全額返金 |
| `/horseracing sethorse <頭数>` | `horseracing.admin` | 出走馬の数を設定（1〜18頭） |
| `/horseracing setstart <馬番号>` | `horseracing.admin` | 指定馬番号のスタート地点を現在地に設定（プレイヤー専用） |
| `/horseracing setcheckpoint <番号>` | `horseracing.admin` | チェックポイントを現在地に設定（プレイヤー専用） |
| `/horseracing setspeed <倍率>` | `horseracing.admin` | コース全体の速度倍率を設定 |
| `/horseracing setlobby` | `horseracing.admin` | ロビー地点を現在地に設定（プレイヤー専用） |
| `/horseracing setstartspawn` | `horseracing.admin` | 退出先（会場外・初期スポーン）を現在地に設定（プレイヤー専用） |
| `/horseracing getbetitem [対象]` | `horseracing.admin` | 競馬ベット券を入手。プレイヤー名を指定すると配布 |
| `/horseracing givebetitem <対象> [枚数]` | `horseracing.admin` | 指定プレイヤーに競馬ベット券を配布（1〜64枚） |
| `/horseracing bet [対象]` | `horseracing.admin` | ベット画面を開く。プレイヤーは `[HorseRacing] shop` 看板で取得した馬券アイテムを右クリックして開く |
| `/horseracing oddsstats [reset]` | `horseracing.admin` | 固定オッズの予測と実際のずれを表示（`reset` で実績リセット） |
| `/horseracing setsign <join\|leave\|shop\|start\|delete>` | `horseracing.admin` | 視線先の看板を競馬看板として登録／解除する（`shop` は馬券販売看板） |
| `/horseracing wand` | `horseracing.admin` | 管理ツールを受け取る |
| `/horseracing menu` | `horseracing.admin` | 管理メニューを開く |
| `/horseracing reload` | `horseracing.admin` | `horse.yml` を再読み込み |
| `/horseracing lobbyfix <対象> [force]` | `horseracing.admin` | 預かり中の持ち物を手動復元（`force` で強制） |

!!! note "レース進行と緊急停止"
    レースの基本フローは「`start`（ベット期間開始）→ `start`（発走）→ 自動でゴール・配当」です。途中でやめたいときは `/horseracing stop` を使うと、馬が撤去され、購入済みの馬券の賭け金が全プレイヤーへ全額返金されます。CasinoPlugin の無効化時にも自動で緊急停止が走ります。

## 権限ノード

CasinoPlugin の `plugin.yml` で宣言されている競馬の権限ノードは1つだけです。

| 権限 | 既定 | 用途 |
|---|---|---|
| `horseracing.admin` | OP | 競馬の管理コマンド全権限（start / stop / sethorse / setstart / setcheckpoint / setspeed / setlobby / setstartspawn / setsign / getbetitem / givebetitem / bet / oddsstats / wand / menu / reload / lobbyfix など） |

!!! note "権限ノードは `horseracing.admin` の1本に統合されました"
    以前は `horseracing.bet` / `horseracing.sign.create` / `horseracing.config.*` / `horseracing.setup` / `horseracing.giveitem` など機能ごとに分かれた権限ノードがありましたが、これらは廃止され、管理コマンドの判定はすべて **OP または `horseracing.admin`** の1本になりました。一般プレイヤーが使えるのは `join` / `leave` / `start` / `status` で、これらは権限不要です。

## トラブルシューティング

??? failure "`/horseracing` コマンドが「不明なコマンド」になる"
    horse モジュールが有効になっているか確認してください。`config.yml` の `modules.horse.enabled` が `true` になっていないと、コマンド・リスナーが一切登録されません。起動ログで horse モジュールが正常に起動しているかも確認してください。

??? failure "horse モジュールが起動に失敗する（EmeraldAPI not bound）"
    競馬は bank モジュールのエメラルド口座機能に依存します。`modules.bank.enabled` を `true` にしてから起動してください。bank が無効だと horse モジュールは `EmeraldAPI not bound` で起動できません。

??? failure "レース準備で「スタート地点が未設定」と出る / 馬がスポーンしない"
    出走頭数ぶんのスタート地点が登録されていない可能性があります。`/horseracing sethorse <頭数>` で設定した頭数すべてについて、`/horseracing setstart <番号>` でスタート地点を登録してください。地点のワールドが読み込まれていない場合もスポーンに失敗します。

??? failure "看板を作成できない"
    `[HorseRacing]` 看板の登録には `horseracing.admin` 権限（OP）が必要です。看板を見ながら `/horseracing setsign <join\|leave\|shop\|start>` を実行して登録してください（1行目 `[HorseRacing]` ＋ 2行目に種類を書くだけの手書き設置は廃止されています）。

??? failure "ロビー／離脱看板を押しても移動しない"
    ロビー地点・スポーン地点が未設定の可能性があります。それぞれ `/horseracing setlobby`・`/horseracing setstartspawn` を、目的の地点に立った状態で実行してください。

??? failure "オッズの幅が極端 / 配当を調整したい"
    `horse.yml` の **`race.virtual_bet_amount`** を大きくするとオッズの変動が緩やかになります（実際に読み込まれるのは `betting.virtual_bet_amount` ではなく `race.virtual_bet_amount` です）。馬の実力差は `horse_stats` の各 min / max で調整します。設定変更後はサーバーの再起動で反映してください。なお `betting.takeout_rate`・`betting.minimum_odds` は現状のコードでは参照されないため、編集しても挙動は変わりません。

??? failure "ベットを受け付けない"
    ベットできるのは `/horseracing start` でベット期間を開始してから、発走（2回目の `start`）までの間だけです。レース中・停止中はベット画面を開けません。プレイヤーはコマンドではなく馬券販売（`shop`）看板から馬券アイテムを受け取り、右クリックでベット画面を開く流れです。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 競馬 概要へ](index.md){ .md-button }
