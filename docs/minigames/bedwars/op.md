<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# BedWarsPlugin ― OP・運営ガイド { .page-op #bedwars-op }

BedWarsPlugin の導入・チーム/ジェネレーター/ショップ/看板の設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | BedWarsPlugin |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/bedwars`（エイリアス `/bw`） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/BedWarsPlugin/config.yml`（設定・地点・看板を一括保存） |

!!! info "仕組み"
    最大8色のチームで対戦します。各チームは **スポーン・ベッド・拠点ジェネレーター** の3点を設定すると「有効チーム」になり、有効チームが2色以上でフィールドが設定されていれば試合を開始できます。拠点ジェネレーターは鉄・金、中央のダイヤ／エメラルドジェネレーターは上位資源を湧かせます。参加者はシャッフルして有効チームへラウンドロビンで振り分けられます。地点・看板・数値設定はすべてコマンドで `config.yml` に **即自動保存** されます。

## 導入手順

1. ビルドした `BedWarsPlugin.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/BedWarsPlugin/config.yml` が自動生成される。
3. 後述の「セットアップ手順」でロビー・フィールド・チーム・ジェネレーター・ショップ・看板を設定する。
4. `/bedwars status` でいつでも設定状態を確認できる。

## セットアップ手順

専用フィールドを用意し、OP権限（`bedwars.admin`）で以下を **その場に立って／対象を見て** 実行します。地点は `config.yml` に即保存されます。

### 共通の地点

```text title="ロビー地点を現在地に設定（参加時のTP先）"
/bedwars setlobby
```
```text title="初期スポーン（離脱・終了後の戻り先）を現在地に設定"
/bedwars setstartspawn
```
```text title="フィールド範囲の角1を現在地に設定"
/bedwars setfield 1
```
```text title="フィールド範囲の角2を現在地に設定"
/bedwars setfield 2
```

!!! tip "フィールド範囲の役割"
    `setfield 1`/`2` の2点で囲んだ直方体が **建築・破壊・特殊アイテムの有効範囲** です。また **フィールド下端-5より下は奈落判定** になり、落ちると死亡します。フィールド未設定でも開始はできますが（範囲無制限扱い）、奈落判定がワールド最低高度になるため、必ず設定することを推奨します。

### チーム設定（スポーン・ベッド・拠点ジェネレーターの3点で有効化）

各色ごとに以下の3点を設定すると、その色が **有効チーム** になります。色は `red` `blue` `green` `yellow` `aqua` `white` `pink` `gray`（日本語名 赤/青/緑/黄/水色/白/桃/灰 も可）。

```text title="チームの試合内スポーンを現在地に設定"
/bedwars setspawn <色>
```
```text title="チームのベッドを登録（対象のベッドを見て実行）"
/bedwars setbed <色>
```
```text title="チームの拠点ジェネレーター（鉄・金）を現在地に設定"
/bedwars setgenerator base <色>
```

!!! warning "有効チームは2色以上必要"
    スポーン・ベッド・拠点ジェネレーターの **3点すべて** が揃った色だけが有効チームです。有効チームが **2色未満だと開始できません**。`/bedwars status` で各色の有効/無効を確認してください。ベッドが未登録・未設置のチームは「ベッドなし」で開始され（最初のデスで即脱落）、警告がログに出ます。

### 共有ジェネレーター（ダイヤ・エメラルド）

フィールド中央などに設置する **全チーム共有** の上位資源ジェネレーターです。複数箇所を登録できます。

```text title="ダイヤジェネレーターを現在地に追加（複数可）"
/bedwars setgenerator diamond
```
```text title="エメラルドジェネレーターを現在地に追加（複数可）"
/bedwars setgenerator emerald
```
```text title="最寄り(5ブロック以内)の共有ジェネレーターを解除"
/bedwars setgenerator delete
```

### ショップNPC（村人）の設置

村人NPCを湧かせる地点を登録します。試合開始時に自動でスポーンし、終了時に消えます。複数箇所を登録できます。

```text title="アイテムショップ地点を現在地に追加（複数可）"
/bedwars setshop item
```
```text title="アップグレードショップ地点を現在地に追加（複数可）"
/bedwars setshop upgrade
```
```text title="最寄り(5ブロック以内)のショップ地点を解除"
/bedwars setshop delete
```

!!! note "ショップNPCは自動生成・自動除去"
    登録地点に試合開始時に村人（AI無効・無敵・押されない）が湧き、終了・リセット時に除去されます。取りこぼしはタグ判定で掃除されるため、手動配置は不要です。

### 数値設定（コマンドで即保存）

```text title="試合の制限時間（秒）を設定"
/bedwars settime <秒>
```
```text title="開始に必要な最低人数を設定"
/bedwars setmin <人数>
```

その他の数値（リスポーン待機・ジェネレーター間隔・イベント時刻など）は `config.yml` を直接編集します（後述）。

### 看板の設置

