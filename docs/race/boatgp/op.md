<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# BoatGP ― OP・運営ガイド { .page-op #boatgp-op }

BoatGP の導入・config・items.yml・サーキット作成・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | BoatGP |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/race`（エイリアス `/boatgp`） |
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

!!! warning "Phase 1 から更新する場合の注意"
    既存サーバーを Phase 1 から Phase 2 へ更新する場合、`items.yml` は無ければ自動生成されますが、`messages.yml` は **既存ファイルがあると新しい `item:` セクション（アイテム関連メッセージ）が自動追記されません**。アイテム発動時のメッセージを正しく表示するには、既存の `messages.yml` に `item:` セクションを追記するか、ファイルを一度退避して再生成してください。

!!! tip "サーバー実装について"
    エンティティ（ボート）を多用するミニゲームのため、設計上は **Paper の採用が推奨** されています。Spigot でも動作しますが、イベント処理・最適化の面で Paper が有利です。

## config.yml 主要項目

### レース設定（`race`）

| キー | 既定値 | 説明 |
|---|---|---|
| `race.min-players` | 2 | レース開始に必要な最少人数 |
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
| `kart.corner-speed` | 0.72 | 操舵中（パドル左右入力中）の速度倍率。小さいほどコーナーで減速して曲がりが詰まる（推奨 0.6〜0.85） |

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

!!! note "アイテムの操作仕様"
    アイテムの発動は **降車操作（`VehicleExitEvent`）の再利用** で実装されています。レース中の降車ボタン押下＝所持アイテムの発動、カウントダウン中の降車ボタン押下＝ロケットスタート判定になります。Java版・統合版の双方で同じ操作になるよう設計されています。

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

サーキットは `/race admin` コマンドで作成し、`plugins/BoatGP/circuits/<名前>.yml` に保存されます。yml の手書きは不要です。OP権限を持った状態でレース専用ワールドに入り、**設置したい地点に立って** コマンドを実行します（実行位置が座標として保存されます）。

標準フロー（create → setfinish → addcheckpoint → addgrid → additembox → setlobby → laps → save）:

```text title="編集開始（現在のワールドが対象になる）"
/race admin create <名前>
```

```text title="フィニッシュライン(=CP0)を現在地に設定（既定半径6.0）"
/race admin setfinish [半径]
```

```text title="チェックポイントを現在地に追加（コース順に繰り返す）"
/race admin addcheckpoint [半径]
```

```text title="スタートグリッドを現在地に追加（参加可能人数ぶん繰り返す）"
/race admin addgrid
```

```text title="アイテムボックスを現在地に追加（任意・複数可）"
/race admin additembox [半径]
```

```text title="待機ロビーを現在地に設定"
/race admin setlobby
```

```text title="周回数を設定"
/race admin laps <周回数>
```

```text title="レースワールドを明示指定（省略時は現在のワールド）"
/race admin world [名前]
```

```text title="編集状況を確認"
/race admin info
```

```text title="サーキットを保存"
/race admin save
```

```text title="編集を破棄"
/race admin cancel
```

!!! tip "保存に必要な最低条件"
    `/race admin save` で保存するには、**ロビー地点・スタートグリッド1個以上・チェックポイント2個以上（フィニッシュ＋通常CP）** が必要です。不足があると保存できず、不足項目が表示されます。`/race admin info` でいつでも確認できます（アイテムボックス数も表示されます）。

!!! note "アイテムボックスの配置"
    `additembox` は **任意** です。アイテムボックスを置かないサーキットも保存・走行できます（その場合アイテムは出現しません）。アイテムバトルを楽しませたいコースには、コース上の通りやすい位置へ複数配置してください。半径を省略すると `items.yml` の `item-box.default-radius`（既定3.0）が使われます。

!!! note "チェックポイントとスタートグリッド"
    チェックポイントの index0 は必ずフィニッシュライン（`setfinish`）です。`addcheckpoint` でコースの進行順に追加していきます。スタートグリッド数が **そのサーキットの実質の最大参加人数** になります（`config.yml` の `race.max-players` と、グリッド数の小さい方が上限）。設計上はコース幅7ブロック前後・壁の高さ3ブロック以上・1周400ブロック前後が目安です。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `boatgp.play` | 全員 | レースに参加できる |
| `boatgp.admin` | OP | サーキットの作成・管理、レース強制操作（start/stop/reload/admin） |

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/race start` | `boatgp.admin` | 自分が参加中のレースを強制開始する |
| `/race stop` | `boatgp.admin` または OP | 自分が参加中のレースを強制中止する（OP は権限ノードなしでも実行可） |
| `/race reload` | `boatgp.admin` | config.yml・items.yml・surfaces.yml・サーキットを再読み込み |
| `/race admin ...` | `boatgp.admin` | サーキットの作成・編集（前述。`additembox` を含む） |
| `/race join [サーキット名]` | `boatgp.play` | レースに参加する |
| `/race leave` | `boatgp.play` | レースから離脱する |
| `/race list` | 全員 | サーキット一覧を表示する |

