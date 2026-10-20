<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# BoatGP ― OP・運営ガイド { .page-op #boatgp-op }

BoatGP の導入・config・items.yml・サーキット作成・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | BoatGP |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/race`（エイリアス `/boatgp`）/ join・leave・start・status・list は全員可、設定系は `boatgp.admin` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/BoatGP/config.yml`、`items.yml`、`messages.yml`、`surfaces.yml`、`circuits/<名前>.yml` |

!!! success "実装状況：Phase 2（アイテムバトル）"
    現在は **Phase 2（アイテムバトル）** までが実装済みです。カート物理・路面・チェックポイント判定・順位HUD・コース外復帰・簡易報酬に加え、**アイテムシステム（6種）・アイテムボックス・ラバーバンド抽選・ボスバー表示・ロケットスタート** が動作します。ベストタイム記録・リーダーボードなどは未実装です。詳細は下記「実装状況」の節を確認してください。

## 導入手順

1. ビルドした `boatgp-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/BoatGP/` に `config.yml` / `items.yml` / `messages.yml` / `surfaces.yml` が自動生成され、`circuits/` フォルダが作られる。
3. レース専用ワールド（フラットまたはボイド推奨）を用意する。
4. 後述の手順でサーキットを作成する（アイテムボックスの配置を含む）。
5. 設定を変更したら `/race reload` で再読み込みする。

!!! note "既存サーバーを更新する場合（4ファイルとも自動追記されます）"
    `config.yml` / `messages.yml` / `items.yml` / `surfaces.yml` は、起動時にサーバー上のファイルを jar 内の最新版と突き合わせ、**不足しているキーを既定値＋コメント付きで自動追記** します（既存の値は変更しません／追記があったときだけ更新前を `<ファイル名>.bak-日時` に退避）。バージョンアップで新しいメッセージキーや設定キーが追加された場合も、起動するだけで反映されるため、手書き追記は基本的に不要です。地点・サーキット・看板などのデータ（`circuits/<名前>.yml`・`signs.yml`）は各設定コマンドの実行時に即保存されます。

!!! info "操舵の既定は「視点操舵」になりました"
    `config.yml` の `kart` セクションでは **サーバー操舵**（`input-steering: true`、既定）が有効で、さらに **`look-steering: true`（既定）** により **プレイヤーが向いている方向へボートが旋回して進む「視点操舵」** が標準の操作になりました。`look-steering: false` にすると、従来の **左右移動キー（A/D）での旋回** に切り替わります。`input-steering: false` にすると、プラグイン制御をやめてバニラのネイティブ操舵（プレイヤーがボートを直接運転）に戻ります。コーナー減速の判定方式は `corner-detect`（既定 `turn`＝実際の旋回量で判定）で切り替えます。これらの新キーは起動時に `config.yml` へ自動追記されるため、手書き追記は不要です（内部既定値でも同じ挙動で動作します）。

!!! tip "サーバー実装について"
    エンティティ（ボート）を多用するミニゲームのため、設計上は **Paper の採用が推奨** されています。Spigot でも動作しますが、イベント処理・最適化の面で Paper が有利です。

## config.yml 主要項目

### レース設定（`race`）

| キー | 既定値 | 説明 |
|---|---|---|
| `race.min-players` | 1 | レース開始に必要な最少人数（同梱configは実験用に1。通常運用は2以上推奨） |
| `race.max-players` | 12 | 1レースの最大人数（実際の上限はスタートグリッド数で決まる） |
| `race.lobby-countdown` | 30 | 最少人数到達後、ロビーで開始までカウントする秒数 |
| `race.start-countdown` | 3 | スタートグリッド整列後の 3-2-1 カウント秒数 |
| `race.default-laps` | 3 | サーキット側で周回数未指定の場合の既定周回数 |
| `race.finish-timeout` | 60 | 1位ゴール後、残り走者を待つ制限時間（秒）。超過で強制終了 |

### カート物理（`kart`）

