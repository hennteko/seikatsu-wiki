<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# EmeraldHorse（育成馬）― OP・運営ガイド { .page-op #emhorse-op }

CasinoPlugin の育成馬モジュール（emhorse）の看板・コマンド・config・権限をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `emhorse`（CasinoPlugin 内） |
| メインコマンド | `/emhorse`（OP / `emhorse.admin`） |
| 前提モジュール | `bank`（必須・EmeraldAPI）、`horse`（出走連携） |
| 設定ファイル | `modules/emhorse.yml`（数値設定）、`emhorse_data.yml`（馬データ・自動保存） |

!!! info "モジュールの有効化"
    `emhorse` は CasinoPlugin の `config.yml` の `modules.emhorse.enabled` で ON/OFF します。**`modules` に項目が無い場合は既定で有効** として起動します。無効化したいときのみ `modules.emhorse.enabled: false` を手動追記してください。

## 看板の設置

プレイヤーの操作導線は3種類の看板です。看板を設置し、看板を見ながら（6ブロック以内）以下を実行すると自動整形されます（`emhorse.admin` 権限が必要）。

```text title="馬ショップ看板（子馬券・エサの購入GUI）"
/emhorse setsign shop
```
```text title="厩舎看板（愛馬の確認・専門化/脚質変更・出走登録）"
/emhorse setsign stable
```
```text title="出走受付看板（次のレースへ出走登録）"
/emhorse setsign entry
```
```text title="看板の設定を解除"
/emhorse setsign remove
```

看板1行目に `[馬ショップ]` / `[厩舎]` / `[出走受付]` と手書きしても、権限者のみ設置できます。`/emhorse setsign remove` で視線先の看板設定を解除できます。

## 管理コマンド

すべて `/emhorse <sub>`（OP または `emhorse.admin`）です。

| コマンド | 説明 |
|---|---|
| `/emhorse setsign <shop\|stable\|entry\|remove>` | 視線先の看板を各種看板に設定/解除する |
| `/emhorse givefoal <player> [bronze\|silver\|gold] [個数]` | 子馬券を付与（既定 bronze・1個） |
| `/emhorse givewhistle <player>` | 呼び笛を付与 |
| `/emhorse givefeed <player> <feedId> [個数]` | エサを付与（feedId: hay / carrot / goldcarrot / sugar / apple / goldapple） |
| `/emhorse give <player> [grade]` | 全素質一律グレード（既定C）の馬を直接付与＋呼び笛 |
| `/emhorse setcondition <player> <値>` | 体調を直接設定（0〜100） |
| `/emhorse setrating <player> <値>` | レーティングを直接設定 |
| `/emhorse setlevel <player> <stat> <Lv>` | 指定ステータス（SPEED/ACCEL/STAMINA/BURST/GUTS）のレベルを設定 |
| `/emhorse reload` | `modules/emhorse.yml` を再読み込み |
| `/emhorse status [player]` | 馬の状態を確認（自分対象なら厩舎GUI、他者ならテキスト） |

!!! warning "`/emhorse give` は既存の馬を上書きします"
    `give` は対象がすでに愛馬を持っていても確認なく置き換えます。プレイヤーの育成済みの馬が消えるため、運用時は注意してください。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `emhorse.admin` | OP | `/emhorse` 全コマンド・看板設置 |

プレイヤー向けの専用権限はありません（看板・アイテム操作で遊びます）。

## modules/emhorse.yml 主要項目

| キー | 既定値 | 説明 |
|---|---|---|
| `train.levels_per_grade` | 5 | グレード1段あたりの育成レベル上限（S＝rank8なら×5でLv40） |
| `train.base_exp` | 100 | レベルアップ必要経験値の基準（Lv N→N+1 は `base_exp×(N+1)`） |
| `mount.base_speed` / `speed_per_level` / `speed_cap` | 0.20 / 0.0015 / 0.32 | 騎乗速度（速度Lvで上昇・上限あり） |
| `mount.jump_base` / `jump_per_level` / `jump_cap` | 0.7 / 0.004 / 1.0 | ジャンプ力 |
| `mount.despawn_seconds` | 30 | 降車後の自動収納までの秒数 |
| `condition.decay_per_hour` | 5.0 | 体調の1時間あたり減少量（オフライン中も経過） |
| `foal.price.{bronze/silver/gold}` | 5000 / 15000 / 50000 | 子馬券の価格 |
| `foal.tiers.*` | （素質抽選の重み） | 各tierのS〜Gグレード出現重み |
| `feed.price/exp/condition.*` | （エサ表） | エサごとの価格・経験値・体調回復量 |
| `race.max_entries` | 4 | 1レースの育成馬の最大出走数 |
| `race.condition_consume` | 15 | 1出走あたりの体調消費 |
| `race.class_thresholds` | `[0,100,250,500,1000,2000]` | クラス（新馬〜G1）のレーティング下限 |
| `race.prize_pool` | `[500,1500,4000,10000,30000,100000]` | クラス別の賞金プール総額 |
| `race.distribution` | `[0.5,0.25,0.15,0.07,0.03]` | 着順別の賞金配分率 |
| `race.exp_finish` / `exp_win` | 15 / 25 | 完走／勝利時の育成経験値 |
| `race.rating.{first/second/third/other/dnf}` | 30/15/5/-10/-15 | 着順別レーティング増減 |

!!! warning "既存サーバーは config が自動追記されません"
    `modules/emhorse.yml` はファイルが無ければ自動生成されますが、**既存ファイルがある場合は新しいキーが自動追記されません**（コード側に既定値があるため動作はします）。値を調整したい場合は手動追記、または yml を退避して再生成し `/emhorse reload` してください。

## 注意・既知の制限

!!! note "実装状況・留意点"
    - 馬は永続データ（`emhorse_data.yml`）で管理され、召喚馬が消えても育成内容は失われません。
    - **脚質（逃げ／先行／差し／追込）と天候は現状フレーバー** で、レースの計算にはほぼ反映されません（専門化と馬場適性は反映）。
    - 出走登録は最大 `race.max_entries`（既定4）まで。超過分は先着順で、落選通知はありません。
    - 競馬（horse）モジュールが無効なときは通常の自動生成馬レースにフォールバックします。
    - 子馬券・エサ・呼び笛は PDC タグ付きアイテムで、外部の取引・露店プラグインで売買・流通させる運用も可能です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← EmeraldHorse 概要へ](index.md){ .md-button }
