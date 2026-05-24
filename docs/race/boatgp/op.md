<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# BoatGP ― OP・運営ガイド { .page-op #boatgp-op }

BoatGP の導入・config・サーキット作成・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | BoatGP |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/race`（エイリアス `/boatgp`） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/BoatGP/config.yml`、`messages.yml`、`surfaces.yml`、`circuits/<名前>.yml` |

!!! warning "実装状況：Phase 1（レースエンジン）"
    現在は **Phase 1（レースエンジン）** までが実装済みです。カート物理・路面・チェックポイント判定・順位HUD・コース外復帰・簡易報酬は動作しますが、**アイテムバトルは未実装（Phase 2 予定）** です。詳細は下記「実装状況」の節を確認してください。

## 導入手順

1. ビルドした `boatgp-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/BoatGP/` に `config.yml` / `messages.yml` / `surfaces.yml` が自動生成され、`circuits/` フォルダが作られる。
3. レース専用ワールド（フラットまたはボイド推奨）を用意する。
4. 後述の手順でサーキットを作成する。
5. 設定を変更したら `/race reload` で再読み込みする。

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

標準フロー（create → setfinish → addcheckpoint → addgrid → setlobby → laps → save）:

```text
/race admin create <名前>            # 編集開始（現在のワールドが対象になる）
/race admin setfinish [半径]         # フィニッシュライン(=CP0)を現在地に設定（既定半径6.0）
/race admin addcheckpoint [半径]     # チェックポイントを現在地に追加（コース順に繰り返す）
/race admin addgrid                  # スタートグリッドを現在地に追加（参加可能人数ぶん繰り返す）
/race admin setlobby                 # 待機ロビーを現在地に設定
/race admin laps <周回数>            # 周回数を設定
/race admin world [名前]             # レースワールドを明示指定（省略時は現在のワールド）
/race admin info                     # 編集状況を確認
/race admin save                     # サーキットを保存
/race admin cancel                   # 編集を破棄
```

!!! tip "保存に必要な最低条件"
    `/race admin save` で保存するには、**ロビー地点・スタートグリッド1個以上・チェックポイント2個以上（フィニッシュ＋通常CP）** が必要です。不足があると保存できず、不足項目が表示されます。`/race admin info` でいつでも確認できます。

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
| `/race stop` | `boatgp.admin` | 自分が参加中のレースを強制中止する |
| `/race reload` | `boatgp.admin` | config.yml・surfaces.yml・サーキットを再読み込み |
| `/race admin ...` | `boatgp.admin` | サーキットの作成・編集（前述） |
| `/race join [サーキット名]` | `boatgp.play` | レースに参加する |
| `/race leave` | `boatgp.play` | レースから離脱する |
| `/race list` | 全員 | サーキット一覧を表示する |

!!! note "強制開始・強制中止について"
    `/race start` と `/race stop` は **コマンド実行者が参加中のレース** に対して作用します。操作対象のレースに参加した状態で実行してください。設定を変更したら `/race reload` を実行すると、config・路面・サーキット定義が反映されます。

## 実装状況（Phase 1）

!!! danger "未実装の主要機能（Phase 2 以降予定）"
    以下は **未実装** です。本番イベントでの利用前に必ず把握してください。

    - **アイテムバトル**（バナナ・こうら・スター・サンダーなどのアイテムシステム、降車ボタンによるアイテム発動）
    - ロケットスタート、ドリフト／ミニターボ
    - コース別ベストタイム・勝利数などの記録永続化（SQLite）、リーダーボード
    - カートスキン／トレイルなどのカスタマイズ
    - 同時複数レースのインスタンス化、コース投票、トーナメントモード

実装済みは「カート物理（オート加速・路面システム）／チェックポイント・ラップ判定／順位計算・サイドバーHUD／スタート演出（3-2-1-GO）／コース外・スタック復帰／順位連動の簡易報酬／サーキット作成コマンド」までの **Phase 1（レースエンジン）** です。

## トラブルシューティング

??? failure "レースに参加できない / サーキットが見つからない"
    `/race list` でサーキットが登録されているか確認してください。1件もない場合は `/race admin create` から作成が必要です。また、サーキットの対象ワールドが読み込まれていないと参加できません。

??? failure "サーキットが保存できない"
    `/race admin save` は **ロビー・スタートグリッド1個以上・チェックポイント2個以上** がそろっていないと保存できません。`/race admin info` で不足項目を確認し、不足分のコマンドを実行してください。

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