| キー | 既定値 | 説明 |
|---|---|---|
| `kart.acceleration` | 0.10 | 1tickあたり現在速度を目標速度へ近づける割合 |
| `kart.deceleration` | 0.18 | 1tickあたりの減速割合 |
| `kart.default-grip` | 0.85 | 既定グリップ（路面側で上書きされる） |
| `kart.stuck-speed-threshold` | 0.04 | この移動量（blocks/tick）未満が続くとスタック判定 |
| `kart.stuck-ticks` | 60 | スタックと判定するまでの継続tick数 |
| `kart.off-track-reset` | true | コース外・危険路面・落下・スタック時にチェックポイントへ復帰させるか |
| `kart.void-y` | 0 | このY座標を下回ったら落下とみなし復帰 |
| `kart.corner-speed` | 0.72 | 旋回中の速度倍率。小さいほどコーナーで減速して曲がりが詰まる（推奨 0.6〜0.85） |
| `kart.input-steering` | true | **サーバー操舵**を有効にするか。`true` でボートの向き・移動をプラグインが制御し、Java版・統合版（Geyser）で操作が統一される。`false` で従来のネイティブ操舵（プレイヤーがボートを直接運転）に戻る |
| `kart.look-steering` | true | `input-steering: true` のときの操舵方式。`true`＝**視点操舵**（向いている方向へボートが旋回して進む・既定）／`false`＝左右移動キー（A/D）で旋回 |
| `kart.turn-rate` | 4.5 | サーバー操舵時の旋回速度（度/tick）。大きいほどキビキビ曲がる（推奨 3.0〜6.0）。`input-steering: false` のときは無効 |
| `kart.corner-detect` | turn | コーナー減速の判定方式。`turn`＝実際の旋回量で判定（既定・Java/統合版で同条件）／`look`＝視点とボートの向きの差で判定（旧方式） |
| `kart.corner-turn-threshold` | 2.0 | `corner-detect: turn` のとき、旋回量（度/tick）がこの値以上の間は `corner-speed` で減速（`turn-rate` より小さくする・推奨 1.5〜3.0） |
| `kart.look-corner-angle` | 20.0 | `corner-detect: look` のとき、視点とボートの向きの差がこの角度（度）を超えている間は `corner-speed` で減速 |
| `kart.debug-log` | false | `true` で各レーサーの速度を1秒ごとにコンソールへ出力（速度比較・デバッグ用） |

### ワールド・HUD・報酬

| キー | 既定値 | 説明 |
|---|---|---|
| `world.default` | `"race_world"` | レース専用ワールド名（サーキットyml側の指定が優先） |
| `hud.update-interval` | 10 | HUD（スコアボード）の更新間隔（tick／20=1秒） |
| `reward.enabled` | true | 順位連動の簡易報酬を有効にするか |
| `reward.places.p1` 〜 `p3` | `DIAMOND:3` ほか | 1〜3位の報酬アイテム（`Material名:個数`） |
| `reward.finish` | `BREAD:2` | 4位以下の完走者への参加賞 |

!!! note "報酬の指定形式"
    報酬は `Material名:個数` の形式で指定します（例: `DIAMOND:3`）。経済プラグイン連携は行わず、報酬はすべてプラグイン内で完結します。`reward.places.p4` 以降を追加すれば、その順位専用の報酬も指定できます。

## items.yml 主要項目（アイテムバトル）

アイテムシステムの設定は `plugins/BoatGP/items.yml` にまとめられています。`/race reload` で再読み込みされます。

### アイテムボックス・ロケットスタート

| キー | 既定値 | 説明 |
|---|---|---|
| `item-box.cooldown-ticks` | 80 | アイテムボックス通過後、再出現までのtick数（20=1秒） |
| `item-box.default-radius` | 3.0 | `additembox` で半径を省略したときの既定判定半径 |
| `rocket-start.window-ticks` | 11 | GO の何tick前から「良いタイミング」とみなすか |
| `rocket-start.boost-speed` | 2.1 | ロケットスタート成功時のブースト速度 |
| `rocket-start.boost-ticks` | 45 | ロケットスタート成功時のブースト継続tick数 |

### アイテムの出現重み（ラバーバンド）

`items.<アイテム>` の `weight-front` / `weight-mid` / `weight-back` で、**順位帯（上位／中位／下位）ごとの抽選重み** を設定します。値が大きいほど出やすく、`0` で出現しません。下位ほど強力なアイテムが出るよう重み付けされています。

