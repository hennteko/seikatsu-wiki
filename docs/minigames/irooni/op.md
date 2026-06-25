<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Irooni（色鬼・改造版）― OP・運営ガイド { .page-op #irooni-op }

色鬼（改造版）の導入・スポーン/ステージ/フィールド設定・看板・config（パレット・床消滅時間・難易度カーブ）・権限をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | ColorTag |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| 作者 | henry |
| メインコマンド | `/colortag`（エイリアス `/ct`／`join`・`leave`・`start` は全員、設定系は管理者） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/ColorTag/config.yml`（設定・座標・看板をすべて格納） |

!!! info "ゲームの仕組み（色当て × 床消滅 × 残機制）"
    鬼はホットバーの染料を右クリックして「安全色」を選びます。猶予（`floor-delay`・既定2秒）のあと、安全色以外のコンクリート床が一斉に空気へ置き換わり、乗れていない逃走者が落下します。落下するごとに残機が1減り、初期スポーンへ復帰。残機0で観戦モードになります。床は猶予＋復活間隔（`recover`）後に元のブロックへ戻ります。**安全色は常に鬼が選んだ1色のみ**。経過時間で床の復活間隔（`recover`）が短くなり消滅サイクルが速くなります。終盤フェーズではフェイント（予告色から別色へ切替）が確率発動します。

!!! warning "個人サバイバル（チーム分けなし）です"
    フォルダ分類上は「対戦・アクション（チーム戦）」に置かれていますが、ゲーム自体は **チーム分けのない個人サバイバル** です。鬼1人 対 逃走者全員の構図で、逃走者同士は協力も対戦もしません（生き残れば全員勝利）。

## 導入手順

1. ビルドした `ColorTag` の jar ファイルを `plugins/` フォルダに配置する。
2. サーバーを起動・再起動すると `plugins/ColorTag/config.yml` が自動生成される。
3. 後述の手順でロビー・スポーン・鬼ステージ・フィールド範囲・看板を設定する。
4. `/colortag status` で各設定状況を確認する。

!!! note "座標・看板は config.yml に直接保存されます"
    本プラグインは別ファイル（locations.yml 等）を持ちません。ロビー・スポーン・ステージ・フィールド・看板の座標は、各設定コマンドの実行時に **`config.yml` の `locations` / `field` / `signs` セクションへ即保存** されます。`config.yml` の設定（時間・残機・パレット・難易度）を手で編集した場合、`reload` コマンドは無いため **反映にはサーバー再起動**（または再読み込み）が必要です。

## セットアップ手順

OP権限（`colortag.admin`）で、設定したい場所に **立って／対象を見て** 実行します。座標は `config.yml` に即保存されます。

```text title="① ロビー地点を現在地に設定"
/colortag setlobby
```

```text title="② 初期スポーン（落下時の戻り先）を現在地に設定"
/colortag setstartspawn
```

```text title="③ ゲームスポーン（逃走者の開始位置）を現在地に設定"
/colortag setspawn
```

```text title="④ 鬼ステージ（鬼の安全な立ち位置）を現在地に設定"
/colortag setstage
```

```text title="⑤ フィールド範囲の頂点1を設定（床ブロックを見ながら／8ブロック以内）"
/colortag setfield 1
```

```text title="⑤ フィールド範囲の頂点2を設定"
/colortag setfield 2
```

```text title="設定状況を確認"
/colortag status
```

設定の流れは次のとおりです。

1. ロビーにする場所に立ち `/colortag setlobby` を実行する。
2. 落下時の戻り先に立ち `/colortag setstartspawn` を実行する。
3. 逃走者の開始位置に立ち `/colortag setspawn` を実行する。
4. 鬼を立たせる安全地点（落下対象外）に立ち `/colortag setstage` を実行する。
5. カラフルな床の範囲の対角2点を、床ブロックを見ながら `/colortag setfield 1` / `setfield 2` で指定する。
6. `/colortag status` ですべて「[OK]」になっているか確認する。

!!! tip "フィールド範囲の指定と床ブロック"
    `setfield 1` / `2` は **対角の2点** を指定し、その直方体範囲内にある **パレット色のコンクリート** だけが床として記録されます（消滅・復活の対象）。視線先のブロックを記録するため、床ブロックを見ながら実行してください（見ているブロックが無い場合は足元の1つ下を使用）。**床の下には水を敷いて** 落下を受け止めてください（落下・奈落・溺れのダメージは無効化されます）。

### フィールドの作り方

- 床は **パレットに登録した色のコンクリート**（既定: 白・橙・黄・黄緑・水色・赤の6色）で敷きます。
- 安全色以外は消滅時に空気へ置き換わり、復活時に元のコンクリートへ戻ります（プラグインが原状を保持）。
- 床の下は **水** を敷いて落下を受け止めます。
- 鬼ステージは床範囲の外（落下しない安全地点）に用意します。

### 看板の設置

参加・離脱・鬼立候補・開始の **4種** の看板を登録できます。参加・離脱・開始は `/colortag join` / `leave` / `start` コマンドでも行えますが（全員使用可）、**鬼立候補だけは看板専用** です。看板で完結させたい場合は4種とも設置してください。看板を見ながら（6ブロック以内）以下を実行します。

```text title="参加看板を登録（クリックで参加・ロビーへTP）"
/colortag setsign join
```

```text title="離脱看板を登録（クリックでロビーから離脱）"
/colortag setsign leave
```

```text title="鬼立候補看板を登録（クリックで鬼を希望登録／再クリックで取消）"
/colortag setsign oni
```

```text title="開始看板を登録（クリックでゲーム開始）"
/colortag setsign start
```

```text title="看板登録を解除（解除したい看板を見ながら／種類は自動判定）"
/colortag setsign delete
```

登録時は看板の表面に用途の文字が自動で書き込まれ、ワックス掛け（編集ロック）されます。`delete` で解除すると文字とワックスも元に戻ります。クリック判定は保存した看板位置との一致で行います（手書きの看板では機能しません）。

### 残機・制限時間・床消滅時間の調整

```text title="残機を変更（既定2・1以上）"
/colortag setzanki <数>
```

```text title="制限時間を変更（既定180秒・10以上）"
/colortag settime <秒>
```

```text title="床消滅までの時間を変更（既定2秒・0より大きく30以下・小数可）"
/colortag setdelay <秒>
```

これらは即 `config.yml` に保存されます（パレット・難易度カーブの編集は config.yml を直接編集します）。`setdelay` は鬼が色を選んでから床が消えるまでの猶予（`floor-delay`）を秒で設定します（例: `2` / `1.5` / `0.5`）。

## config.yml 設定項目

### settings セクション

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.game-time` | 180 | 制限時間（秒）。経過で生存者の勝利（既定3分）。`settime` で変更可 |
| `settings.default-zanki` | 2 | 残機。落下するごとに -1、0で観戦モード。`setzanki` で変更可 |
| `settings.floor-delay` | 2.0 | 鬼が色を選んでから床が消えるまでの猶予（秒・小数可）。`setdelay` で変更可 |
| `settings.palette` | WHITE / ORANGE / YELLOW / LIME / LIGHT_BLUE / RED | 床に使う色（コンクリートの `DyeColor` 名）。鬼に配る染料もこの色 |
| `settings.difficulty` | 下表参照 | 難易度カーブ（経過秒 `from` で切り替え） |

### difficulty（難易度カーブ）

`difficulty` は経過秒 `from` で切り替わるフェーズの配列です。各フェーズに以下のキーがあります。

| キー | 説明 |
|---|---|
| `from` | このフェーズが適用される経過秒（昇順） |
| `recover` | 床が復活するまでの間隔（秒）。小さいほど床消滅サイクルが速い |
| `feint` | フェイント（予告後に色を切替）の許可（`true`/`false`） |

既定の難易度カーブは次のとおりです。

| 経過 | recover | feint |
|---|---|---|
| 0秒〜 | 5.0 | false |
| 60秒〜 | 3.0 | false |
| 120秒〜 | 1.5 | true |

!!! note "安全色は常に1色・床消滅猶予は `floor-delay` で固定"
    安全な色は **常に「鬼が選んだ1色」のみ** です（フェーズで安全色数は変わりません）。予告から床消滅までの猶予はフェーズではなく `settings.floor-delay`（既定2秒）で決まり、全フェーズ共通です。難易度カーブで変化するのは床の復活間隔（`recover`）とフェイント許可（`feint`）です。

!!! note "難易度・パレットの編集は config.yml を直接編集"
    `palette` と `difficulty` を変更するコマンドはありません。`config.yml` を直接編集してください。`reload` コマンドが無いため、**変更の反映にはサーバー再起動** が必要です（`setzanki` / `settime` / `setdelay` はコマンドで即反映されます）。

### locations / field / signs セクション

- `locations.{lobby,startspawn,gamespawn,stage}` … 各座標（向きあり）。設定コマンドで自動保存。
- `field.{pos1,pos2}` … フィールド範囲の対角2点。`setfield 1` / `2` で自動保存。
- `signs.{join,leave,oni,start}` … 各看板の位置。`setsign` で自動保存。

!!! warning "既存サーバーは config が自動追記されません"
    本プラグインは `saveDefaultConfig()` のみで、既存の `config.yml` に新キーを自動追記しません（コード側に既定値があるため、欠けていても既定値で動作します）。`palette` や `difficulty` を調整する場合は手動で追記するか、`config.yml` を退避して再生成してください。座標・看板はコマンドで `config.yml` に保存されます。

## 権限ノード

`plugin.yml` には `colortag.admin` の **1つだけ** が定義されています。

| 権限 | 既定 | 用途 |
|---|---|---|
| `colortag.admin` | OP | `/colortag` の **設定系**（setsign・setlobby・setspawn系・setstage・setfield・setzanki・settime・setdelay・status） |

!!! note "join / leave / start は全員・設定系のみ権限必要"
    `/colortag join` / `leave` / `start` は **権限不要で全員が使用可能** です（看板クリックでも同等の操作ができます）。鬼立候補は看板（鬼立候補看板）専用です。一方、設定系サブコマンド（setsign・setlobby・setstartspawn・setspawn・setstage・setfield・setzanki・settime・setdelay・status）は `colortag.admin`（既定OP）が必要です。`status` も管理者専用です。

## コマンド一覧

### 全員使用可能（権限不要）

| コマンド | 説明 |
|---|---|
| `/colortag join` | ゲームに参加（ロビーへTP） |
| `/colortag leave` | ゲームから離脱 |
| `/colortag start` | ゲーム開始（2人以上必要） |

`stop` / `reload` コマンドはありません（強制終了はサーバーのプラグイン無効化時に自動終了、設定の再読み込みは再起動）。鬼立候補はコマンドが無く、鬼立候補看板のクリックで行います。

### 管理コマンド

設定系サブコマンドはすべて `colortag.admin`（既定OP）が必要です。

| コマンド | 説明 |
|---|---|
| `/colortag setlobby` | ロビーを現在地に設定 |
| `/colortag setstartspawn` | 初期スポーン（落下時の戻り先）を現在地に設定 |
| `/colortag setspawn` | ゲームスポーン（逃走者の開始位置）を現在地に設定 |
| `/colortag setstage` | 鬼ステージ（鬼の立ち位置）を現在地に設定 |
| `/colortag setfield <1\|2>` | 視線先のブロックをフィールド範囲の頂点に設定 |
| `/colortag setsign <join\|leave\|oni\|start>` | 視線先の看板を各用途で登録 |
| `/colortag setsign delete` | 視線先の看板の登録を解除（種類は自動判定） |
| `/colortag setzanki <数>` | 残機を設定（1以上・既定2） |
| `/colortag settime <秒>` | 制限時間を設定（10以上・既定180） |
| `/colortag setdelay <秒>` | 床消滅までの猶予を設定（0より大きく30以下・小数可・既定2） |
| `/colortag status` | 設定状況を表示 |

!!! tip "TAB補完に対応"
    `/colortag` のサブコマンド（管理者には設定系も表示）、`setsign` の種別（join/leave/oni/start/delete）、`setfield` の番号（1/2）はTAB補完できます。

## トラブルシューティング

??? failure "「ゲームの設定が未完了です」「設定が未完了のため開始できません」と表示される"
    ロビー・初期スポーン・ゲームスポーン・鬼ステージ・フィールド点1・点2・パレット（1色以上）のいずれかが未設定です。`/colortag status` で「[--]」の項目を確認し、対応するコマンドで設定してください。

??? failure "「参加者が足りません（2人以上必要）」と表示される"
    ゲーム開始には参加者が **2人以上** 必要です。参加看板でロビーに人を集めてから開始看板を押してください。

??? failure "看板をクリックしても反応しない"
    看板は `/colortag setsign <種別>` で登録した位置で判定されます。登録済みか `/colortag status` で確認してください（手書きの看板では機能しません）。

??? failure "床が消えない／復活しない"
    フィールド範囲（`setfield 1` / `2`）内に **パレット色のコンクリート** が敷かれているか確認してください。範囲外や別ブロックは床として記録されません。`palette` に無い色のコンクリートも対象外です。

??? failure "逃走者が落下で死んでしまう"
    床の下に **水** を敷いて落下を受け止めてください（ゲーム中は落下・奈落・溺れダメージが無効化されますが、水で確実に受け止める想定です）。初期スポーンへ戻されるため、初期スポーンがフィールド近くにあるか確認してください。

??? failure "config.yml（パレット・難易度）を編集したのに反映されない"
    `reload` コマンドが無いため、`palette` / `difficulty` の変更は **サーバー再起動** で反映されます。`setzanki` / `settime` / `setdelay` はコマンド実行で即反映されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Irooni 概要へ](index.md){ .md-button }
