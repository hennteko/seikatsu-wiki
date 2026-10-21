<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Irooni（色鬼・改造版）― OP・運営ガイド { .page-op #irooni-op }

色鬼（改造版）の導入・スポーン/ステージ/フィールド設定・看板・config（パレット・床消滅時間・難易度カーブ）・権限をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | ColorTag |
| バージョン | 1.1.0 |
| api-version | 1.21 |
| 作者 | henry |
| メインコマンド | `/colortag`（エイリアス `/ct`／`join`・`leave`・`start`・`status`・`stats`・`solo`・`solotop` は全員、設定系・`stop`・`reload` は管理者） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/ColorTag/config.yml`（設定・座標・看板・ソロ設定をすべて格納） |
| 成績ファイル | `plugins/ColorTag/stats.yml`（通常戦）／`plugins/ColorTag/solo-stats.yml`（ソロ）（自動生成） |

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
    本プラグインは別ファイル（locations.yml 等）を持ちません。ロビー・スポーン・ステージ・フィールド・看板の座標は、各設定コマンドの実行時に **`config.yml` の `locations` / `field` / `signs` セクションへ即保存** されます。`config.yml` の設定（時間・残機・パレット・難易度・ソロ）を手で編集した場合は、**`/colortag reload`**（または管理メニューのリロード）で反映できます。

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

```text title="⑤ フィールド範囲の頂点1を設定（床の角に立って実行／足元の1つ下を記録）"
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
5. カラフルな床の範囲の対角2点に **立って**、`/colortag setfield 1` / `setfield 2` で指定する（実行位置の **足元の1つ下** のブロックの高さが床の高さとして記録される）。
6. `/colortag status` ですべて「[OK]」になっているか確認する。

!!! tip "フィールド範囲の指定と床ブロック"
    `setfield 1` / `2` は **対角の2点** を指定します。記録されるのは **実行位置の足元の1つ下** のブロック座標で、この高さ（Y）が床の高さとして使われます（視線先のブロックは見ません）。床はゲーム開始時にこの範囲へ自動生成され、パレット色のコンクリートが消滅・復活の対象になります。**床の下には水を敷いて** 落下を受け止めてください（落下・奈落・溺れのダメージは無効化されます）。

### フィールドの作り方

- 床は **ゲーム開始時にプラグインが自動生成** します。`setfield 1` / `2` で囲った範囲に、パレットの色（既定: 白・橙・黄・黄緑・水色・赤の6色）のコンクリートが **クラスタ状（4〜9マスの色の塊）にランダム配色** で1層敷かれます。配色は毎試合変わります。
- そのため、OPが手で床のコンクリートを敷く必要はありません（`setfield` で範囲と高さだけ指定すればOK）。
- 安全色以外は消滅時に空気へ置き換わり、復活時に元のコンクリートへ戻ります（プラグインが原状を保持）。
- 床の下（自動生成される床の1つ下）には **水** を敷いて落下を受け止めます。
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

### 残機・制限時間・床消滅時間・人数の調整

```text title="残機を変更（既定2・1以上）"
/colortag setzanki <数>
```

```text title="制限時間を変更（既定180秒・10以上）"
/colortag settime <秒>
```

```text title="床消滅までの時間を変更（既定2秒・0より大きく30以下・小数可）"
/colortag setdelay <秒>
```

```text title="最大参加人数を変更（既定16・1〜200）"
/colortag setmax <人数>
```

```text title="最低開始人数を変更（既定2・1〜200）"
/colortag setmin <人数>
```

これらは即 `config.yml` に保存されます（パレット・難易度カーブの編集は config.yml を直接編集します）。`setdelay` は鬼が色を選んでから床が消えるまでの猶予（`floor-delay`）を秒で設定します（例: `2` / `1.5` / `0.5`）。`setmax` / `setmin` は満員判定（`max-players`）と開始に必要な最低人数（`min-players`）を変更します。

## config.yml 設定項目

### settings セクション

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.game-time` | 180 | 制限時間（秒）。経過で生存者の勝利（既定3分）。`settime` で変更可 |
| `settings.default-zanki` | 2 | 残機。落下するごとに -1、0で観戦モード。`setzanki` で変更可 |
| `settings.floor-delay` | 2.0 | 鬼が色を選んでから床が消えるまでの猶予（秒・小数可）。`setdelay` で変更可 |
| `settings.max-players` | 16 | 最大参加人数（満員判定）。`setmax` で変更可（1〜200） |
| `settings.min-players` | 2 | 開始に必要な最低人数。`setmin` で変更可（1〜200） |
| `settings.result-seconds` | 10 | 試合終了後の結果発表の表示秒数（経過後にロビーへ復帰）。コマンドでの変更不可・`config.yml` を直接編集 |
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
    `palette` と `difficulty` を変更するコマンドはありません。`config.yml` を直接編集し、**`/colortag reload`** で反映してください（`setzanki` / `settime` / `setdelay` はコマンドで即反映されます）。

### locations / field / signs セクション

- `locations.{lobby,startspawn,gamespawn,stage}` … 各座標（向きあり）。設定コマンドで自動保存。
- `field.{pos1,pos2}` … フィールド範囲の対角2点。`setfield 1` / `2` で自動保存。
- `signs.{join,leave,oni,start,solo}` … 各看板の位置。`setsign` で自動保存。

### solo セクション（ソロモード）

ソロモード（後述）の設定は `config.yml` の `solo` セクションにまとまっています。主なキーは次のとおりです（詳細はファイル内コメント参照）。

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | true | ソロモードの有効/無効 |
| `solo.menu-delay` | 3 | 試合後ロビーへ戻ってからソロメニューを開くまでの秒数 |
| `solo.result-seconds` | 5 | ソロの結果表示秒数 |
| `solo.countdown` | 3 | 開始前カウントダウン秒数 |
| `solo.broadcast-result` | best-only | 結果の全体通知（none／best-only／all） |
| `solo.ranking.size` | 10 | `/colortag solotop` で表示する人数 |
| `solo.menu-item` | COMPASS / slot 4 | ロビー中に持つソロメニューアイテム |
| `solo.respawn-safe` | 2.0 | 復帰直後に足元マスを残す秒数 |
| `solo.reach-margin` | 0.8 | CPUが「たどり着ける色」を選ぶ判定の余裕（小さいほど易しい） |
| `solo.difficulties` | easy / normal / hard | 難易度定義（残機・上限時間・CPUの意地悪さ・フェーズ・ランク基準） |

!!! note "不足キーは起動時に自動追記されます"
    起動時・`/colortag reload` 時に、jar 内の最新 `config.yml` と突き合わせて**不足している項目を既定値＋コメント付きで自動追記**します（既存の値は変更しません／変更があったときのみ `config.yml.bak` を保存）。座標・看板・フィールド・`solo.difficulties` などのデータ領域は補完対象外です。

## ソロモード（CPU鬼）

1人でも遊べる **ソロモード** が用意されています。CPU（コンピューター）が鬼となって色を選び、プレイヤーは生き残った時間を競います。

- **会場は通常戦と共用** … フィールド・ゲームスポーン・鬼ステージ・ロビーはそのまま使われるため、**追加の地点設定は不要** です（通常戦をセットアップしてあればすぐ遊べます）。
- **入り方** … `/colortag solo [難易度]`、ロビー中に配られる **ソロメニューアイテム**（既定 COMPASS・ホットバー5番）を右クリック、または **ソロ看板**（`/colortag setsign solo`）のクリックで入れます。難易度を省略するとメニューが開きます。
- **難易度** … 既定で `easy`（かんたん・★1・残機3）／`normal`（ふつう・★2・残機2）／`hard`（むずかしい・★3・残機1）の3段階。`config.yml` の `solo.difficulties` で残機・上限時間・CPUの意地悪さ（`malice`）・フェーズ・ランク基準を調整できます。
- **ランキングと記録** … 結果は `plugins/ColorTag/solo-stats.yml` に保存され、`/colortag solotop <難易度>` で上位（既定10人）を表示します。自己ベスト更新時などの全体通知は `solo.broadcast-result`（既定 best-only）で制御します。

```text title="ソロメニューを開く看板を登録（任意）"
/colortag setsign solo
```

!!! note "ソロモードの有効/無効"
    `solo.enabled: false` にするとソロモードを無効化できます。無効時は `/colortag solo` やソロ看板は使えません。

## 権限ノード

`plugin.yml` には `colortag.admin` の **1つだけ** が定義されています。

| 権限 | 既定 | 用途 |
|---|---|---|
| `colortag.admin` | OP | `/colortag` の **設定系**（setsign・setlobby・setspawn系・setstage・setfield・setzanki・settime・setdelay・setmax・setmin）、**`wand`・`menu`・`stop`・`reload`・`forceunlock`**、および `join` / `leave` での **他プレイヤー指定** |

!!! note "join / leave / start / status / stats / solo / solotop は全員・設定系と stop/reload は権限必要"
    `/colortag join` / `leave` / `start` / `status` / `stats` / `solo` / `solotop` は **権限不要で全員が使用可能** です（看板クリックでも join/leave/oni/start/solo の操作ができます）。鬼立候補は看板（鬼立候補看板）専用です。一方、設定系サブコマンド（setsign・setlobby・setstartspawn・setspawn・setstage・setfield・setzanki・settime・setdelay・setmax・setmin）と **`wand`・`menu`・`stop`・`reload`・`forceunlock`**、`join` / `leave` で **他プレイヤー名を指定** する操作は `colortag.admin`（既定OP）が必要です。`status`（設定状況）と `stats`（成績）は閲覧のみで全員可です。

## コマンド一覧

### 全員使用可能（権限不要）

| コマンド | 説明 |
|---|---|
| `/colortag join` | ゲームに参加（ロビーへTP） |
| `/colortag leave` | ゲームから離脱 |
| `/colortag start` | ゲーム開始（最低人数以上必要・コンソール／コマンドブロックからも実行可） |
| `/colortag solo [難易度]` | ソロモードに入る（引数省略でメニュー表示）。CPU鬼から逃げ切る1人用モード |
| `/colortag solotop <難易度>` | ソロのランキングを表示（コンソールからも可） |
| `/colortag status` | 設定状況を表示（読み取り専用） |
| `/colortag stats [名前]` | 自分または指定プレイヤーの成績を表示（読み取り専用） |

設定の再読み込みは **`/colortag reload`**（`colortag.admin` 必要）で行えます。鬼立候補はコマンドが無く、鬼立候補看板のクリックで行います。試合の強制停止は **`/colortag stop`**（`colortag.admin` 必要）で行えます（プラグイン無効化時にも自動で停止します）。

### 管理コマンド

設定系サブコマンドはすべて `colortag.admin`（既定OP）が必要です。

| コマンド | 説明 |
|---|---|
| `/colortag setlobby` | ロビーを現在地に設定 |
| `/colortag setstartspawn` | 初期スポーン（落下時の戻り先）を現在地に設定 |
| `/colortag setspawn` | ゲームスポーン（逃走者の開始位置）を現在地に設定 |
| `/colortag setstage` | 鬼ステージ（鬼の立ち位置）を現在地に設定 |
| `/colortag setfield <1\|2>` | 足元のブロックをフィールド範囲の頂点に設定 |
| `/colortag setsign <join\|leave\|oni\|start\|solo>` | 視線先の看板を各用途で登録（`solo` はソロメニューを開く看板） |
| `/colortag setsign delete` | 視線先の看板の登録を解除（種類は自動判定） |
| `/colortag setzanki <数>` | 残機を設定（1以上・既定2） |
| `/colortag settime <秒>` | 制限時間を設定（10以上・既定180） |
| `/colortag setdelay <秒>` | 床消滅までの猶予を設定（0より大きく30以下・小数可・既定2） |
| `/colortag setmax <人数>` | 最大参加人数を設定（1〜200・既定16） |
| `/colortag setmin <人数>` | 最低開始人数を設定（1〜200・既定2） |
| `/colortag join\|leave <名前>` | 指定プレイヤーを参加／離脱させる（他者操作はOP限定） |
| `/colortag wand` | 管理ツール（設定用アイテム）を受け取る。右クリックで管理メニューを開く |
| `/colortag menu` | 管理メニュー（GUI）を開く |
| `/colortag stop` | 進行中の試合を強制停止（結果発表・成績記録なしで即ロビーへ） |
| `/colortag reload` | config.yml を再読み込み（ソロ設定・看板も再反映） |
| `/colortag forceunlock <名前> [confirm]` | 他ゲーム参加中フラグが異常で残った場合に強制解除 |

!!! tip "TAB補完に対応"
    `/colortag` のサブコマンド（管理者には設定系も表示）、`setsign` の種別（join/leave/oni/start/solo/delete）、`setfield` の番号（1/2）、`solo` / `solotop` の難易度、`join` / `leave` / `stats` / `forceunlock` のオンラインプレイヤー名はTAB補完できます。

## 成績の記録（stats.yml）

試合が正常終了すると、参加者の成績が `plugins/ColorTag/stats.yml` に自動保存されます（`/colortag stop` での強制停止時は記録されません）。`/colortag stats [名前]` で誰でも閲覧できます。

| 記録項目 | 内容 |
|---|---|
| 参加回数（`games`） | その試合に参加した回数 |
| 生存勝利（`wins`） | 時間切れ時に生存していた回数（逃走者の勝利） |
| 鬼で落とした延べ人数（`oni-drops`） | 鬼を担当した試合で落下させた延べ人数 |
| 最長生存（`best-survival`） | 逃走者として生き残った最長秒数 |

!!! note "成績ファイルはコマンドで生成"
    `stats.yml` は初回アクセス時に自動生成されます。手で編集する必要はありません。

## トラブルシューティング

??? failure "「ゲームの設定が未完了です」「設定が未完了のため開始できません」と表示される"
    ロビー・初期スポーン・ゲームスポーン・鬼ステージ・フィールド点1・点2・パレット（1色以上）のいずれかが未設定です。`/colortag status` で「[--]」の項目を確認し、対応するコマンドで設定してください。

??? failure "「参加者が足りません（○人以上必要）」と表示される"
    ゲーム開始には `min-players`（既定2人）以上の参加者が必要です。参加看板でロビーに人を集めてから開始看板を押してください。最低人数は `/colortag setmin <人数>` で変更できます。

??? failure "看板をクリックしても反応しない"
    看板は `/colortag setsign <種別>` で登録した位置で判定されます。登録済みか `/colortag status` で確認してください（手書きの看板では機能しません）。

??? failure "床が消えない／復活しない"
    フィールド範囲（`setfield 1` / `2`）内に **パレット色のコンクリート** が敷かれているか確認してください。範囲外や別ブロックは床として記録されません。`palette` に無い色のコンクリートも対象外です。

??? failure "逃走者が落下で死んでしまう"
    床の下に **水** を敷いて落下を受け止めてください（ゲーム中は落下・奈落・溺れダメージが無効化されますが、水で確実に受け止める想定です）。初期スポーンへ戻されるため、初期スポーンがフィールド近くにあるか確認してください。

??? failure "config.yml（パレット・難易度・ソロ）を編集したのに反映されない"
    `palette` / `difficulty` / `solo` の変更は **`/colortag reload`**（または管理メニューのリロード・サーバー再起動）で反映されます。`setzanki` / `settime` / `setdelay` はコマンド実行で即反映されます。

---

## 全ゲーム共通の改修（2026-09）

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ゲーム名】名前」になり、離脱で元に戻ります（表示名は config の `display-name`）。
- **参加/離脱の全体告知** … 参加・離脱時にサーバー全体へ「【ゲーム名】名前 が参加しました (N人)」等を通知します。
- **同時参加は1ゲームまで** … 他ゲームに参加中は参加が拒否されます（「【○○】に参加中です」）。異常で参加ロックが残った場合はOPが `/<コマンド> forceunlock <プレイヤー>` で解除できます。
- **退避データのファイル保存** … ロビー入場時に退避した所持品を `plugins/<プラグイン>/vault/<UUID>.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します（退避・復元は各1回、試合終了時は復元しません）。

### 追加された config キー

| キー | 説明 |
|---|---|
| `display-name` | ゲーム表示名（「【…】」の中身） |
| `messages.join-broadcast` | 参加の全体告知文 |
| `messages.leave-broadcast` | 離脱の全体告知文 |
| `messages.already-in-other-game` | 他ゲーム参加中に拒否したときの文言 |

!!! note "config は自動で追記されるようになりました"
    起動時・`/colortag reload` 時に、`config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／変更があったときのみ `config.yml.bak` を保存）。座標・看板・フィールド・`solo.difficulties` などのデータ領域は補完対象外です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Irooni 概要へ](index.md){ .md-button }