| アイテム | weight-front（上位） | weight-mid（中位） | weight-back（下位） |
|---|---|---|---|
| `dash_mushroom`（ダッシュキノコ） | 30 | 28 | 16 |
| `green_shell`（みどりこうら） | 26 | 20 | 10 |
| `red_shell`（あかこうら） | 4 | 18 | 24 |
| `banana`（バナナ） | 34 | 19 | 8 |
| `star`（スター） | 2 | 10 | 22 |
| `lightning`（サンダー） | 0 | 5 | 20 |

各アイテムの `display` で、ボスバー等に表示される名前（`&` カラーコード対応）を設定できます。

### アイテム効果パラメータ（`effects`）

| キー | 既定値 | 説明 |
|---|---|---|
| `effects.dash-boost-speed` / `dash-boost-ticks` | 1.95 / 50 | ダッシュキノコの加速量・継続tick |
| `effects.green-speed` / `green-lifetime` | 1.7 / 110 | みどりこうら（直進弾）の速度・寿命tick |
| `effects.red-speed` / `red-lifetime` / `red-homing` | 1.45 / 150 / 0.30 | あかこうら（ホーミング弾）の速度・寿命・追尾の強さ |
| `effects.banana-lifetime` | 700 | バナナ（後方トラップ）が残るtick数 |
| `effects.star-ticks` / `star-boost-speed` | 150 / 1.8 | スターの無敵＋加速の継続tick・加速量 |
| `effects.lightning-slow-ticks` | 70 | サンダーで全員が減速するtick数 |
| `effects.spinout-ticks` | 32 | 命中時のスピンアウト継続tick |
| `effects.hit-radius` | 2.2 | 弾・トラップ・体当たりの命中半径 |

!!! note "操舵・アイテムの操作仕様"
    ステアリングは **サーバー操舵** で実装されています。既定の **視点操舵**（`kart.look-steering: true`）では、プレイヤーの視線方向（yaw）へ1tickあたり最大 `turn-rate` 度まで車体の向きを寄せ、サーバー側でボートを `setRotation` で旋回させます。`kart.look-steering: false` にすると `Player#getCurrentInput()` の左右入力（A/D）で旋回する方式になります。いずれも Java版・統合版（Geyser）で同一の挙動です。`kart.input-steering: false` で従来のネイティブ操舵（プレイヤーがボートを直接運転し、ボートの向きで進む）に戻せます。
    アイテムの発動は **降車操作（`VehicleExitEvent`）の再利用** で実装されています。レース中の降車ボタン押下＝所持アイテムの発動、カウントダウン中の降車ボタン押下＝ロケットスタート判定になります。統合版は端末によって降車ボタンが届きにくいため、空中タップ（腕振り）やホットバー切り替えでも同じ入力を受け付けます（`bedrock.swing-to-use` / `hotbar-to-use`）。`/race use` コマンドでも代替できます。

### 路面定義（`surfaces.yml`）

路面は「見た目のブロック」ではなく「最高速＋グリップ」のパラメータで定義します。`surfaces.yml` でブロックの割り当てを自由に編集できます。

| 路面区分 | `speed` | `grip` | 既定の対象ブロック |
|---|---|---|---|
| `standard`（標準） | 0.85 | 0.90 | 石・石レンガ・コンクリート・草ブロック など |
| `ice`（氷） | 1.25 | 0.55 | 青氷・氷塊・氷 |
| `boost`（ブースト） | 1.90 | 0.95 | 薄水色／黄色コンクリート |
| `offroad`（オフロード） | 0.35 | 0.80 | 砂・赤い砂・ソウルサンド・砂利・土の道 など |
| `danger`（危険） | 0.0 | 0.50 | マグマ・マグマブロック・水（`reset: true` で復帰トリガー） |

未定義ブロック上の挙動は `default-surface`（既定 `standard`）で決まります。

## サーキット作成手順

!!! success "コマンド体系が刷新されました"
    従来の `/race admin <サブ>` 方式に加え、**`/race <サブ>` のトップレベル形式** で実行できるようになりました（`/race admin <サブ>` も引き続き使えます）。また **変更は自動保存** されるため、`save` の実行は不要です。チェックポイント／ゴールラインは **半径の円ではなく「A→Bの2点で引くライン（ゲート）」** に変更され、コースを横切る直線として通過判定するようになりました。さらに **看板での参加・離脱・開始**（`setsign` / `setstart`）と **初期スポーン**（`setstartspawn`）に対応しています。