!!! note "強制開始・強制中止について"
    `/race start` と `/race stop` は **コマンド実行者が参加中のレース** に対して作用します。操作対象のレースに参加した状態で実行してください。設定を変更したら `/race reload` を実行すると、config・items.yml・路面・サーキット定義が反映されます。

## 実装状況（Phase 2）

実装済みは **Phase 2（アイテムバトル）** までです。具体的には「カート物理（オート加速・路面システム）／チェックポイント・ラップ判定／順位計算・サイドバーHUD／スタート演出（3-2-1-GO）／コース外・スタック復帰／順位連動の簡易報酬／サーキット作成コマンド／アイテムシステム6種・アイテムボックス・ラバーバンド抽選・ボスバー表示・ロケットスタート」が動作します。

!!! danger "未実装の主要機能（Phase 3 以降予定）"
    以下は **未実装** です。本番イベントでの利用前に必ず把握してください。

    - ドリフト／ミニターボ
    - コース別ベストタイム・勝利数などの記録永続化（SQLite）、リーダーボード
    - カートスキン／トレイルなどのカスタマイズ
    - 同時複数レースのインスタンス化、コース投票、トーナメントモード

    なお、設計案にあった「ニセアイテムボックス・トリプルキノコ・ゲッソー・キラー」は現バージョンでは未実装で、コアアイテム6種（ダッシュキノコ・みどりこうら・あかこうら・バナナ・スター・サンダー）のみが動作します。

## トラブルシューティング

??? failure "レースに参加できない / サーキットが見つからない"
    `/race list` でサーキットが登録されているか確認してください。1件もない場合は `/race admin create` から作成が必要です。また、サーキットの対象ワールドが読み込まれていないと参加できません。

??? failure "サーキットが保存できない"
    `/race admin save` は **ロビー・スタートグリッド1個以上・チェックポイント2個以上** がそろっていないと保存できません。`/race admin info` で不足項目を確認し、不足分のコマンドを実行してください。アイテムボックスは保存の必須条件ではありません。

??? failure "アイテムボックスを通ってもアイテムが出ない"
    そのサーキットに `additembox` でアイテムボックスが追加されているか `/race admin info` で確認してください。また、プレイヤーが **すでにアイテムを所持している** とアイテムボックスは反応しません。取得直後のボックスは `items.yml` の `item-box.cooldown-ticks`（既定80tick）の間は再出現しません。

??? failure "アイテム発動時のメッセージが文字化け / 表示されない"
    Phase 1 からの更新で `messages.yml` をそのまま使っている場合、`item:` セクションが無いと正しく表示されません。`messages.yml` に `item:` セクションを追記するか、ファイルを退避して再生成してください（導入手順の注意書きを参照）。

??? failure "アイテムの出現バランスを変えたい"
    `items.yml` の `items.<アイテム>.weight-front` / `weight-mid` / `weight-back` を編集し、`/race reload` で反映してください。`0` にするとその順位帯では出現しなくなります。効果の強さは `effects.*` で調整できます。

??? failure "レースが満員になる / 参加人数を増やしたい"
    実際の上限は **スタートグリッド数** と `config.yml` の `race.max-players` の小さい方です。人数を増やしたい場合は `/race admin addgrid` でグリッドを追加してください。

??? failure "カートの挙動がおかしい / 壁をすり抜ける"
    カート駆動は `setVelocity` ベースのため、高速移動時にチャンク境界や壁ですり抜けが起きる可能性があります。設計上は **壁を厚く・高く（3ブロック以上）**、サーキット範囲のチャンクを常時ロードすることが推奨されています。

??? failure "コースアウト復帰が頻発する / 復帰してほしくない"
    危険路面・落下・スタックで直近チェックポイントへ復帰します。挙動を止めたい場合は `config.yml` の `kart.off-track-reset` を `false` に、落下判定のしきい値は `kart.void-y` で調整できます。スタック判定は `kart.stuck-speed-threshold` と `kart.stuck-ticks` で調整します。

??? failure "氷やブーストが効かない / 路面の挙動を変えたい"
    路面はブロック種別で判定されます。`surfaces.yml` で対象ブロックや `speed`・`grip` を編集し、`/race reload` で反映してください。未定義ブロックは `default-surface` の挙動になります。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← BoatGP 概要へ](index.md){ .md-button }