参加・離脱・開始の **3種** の看板を登録できます。看板を見ながら（6ブロック以内）以下を実行します。テキストはプラグインが自動で書き込み・更新します（人数・状態を表示）。

```text title="参加看板を登録"
/bedwars setsign join
```
```text title="離脱看板を登録"
/bedwars setsign leave
```
```text title="開始看板を登録（複数登録可）"
/bedwars setsign start
```
```text title="登録済み看板の登録を解除（視線先の看板を自動判別）"
/bedwars setsign delete
```

!!! tip "看板は保存座標で判定"
    クリック判定は登録した座標との一致で行います（手書きテキストでは機能しません）。看板は自動的に施錠（waxed）され、編集UIが開かないようになっています。参加/開始看板には現在の参加人数と状態（待機中/試合中/終了処理中）が表示されます。

## 設定状況の確認

```text title="設定・進行状況をまとめて確認"
/bedwars status
```

地点設定・チームの有効/無効・看板・現在の状態（待機中/試合中）・参加人数・試合時間・進行中の経過秒とベッド生存が確認できます。

## config.yml 主要項目

地点・看板はコマンドで自動保存されるため手動編集は不要です。以下の数値パラメータは調整時のみ編集します。

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.min-players` | 2 | 開始に必要な最低人数（`setmin` でも変更可） |
| `settings.team-size` | 2 | 1チームの目標人数（チーム数＝参加人数÷この値の切り上げ・最大は有効チーム数。例: 2なら6人→3チーム 2vs2vs2） |
| `settings.game-time` | 2400 | 試合の制限時間（秒＝40分・`settime` でも変更可） |
| `settings.respawn-delay` | 5 | リスポーン待機（秒） |
| `settings.build-max-y` | 0 | 建築高度上限（0＝無制限） |
| `settings.generator.iron-interval` | 30 | 鉄の湧き間隔（tick） |
| `settings.generator.gold-interval` | 120 | 金の湧き間隔（tick） |
| `settings.generator.diamond-interval` | `[600, 460, 300]` | ダイヤの湧き間隔（tick）Tier I / II / III |
| `settings.generator.emerald-interval` | `[1200, 900, 600]` | エメラルドの湧き間隔（tick）Tier I / II / III |
| `settings.events.diamond-2` | 360 | ダイヤ Tier II 昇格時刻（開始からの秒） |
| `settings.events.emerald-2` | 720 | エメラルド Tier II 昇格時刻（秒） |
| `settings.events.diamond-3` | 1080 | ダイヤ Tier III 昇格時刻（秒） |
| `settings.events.emerald-3` | 1440 | エメラルド Tier III 昇格時刻（秒） |
| `settings.events.bed-destruction` | 1800 | 全ベッド自動崩壊の時刻（秒＝30分） |
| `settings.events.sudden-death` | 2160 | サドンデス（ドラゴン出現）の時刻（秒＝36分） |

座標系（`default-spawn` / `lobby-spawn` / `arena.field.min|max` / `arena.spawn.<色>` / `arena.bed.<色>` / `arena.generator.base.<色>` / `arena.generator.diamond` / `arena.generator.emerald` / `arena.shop.item` / `arena.shop.upgrade`）と看板（`sign` / `leave-sign` / `start-sign.N`）はコマンド実行時に自動保存されます。

!!! warning "既存サーバーを更新する場合（config自動マージなし）"
    本プラグインは `saveDefaultConfig()` のみで、**既存の `config.yml` に新しいキーを自動追記しません**。コード側に既定値があるため未記載でも既定値で動作しますが、**ジェネレーター間隔やイベント時刻を調整したい場合は配布 `config.yml` を参照して手動で追記**してください。地点・看板・`settime`/`setmin` の値はコマンドで都度保存されるため問題ありません。

!!! note "config の反映タイミング"
    `settings.*` の数値（ジェネレーター間隔・イベント時刻・リスポーン待機など）は試合開始時に読み込まれます。手動編集した場合は **次の試合から反映** されます（reloadコマンドはありません。手動編集後はサーバー再起動が確実です）。

## 商品・アップグレードの価格（参考）

商品と価格はコードにハードコードされており、config では変更できません。

??? question "アイテムショップの商品一覧"
    - **ブロック**: チーム色羊毛x16（鉄4）／テラコッタx16（鉄12）／板材x16（金4）／エンドストーンx12（鉄24）／黒曜石x4（エメラルド4）／はしごx8（鉄4）
    - **近接**: 石の剣（鉄10）／鉄の剣（金7）／ダイヤの剣（エメラルド4）／ノックバック棒（金5）
    - **防具（永続）**: チェーン脚靴（鉄40）／鉄脚靴（金12）／ダイヤ脚靴（エメラルド6）
    - **ツール**: ツルハシ／斧（段階式・死亡で1段階ダウン）／ハサミ（鉄20・永続）
    - **遠距離**: 弓（金12）／弓パワーI（金24）／矢x8（金2）
    - **消耗品**: 金リンゴ（金3）／ファイヤーボール（鉄40）／TNT（金4）／エンダーパール（エメラルド4）／水バケツ（金3）／スポンジx4（金2）／透明ポーション30秒（エメラルド2）／スピード45秒（エメラルド1）／跳躍45秒（エメラルド1）／橋の卵（エメラルド1）

??? question "チームアップグレードの価格（ダイヤ）"
    鋭い刃（8）／防具強化 I〜IV（5/10/20/30）／迅速な採掘 I〜II（4/6）／鍛冶場強化 I〜IV（4/8/12/16・Lv4で拠点にエメラルドも湧く）／回復プール（3）／トラップ4種（購入数で 1/2/4・最大3個キュー）

## コマンド一覧

### プレイヤー用（全員可・権限不要）

| コマンド | 説明 |
|---|---|
| `/bedwars join` | ロビーに参加する |
| `/bedwars leave` | ロビーから離脱する |
| `/bedwars start` | 試合を開始する（コマンドブロック/コンソール可） |
| `/bedwars status` | 設定・進行状況を確認する |

### 管理用（`bedwars.admin`）

| コマンド | 説明 |
|---|---|
| `/bedwars stop` | 進行中の試合を強制終了してリセット |
| `/bedwars settime <秒>` | 試合の制限時間を設定し `config.yml` に保存 |
| `/bedwars setmin <人数>` | 開始に必要な最低人数を設定し保存 |
| `/bedwars setstartspawn` / `setlobby` | 初期スポーン / ロビーを現在地に設定 |
| `/bedwars setfield <1\|2>` | フィールド範囲の角を現在地に設定 |
| `/bedwars setspawn <色>` | チームの試合内スポーンを設定 |
| `/bedwars setbed <色>` | 視線先のベッドをチームのベッドに登録 |
| `/bedwars setgenerator base <色>` | チームの拠点ジェネレーター（鉄・金）を設定 |
| `/bedwars setgenerator diamond` / `emerald` | 共有ダイヤ / エメラルドジェネレーターを追加 |
| `/bedwars setgenerator delete` | 最寄り(5ブロック以内)の共有ジェネレーターを解除 |
| `/bedwars setshop item` / `upgrade` | アイテム / アップグレードショップ地点を追加 |
| `/bedwars setshop delete` | 最寄り(5ブロック以内)のショップ地点を解除 |
| `/bedwars setsign <join\|leave\|start\|delete>` | 視線先の看板を各用途で登録 / 解除 |

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `bedwars.admin` | OP | setsign系・地点/チーム/ジェネレーター/ショップ設定・settime・setmin・stop などの管理コマンド |

!!! note "join / leave / start / status は権限不要で全員可"
    `/bedwars join`・`leave`・`start`・`status` は権限チェックがなく全プレイヤーが使えます（看板と同等）。設定・管理コマンドのみ `bedwars.admin` が必要です。コマンドレベルの権限は plugin.yml では付与せず、コード側で判定しています。

## トラブルシューティング

??? failure "試合が開始できない"
    次を `/bedwars status` で確認してください。**ロビーとフィールド範囲が設定済み**、**有効チームが2色以上**（各色にスポーン・ベッド・拠点ジェネレーターの3点）、**参加者が最低人数以上**（既定2人）。いずれかが欠けると開始できません。

??? failure "あるチームが最初のデスで脱落してしまう"
    そのチームの **ベッドが未登録／未設置** の可能性があります。`/bedwars setbed <色>` でベッドを見て登録し、実際にベッドが置かれているか確認してください（ベッドなしで開始すると即脱落＝ファイナルキル扱い）。

??? failure "ショップNPCが出ない / 商品が買えない"
    `/bedwars setshop item`（または `upgrade`）で地点を登録しているか確認してください。NPCは試合開始時に湧きます。購入は試合中の参加者のみ可能で、アイテムショップは所持資源（鉄/金/ダイヤ/エメラルド）、アップグレードショップは **ダイヤ** で支払います。

??? failure "資源が湧かない / 湧きすぎて止まる"
    拠点ジェネレーターは `/bedwars setgenerator base <色>`、中央は `setgenerator diamond`/`emerald` で登録が必要です。ジェネレーター周囲に資源が溜まりすぎる（鉄48・金16・ダイヤ8・エメラルド4を超える）と一時的に湧きが止まる仕様です（拾って解消）。

??? failure "config の値を変えても反映されない"
    `settings.*` は試合開始時に読み込まれます。手動編集後は次の試合から反映されます（reloadコマンドはありません。確実に反映するにはサーバー再起動）。既存サーバーでは新しいキーが自動追記されないため、調整したいキーは手動追記が必要です。

??? failure "試合終了後にベッドや地形が元に戻らない"
    ベッドや変化したブロックは試合開始時のスナップショットから復元されます。サーバー停止などで中断された場合は復元されないことがあります。`/bedwars stop` で強制終了するとリセット（設置ブロック撤去・ベッド復元・NPC/ドラゴン除去・ドロップ掃除）が実行されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← BedWarsPlugin 概要へ](index.md){ .md-button }