サーキットは `/race` コマンドで作成し、`plugins/BoatGP/circuits/<名前>.yml` に保存されます。yml の手書きは不要です。OP権限を持った状態でレース専用ワールドに入り、**設置したい地点に立って**（またはラインの端点に立って）コマンドを実行します。

標準フロー（create → setlobby → setstartspawn → setspawn → setfinish a/b → addcheckpoint a/b → additembox → setsign/setstart → laps）:

```text title="① 新規サーキットを作成して選択（現在のワールドが対象）"
/race create <名前>
```

```text title="② 待機ロビーを現在地に設定"
/race setlobby
```

```text title="③ 初期スポーン（離脱時の戻り先）を現在地に設定"
/race setstartspawn
```

```text title="④ スタートグリッドを現在地に追加（参加可能人数ぶん繰り返す）"
/race setspawn
```

```text title="⑤-1 ゴールライン(=CP0)の始点Aを現在地に記録"
/race setfinish a
```

```text title="⑤-2 ゴールラインの終点Bを現在地に記録（A→Bを結ぶラインになる）"
/race setfinish b
```

```text title="⑥-1 チェックポイントの始点Aを記録（コース順に繰り返す）"
/race addcheckpoint a
```

```text title="⑥-2 チェックポイントの終点Bを記録（A→Bのラインで追加）"
/race addcheckpoint b
```

```text title="アイテムボックスを現在地に追加（任意・複数可・半径省略可）"
/race additembox [半径]
```

```text title="周回数を設定"
/race laps <周回数>
```

```text title="既存サーキットを選択して編集する"
/race select <名前>
```

```text title="設定状況を確認（不足項目も表示）"
/race status
```

!!! tip "ラインの引き方（setfinish / addcheckpoint）"
    ゴールラインとチェックポイントは **a（始点）→ b（終点）の2回** でコースを横切る直線として引きます。コースの片端に立って `... a`、反対の端に立って `... b` を実行してください。高さ（Y許容）は `... b [高さ]` で指定でき、省略時は ±4.0 ブロックです。通過判定は「前tick→現tick の移動線がラインを横切ったか」で行うため、**コース幅いっぱいにラインを張る** とショートカット防止になります。`addcheckpoint` は先に `setfinish` でゴールラインを引いてから実行してください。

!!! note "保存（自動）と有効化の条件"
    各設定コマンドは実行のたびに **即時自動保存** されます（`save` は不要、`cancel` で編集破棄）。レース開始に使える状態になる条件は **ロビー地点・スタートグリッド1個以上・チェックポイント2個以上（ゴールライン＋通常CP）** です。`setstartspawn`・アイテムボックス・看板は必須ではありません（初期スポーン未設定時は離脱時の戻り先がロビーになります）。`/race status` で不足項目を確認できます。

!!! note "アイテムボックスの配置"
    `additembox` は **任意** です。置かないサーキットも走行できます（その場合アイテムは出現しません）。半径を省略すると `items.yml` の `item-box.default-radius`（既定3.0）が使われます。

!!! note "チェックポイントとスタートグリッド"
    チェックポイントの index0 は必ずゴールライン（`setfinish`）です。`addcheckpoint` でコースの進行順にラインを追加します。スタートグリッド数（`setspawn` の数）が **そのサーキットの実質の最大参加人数** になります（`config.yml` の `race.max-players` と、グリッド数の小さい方が上限）。設計上はコース幅7ブロック前後・壁の高さ3ブロック以上・1周400ブロック前後が目安です。

## チェックポイントの可視化（`/race show`）

チェックポイント／ゴールラインは **パーティクルの「ゲート（縦のカーテン）」** として表示されるようになりました。色はゴール/スタート=金、次に通過すべきCP=緑、その他=水色です。表示は **見ている本人だけ** に出ます（他プレイヤーには見えません）。

- **レース中の参加者には自動表示** されます（次に通過すべきCPが緑で強調）。
- OPは設定確認用に、同じワールドの全サーキットのゲートを **プレビュー表示** できます。

```text title="チェックポイント表示のプレビューをON/OFF（自分のみ・トグル）"
/race show
```

!!! note "表示範囲"
    ゲートは約60ブロック以内のものだけが描画されます。ライン（A→B）がコースを正しく横切っているか、`/race show` をONにして歩きながら確認すると設置ミスに気づきやすくなります。

## チェックポイント・スタートグリッドの削除

設置をやり直したいとき用に、削除コマンドが追加されました（いずれも自動保存）。

```text title="指定番号のチェックポイントを削除（1以上。0=ゴールは対象外）"
/race delcheckpoint <番号>
```
```text title="ゴール/スタートライン(CP0)を削除（/race setfinish で再設定）"
/race delfinish
```
```text title="全チェックポイント（ゴール含む）を削除"
/race clearcheckpoints
```
```text title="指定番号のスタートグリッドを削除（1以上）"
/race delspawn <番号>
```
```text title="全スタートグリッドを削除"
/race clearspawn
```

## 看板の設置（参加・離脱・開始）

看板は **プレイヤーがコマンドを覚えずに参加・離脱・開始できる補助手段** です（`/race join` / `leave` / `start` は全員が実行できますが、ロビーに看板を置いておくと便利です）。看板の設置・解除は **`/race setsign` コマンド専用** です（看板への直接書き込みによる登録は廃止されました）。いずれも `boatgp.admin` 権限が必要です。

設置した看板を見ながら（6ブロック以内）以下を実行すると、プラグインが登録し、整形済みテキストを書き込みます。

```text title="参加看板を設定（クリックで参加。コース名が書き込まれる）"
/race setsign join
```

```text title="離脱看板を設定（クリックで離脱）"
/race setsign leave
```

```text title="開始看板を設定（クリックでレース開始・編集中のサーキット対象）"
/race setstart
```

```text title="開始看板を設定（setsign 形式・サーキット名必須）"
/race setsign start <サーキット名>
```

```text title="見ている看板の登録を解除（テキストも消去）"
/race setsign delete
```

開始看板は `/race setstart` と `/race setsign start` のどちらでも作成できます。`setstart` は引数を省略すると編集中のサーキットが対象になり、サーキット名で明示も可能です（`/race setstart <サーキット名>`）。**`setsign start` はサーキット名の指定が必須** です。

`setsign join` / `setstart` は引数でサーキットを明示することもできます（省略時は編集中のサーキット、`setsign leave` はサーキット指定不要）。

```text title="サーキットを指定して参加看板を設定"
/race setsign join <サーキット名>
```

```text title="サーキットを指定して開始看板を設定"
/race setstart <サーキット名>
```

!!! note "看板の仕組み（座標で判定・テキストは表示専用）"
    登録した看板は **座標** を `plugins/BoatGP/signs.yml` に保存し、クリック判定は **保存した座標との一致** で行います（看板のテキストには依存しません）。看板には1行目に `[BoatGP]` タグ、2行目に種別（JOIN / LEAVE / START）、3行目にコース名、4行目に **状態＋参加人数**（約5秒ごとに自動更新）が表示されます。看板を壊すと登録は自動解除されます。`setsign delete` は見ている看板の登録を解除し、テキストも消去します。開始看板（START）クリックは `/race start` 相当で、参加者がいれば誰でも開始できます。クリック看板には連打防止のため1秒のクールダウンがあります。

## ソロモード（CPUレース・タイムトライアル）の設定

1人でも遊べる **ソロモード** です。**CPUレース**（AIカートとの競走）と **タイムトライアル**（タイム計測＋ゴースト）の2種類があり、入口は **ソロ看板** か **`/race solo`** です。**サーキットは通常レースと共用** で、どちらかが使用中のあいだは、もう一方を開始できません。在籍・持ち物の預かりは通常レースと同じロビー層を使い、ソロ用に **専用のロビー地点** を1つ用意します。

!!! note "ソロの記録は専用ファイルに保存されます"
    ソロの記録は `plugins/BoatGP/solo-records.yml`、ゴーストは `plugins/BoatGP/ghosts/` に自動保存されます。これは **通常レースの共通成績・リーダーボード（Phase 3予定）とは別系統の、ソロ専用の記録** です。`/race ranking <cpu|tt> ...` でランキングを表示できます。

### セットアップ手順（ソロ）

OP権限（`boatgp.admin`）で実行します。走るサーキット自体の設定は通常レースと共通です（`create`〜`laps`）。ソロ用に追加するのは **ソロロビー**（必須）と、任意で **ソロ看板** です。

```text title="ソロロビーを現在地に設定（ソロ参加時のTP先・必須）"
/race setsololobby
```
```text title="ソロ看板を設定（クリックでソロメニューを開く・看板を見て実行）"
/race setsign solo
```
```text title="ソロの記録とゴーストを消す（サーキット省略で全サーキット）"
/race soloreset <プレイヤー> [サーキット]
```

!!! tip "CPUレースに必要な条件"
    CPUレースは **スタートグリッドが2つ以上** あるサーキットでのみ遊べます（CPUと挑戦者を並べるため）。タイムトライアルはグリッド1つでも可能です。難易度（台数・速さ等）・クリア順位・ゴースト・制限時間（既定600秒）などは `config.yml` の `solo.*` で調整します（未設定でも既定値で動作・起動時に自動追記）。ソロロビーが未設定の場合は、暫定でサーキットのロビーへ送られます（OPには警告が出ます）。

### config.yml（`solo.*` 主要項目）

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | true | ソロモードの有効/無効 |
| `solo.time-limit` | 600 | 1回の制限時間（秒・0で無制限） |
| `solo.menu-delay` | 3 | 挑戦後にメニューを開き直すまでの秒数 |
| `solo.ranking-size` | 10 | `/race ranking` の表示人数 |
| `solo.broadcast-best` | true | 自己ベスト更新を全体へ告知 |
| `solo.cpu.clear-position` | 3 | CPUレースでクリアとなる順位（以内） |
| `solo.cpu.default-level` | `normal` | 難易度省略時の難易度 |
| `solo.cpu.levels.*` | easy/normal/hard | 難易度ごとのCPU台数・速さ・運転精度など（最大3種） |
| `solo.cpu.riders` | CPUレッド他 | CPUの名前と防具色 |
| `solo.time-trial.start-items` | 3 | タイムトライアル開始時のダッシュキノコ数 |
| `solo.time-trial.ghost.enabled` / `default` | true / `last` | ゴーストの有効・既定種別（none/last/best） |

!!! note "ソロロビーのデータ保存先"
    ソロロビー地点（`/race setsololobby`）は `plugins/BoatGP/solo-data.yml` に即保存されます。`config.yml` の `solo.*` は数値・挙動の調整用で、起動時に不足キーが自動追記されるため手書き追記は不要です。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `boatgp.admin` | OP | サーキットの作成・管理（create/setlobby/setsign 等）、`/race stop` / `reload`、看板設置・削除、`/race setsololobby` / `soloreset`。設定系コマンドのみコード側でこの権限を確認する |

!!! warning "`/race` のコマンドレベル権限は無し（設定系のみ admin）"
    `plugin.yml` の `race` コマンドには **コマンドレベルの `permission` が設定されていません**。そのため **`/race join` / `leave` / `start` / `status` / `list` は全員が実行できます**。サーキット設定（create / setlobby / setspawn / setfinish / addcheckpoint / setsign / setstart / show / laps 等）と `/race reload` は **コード内で `boatgp.admin` を確認** し、`/race stop` は **OP または `boatgp.admin`** で実行できます。プレイヤーは看板クリック・コマンドのどちらでも参加・離脱・開始できます。`boatgp.play` 権限は廃止されました。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/race join [サーキット名]` | 全員 | レースに参加してロビーへ（看板クリックと同等） |
| `/race leave` | 全員 | レースから離脱する（看板クリックと同等） |
| `/race start [サーキット名]` | 全員 | レースを開始する（参加者がいれば誰でも可。コンソール/コマブロはサーキット名必須） |
| `/race status [サーキット名]` | 全員 | サーキットの設定状況を確認する（`info` も可） |
| `/race list` | 全員 | サーキット一覧を表示する |
| `/race use` | 全員 | アイテム使用／ロケットスタート（降車ボタンの代替。統合版向け） |
| `/race solo [cpu\|tt [サーキット] [難易度\|none\|last\|best]]` | 全員 | ソロモード（メニューを開く／CPUレース・タイムトライアルを開始） |
| `/race ranking <cpu\|tt> [サーキット] [難易度]` | 全員 | ソロのランキングを表示 |
| `/race setsololobby` | `boatgp.admin` | ソロロビーを現在地に設定 |
| `/race setsign solo` | `boatgp.admin` | 見ている看板をソロ看板に設定 |
| `/race soloreset <プレイヤー> [サーキット]` | `boatgp.admin` | ソロの記録とゴーストを消す（コンソール可） |
| `/race stop` | `boatgp.admin` または OP | 自分が参加中のレース／ソロ挑戦を強制中止する（OP は権限ノードなしでも実行可） |
| `/race reload` | `boatgp.admin` | config.yml・items.yml・surfaces.yml・サーキットを再読み込み |
| `/race create <名前>` | `boatgp.admin` | 新規サーキットを作成して選択する |
| `/race select <名前>` | `boatgp.admin` | 既存サーキットを編集対象に選択する（`edit` も可） |
| `/race setlobby` / `setstartspawn` / `setspawn` | `boatgp.admin` | ロビー・初期スポーン・スタートグリッドを現在地に設定 |
| `/race setfinish <a\|b>` / `addcheckpoint <a\|b>` | `boatgp.admin` | ゴールライン・チェックポイントを2点ラインで設定 |
| `/race delcheckpoint <番号>` / `delfinish` / `clearcheckpoints` | `boatgp.admin` | チェックポイント／ゴールの削除・全消去 |
| `/race delspawn <番号>` / `clearspawn` | `boatgp.admin` | スタートグリッドの削除・全消去 |
| `/race additembox [半径]` / `laps <数>` / `world [名前]` | `boatgp.admin` | アイテムボックス追加・周回数・対象ワールド |
| `/race setsign <join\|leave\|start\|delete>` / `setstart` | `boatgp.admin` | 看板を参加／離脱／開始看板に設定、`delete` で登録解除（`setsign start <名前>` は開始看板、`setstart` も開始看板） |
| `/race show` | `boatgp.admin` | チェックポイント表示プレビューのON/OFF（自分のみ） |
| `/race admin <サブ> ...` | `boatgp.admin` | 上記設定コマンドの旧形式（互換のため引き続き使用可） |

!!! note "自動保存・強制開始・強制中止について"
    設定系コマンドは **実行のたびに自動保存** されるため `save` は不要です（`/race cancel` で編集破棄）。`/race start` と `/race stop` は **コマンド実行者が参加中のレース** に作用します。設定（config・items.yml・路面・サーキット）を変更したら `/race reload` で反映できます。

## 実装状況（Phase 2）

実装済みは **Phase 2（アイテムバトル）** までです。具体的には「カート物理（オート加速・路面システム）／**サーバー操舵（既定は視点操舵、`kart.look-steering: false` で左右移動キー A/D・Java/統合版で統一、`kart.input-steering`/`turn-rate`/`corner-detect` で調整）**／**ライン（A→B 2点）方式のチェックポイント・ラップ判定**／**チェックポイントのパーティクル可視化（レース中は自動・OPは `/race show` でプレビュー）**／順位計算・サイドバーHUD／スタート演出（3-2-1-GO）／コース外・スタック復帰／順位連動の簡易報酬／サーキット作成・**削除**コマンド（フラット化・**自動保存**）／**看板での参加・離脱・開始**／**初期スポーン（離脱時の戻り先）**／**レース終了後の自動ロビー帰還・連続プレイ**／アイテムシステム6種・アイテムボックス・ラバーバンド抽選・ボスバー表示・ロケットスタート」が動作します。

!!! danger "未実装の主要機能（Phase 3 以降予定）"
    以下は **未実装** です。本番イベントでの利用前に必ず把握してください。

    - ドリフト／ミニターボ
    - コース別ベストタイム・勝利数などの記録永続化（SQLite）、リーダーボード
    - カートスキン／トレイルなどのカスタマイズ
    - 同時複数レースのインスタンス化、コース投票、トーナメントモード

    なお、設計案にあった「ニセアイテムボックス・トリプルキノコ・ゲッソー・キラー」は現バージョンでは未実装で、コアアイテム6種（ダッシュキノコ・みどりこうら・あかこうら・バナナ・スター・サンダー）のみが動作します。

## トラブルシューティング

??? failure "レースに参加できない / サーキットが見つからない"
    `/race list` でサーキットが登録されているか確認してください。1件もない場合は `/race create <名前>` から作成が必要です。また、サーキットの対象ワールドが読み込まれていないと参加できません。

??? failure "サーキットが「設定OK」にならない / レースが始められない"
    レース開始に使える条件は **ロビー・スタートグリッド1個以上・チェックポイント2個以上** です。`/race status` で不足項目を確認し、`/race setlobby` / `setspawn` / `setfinish a→b` / `addcheckpoint a→b` で補ってください（変更は自動保存されます）。アイテムボックス・初期スポーン・看板は必須ではありません。

??? failure "チェックポイント（ライン）が通過判定されない"
    ゴールライン・チェックポイントは **a→b の2点で引いたライン** をコースを横切る形で通過する必要があります。ラインがコース幅をまたいでいない、または高さ（Y許容）が足りないと通過になりません。`/race setfinish a`→反対側で `setfinish b`、`addcheckpoint a`→`b` の順で、コース幅いっぱいに引いてください。高さは `... b [高さ]` で調整できます（既定±4.0）。

??? failure "看板をクリックしても参加・開始できない"
    看板は **`/race setsign`（または `setstart`）で登録済み** か確認してください（看板への直接書き込みによる登録は廃止されました）。クリック判定は **保存した座標との一致** で行うため、看板を一度壊して置き直した場合は再登録が必要です（壊すと自動解除されます）。開始看板はそのコースに参加者がいないと開始されません。クリック直後は1秒のクールダウンがあります。なお `/race join` / `leave` / `start` は全員がコマンドでも実行できます。

??? failure "アイテムボックスを通ってもアイテムが出ない"
    そのサーキットに `additembox` でアイテムボックスが追加されているか `/race status` で確認してください。また、プレイヤーが **すでにアイテムを所持している** とアイテムボックスは反応しません。取得直後のボックスは `items.yml` の `item-box.cooldown-ticks`（既定80tick）の間は再出現しません。

??? failure "看板や設定のメッセージが空欄 / 表示されない"
    `messages.yml` は起動時に不足キーが最新版から自動追記されるため、通常は空欄になりません。もし空欄になる場合は、ファイルがYAML構文エラーで読めていない（自動更新がスキップされる）可能性があります。起動ログの警告を確認し、構文を直してサーバーを再起動してください。

??? failure "アイテムの出現バランスを変えたい"
    `items.yml` の `items.<アイテム>.weight-front` / `weight-mid` / `weight-back` を編集し、`/race reload` で反映してください。`0` にするとその順位帯では出現しなくなります。効果の強さは `effects.*` で調整できます。

??? failure "レースが満員になる / 参加人数を増やしたい"
    実際の上限は **スタートグリッド数** と `config.yml` の `race.max-players` の小さい方です。人数を増やしたい場合は `/race setspawn` でスタートグリッドを追加してください。

??? failure "カートの挙動がおかしい / 壁をすり抜ける"
    カート駆動は `setVelocity` ベースのため、高速移動時にチャンク境界や壁ですり抜けが起きる可能性があります。設計上は **壁を厚く・高く（3ブロック以上）**、サーキット範囲のチャンクを常時ロードすることが推奨されています。

??? failure "コースアウト復帰が頻発する / 復帰してほしくない"
    危険路面・落下・スタックで直近チェックポイントへ復帰します。挙動を止めたい場合は `config.yml` の `kart.off-track-reset` を `false` に、落下判定のしきい値は `kart.void-y` で調整できます。スタック判定は `kart.stuck-speed-threshold` と `kart.stuck-ticks` で調整します。

??? failure "氷やブーストが効かない / 路面の挙動を変えたい"
    路面はブロック種別で判定されます。`surfaces.yml` で対象ブロックや `speed`・`grip` を編集し、`/race reload` で反映してください。未定義ブロックは `default-surface` の挙動になります。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← BoatGP 概要へ](index.md){ .md-button }
