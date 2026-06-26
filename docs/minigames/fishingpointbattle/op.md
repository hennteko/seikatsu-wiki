<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# FishingPointBattle ― OP・運営ガイド { .page-op #fishingpointbattle-op }

FishingPointBattle の導入・エリア設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | FishingPointBattle |
| メインコマンド | `/fpb`（エイリアス `/fishingpointbattle`、`/fishing`） |
| 依存プラグイン | なし（単独で動作） |
| 設定ファイル | `plugins/FishingPointBattle/config.yml` |
| api-version | 26.1.2 |

## 導入手順

1. ビルドした `FishingPointBattle` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/FishingPointBattle/config.yml` が自動生成される。
3. 後述の「セットアップ手順」でゲームエリアの座標を設定する。
4. ロビーに参加・退出用の看板を設置する（後述）。
5. 設定を変更したら `/fpb reload` で再読み込みする。

## セットアップ手順

ゲームに使うワールドを用意し、OP権限で以下のコマンドを **設定したい場所に立って** 実行します（実行位置・向きが座標として保存されます）。

```text title="初期リスポーン地点（退出・終了時の戻り先）"
/fpb setstartspawn
```

```text title="待機ロビー地点"
/fpb setlobby
```

```text title="ゲームエリアの角1"
/fpb setfield 1
```

```text title="ゲームエリアの角2"
/fpb setfield 2
```

```text title="高ポイントゾーン（任意）"
/fpb setfishspot
```

```text title="視線先の看板を参加看板として登録"
/fpb setsign join
```

```text title="視線先の看板を退出看板として登録"
/fpb setsign leave
```

```text title="視線先の看板を開始看板として登録"
/fpb setsign start
```

```text title="視線先の看板の登録を解除（看板を見ながら実行）"
/fpb setsign delete
```

!!! note "開始看板（start）も登録できます"
    現バージョンの `setsign` は **join / leave / start の3種類** に対応します。`/fpb setsign start` で開始看板を登録すると、その看板を右クリックするだけでゲームを開始できます。登録を解除したいときは、対象の看板を見ながら `/fpb setsign delete` を実行してください（join / leave / start いずれの登録も解除できます）。

!!! warning "開始看板のクリックは OP（`fpb.admin`）のみ"
    `/fpb setsign start` で登録した **開始看板を右クリックして開始できるのは `fpb.admin` 権限を持つプレイヤーだけ** です。一方、コマンドの `/fpb start [分]` は権限不要（全員可）です。一般プレイヤーにも開始させたい場合はコマンド導線を案内してください。

!!! note "必須の座標と任意の座標"
    初期リスポーン（`setstartspawn`）・ロビー（`setlobby`）・ゲームエリアの角1/2（`setfield 1`・`setfield 2`）の4点はゲーム開始に **必須** です。すべて設定されていないと `/fpb start` を実行できません。`fishspot`（高ポイントゾーン）は **任意** で、未設定でもゲームは開始できます。設定した座標は config.yml の `locations` セクションに自動保存されます。

!!! tip "看板での参加導線を設置"
    プレイヤーの参加・退出・開始は看板で行えます。看板を設置し、**看板を見ながら（5ブロック以内）** `/fpb setsign join`（参加看板）・`/fpb setsign leave`（退出看板）・`/fpb setsign start`（開始看板）を実行して登録してください。

    - 看板テキストはプラグインが自動で書き込みます（1行目 `[釣り大会]`、2行目 `▶クリックで参加`／`▶クリックで退出`／`▶クリックで開始`）。
    - 位置は config.yml の `signs.join` / `signs.leave` / `signs.start` に保存され、再起動後も有効です。
    - クリック判定は **登録された位置との一致** で行われるため、手書きで同じ文字を書いた看板は機能しません（手書き登録は廃止）。
    - 登録を解除するには、対象の看板を見ながら `/fpb setsign delete` を実行します。
    - 登録状況は `/fpb status` の「参加看板／退出看板／開始看板」欄で確認できます。

    参加看板を使うには `fpb.play` 権限（既定で全員に付与）が必要です。開始看板のクリックには `fpb.admin` 権限が必要です。

## config.yml 設定項目

### ゲーム基本設定（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `default-duration` | 5 | デフォルト制限時間（分） |
| `min-players` | 1 | 開始に必要な最小参加人数（既定 `1` で一人開催が可能） |
| `countdown-seconds` | 5 | 開始前カウントダウン（秒） |

### コンボ設定（`combo`）

| キー | 既定値 | 説明 |
|---|---|---|
| `timeout-seconds` | 10 | コンボ継続判定時間（秒）。前回の釣りからこの時間内に釣るとコンボ継続 |
| `multipliers` | 下記 | コンボ数ごとのポイント倍率 |

コンボ倍率（`combo.multipliers`）の既定値:

| コンボ数 | 倍率 |
|---|---|
| 3 | 1.2 |
| 5 | 1.5 |
| 7 | 1.8 |
| 10 | 2.0 |

### ゴールデンタイム設定（`golden-time`）

| キー | 既定値 | 説明 |
|---|---|---|
| `enabled` | true | ゴールデンタイムを有効化するか |
| `duration-seconds` | 30 | 持続時間（秒） |
| `multiplier` | 2.0 | ポイント倍率 |

!!! note "ゴールデンタイムの発生タイミング"
    ゴールデンタイムは1試合に一度だけ、ゲーム中盤（開始30秒後〜終了60秒前の範囲）でランダムに発生します。発生時刻を固定する設定はありません。

### 釣りスポット設定（`fish-spot`）

| キー | 既定値 | 説明 |
|---|---|---|
| `radius` | 5 | 高ポイントゾーンの半径（ブロック） |
| `bonus-multiplier` | 1.5 | ゾーン内で釣ったときのポイント倍率 |

### 基本ポイント設定（`points`）

釣れたアイテムごとの基本ポイントです。ここに無いアイテムはポイント0（ゴミ扱い）になります。

| キー | 既定値 |
|---|---|
| `COD`（タラ） | 1 |
| `SALMON`（サケ） | 2 |
| `PUFFERFISH`（フグ） | 3 |
| `TROPICAL_FISH`（熱帯魚） | 5 |
| `HEART_OF_THE_SEA`（海洋の心） | 15 |
| `ENCHANTED_BOOK`（エンチャント本） | 15 |

### レアアイテム設定（`rare-items`）

`rare-items` に列挙したアイテムは、釣り上げると全体アナウンスされます。既定値は `HEART_OF_THE_SEA` と `ENCHANTED_BOOK`。

### 特殊アイテムドロップ率（`special-items`）

ゴミを釣ったときに特殊アイテムが出る確率（%）です。すべての値の合計が、特殊アイテムが出る全体確率になります。

| キー | 既定値（%） | アイテム |
|---|---|---|
| `ink-bomb` | 3 | イカスミ爆弾 |
| `freeze-ball` | 3 | 凍結弾 |
| `line-cutter` | 2 | 釣り糸カッター |
| `lucky-bait` | 2 | 幸運の餌 |
| `speed-bait` | 3 | 加速エサ |
| `trap-float` | 2 | トラップ浮き |
| `fish-beacon` | 2 | 魚寄せビーコン |

!!! note "魚を釣ったときの追加ドロップ"
    上記のドロップ率はゴミを釣ったときの判定に使われます。これとは別に、魚を釣ったときも一定確率で特殊アイテムが追加で手に入ります。その確率は下記 `special-items-bonus.on-catch-chance` で変更できます。

### 魚を釣ったときの追加ドロップ確率（`special-items-bonus`）

| キー | 既定値 | 説明 |
|---|---|---|
| `on-catch-chance` | 5 | 魚を釣るたびに、追加で特殊アイテム抽選を行う確率（%） |

!!! tip "追加ドロップと通常ドロップの違い"
    - **通常ドロップ**（`special-items` セクション）: ゴミを釣ったときの当選確率。魚自体は手に入りません。
    - **追加ドロップ**（`special-items-bonus.on-catch-chance`）: 魚（ポイント加算対象）を釣ったときに、まずポイントが入り、その上で抽選に当たれば追加で特殊アイテムも手に入ります。
    - どちらの抽選も、当たった後にどの特殊アイテムが出るかは `special-items` セクションの比率に従います。

### 特殊アイテム効果設定（`item-effects`）

| キー | 既定値 | 説明 |
|---|---|---|
| `ink-bomb-duration` | 3 | イカスミ爆弾の盲目時間（秒） |
| `freeze-duration` | 2 | 凍結弾の効果時間（秒） |
| `speed-bait-duration` | 30 | 加速エサの効果時間（秒） |
| `speed-bait-luck-level` | 2 | 加速エサで付与するLUCKレベル |
| `trap-duration` | 60 | トラップ浮きの設置持続時間（秒） |
| `beacon-duration` | 45 | 魚寄せビーコンの設置持続時間（秒） |
| `beacon-bonus` | 1.3 | 魚寄せビーコンのポイントボーナス倍率 |
| `beacon-radius` | 3.0 | 魚寄せビーコンの効果半径（ブロック）。小数指定可 |

!!! note "魚寄せビーコンのアイテム説明文"
    配布される「魚寄せビーコン」アイテムの説明文（lore）に表示される効果半径・ボーナス倍率・設置時間は、`item-effects` の `beacon-radius` / `beacon-bonus` / `beacon-duration` の値から動的に生成されます（半径は整数に丸めて表示）。config を変更すれば説明文にも反映されます。

### 報酬設定（`rewards`）

ゲーム終了時に上位3名へ配る報酬です。それぞれ `material`（アイテム種別）・`amount`（個数）・`name`（表示名）を設定します。

| 順位 | 既定 material | 既定 amount |
|---|---|---|
| `first-place`（優勝） | EMERALD | 3 |
| `second-place`（準優勝） | EMERALD | 2 |
| `third-place`（3位） | EMERALD | 1 |

### 専用釣り竿設定（`fishing-rod`）

| キー | 説明 |
|---|---|
| `name` | 配布される大会用釣り竿の表示名 |
| `lore` | 釣り竿の説明文（複数行） |

### その他のセクション

- `locations` … エリア座標の保存先。`/fpb setstartspawn` / `setlobby` / `setfield` / `setfishspot` で自動更新されるため **手動編集は非推奨** です。
- `messages` … 各種ゲーム内メッセージとプレフィックス。文言を変更できます。

!!! note "設定変更後は必ず `/fpb reload`"
    `config.yml` を編集したら `/fpb reload` を実行してください。ポイント配点・倍率・メッセージなどの変更が反映されます。

## コマンド

`/fpb` コマンド自体はすべてのプレイヤーが実行でき、サブコマンドごとに権限が分かれています。地点・看板の設定系はプレイヤー（コンソール不可）として実行する必要があります。

### プレイヤー用（全員可）

| コマンド | 説明 |
|---|---|
| `/fpb join` / `leave` | ロビーに参加 / 退出する（看板と同等。join は `fpb.play`＝既定全員） |
| `/fpb start [分]` | ゲームを開始（最小人数を満たせば誰でも可。分を省略すると `default-duration`、1〜60分で指定可） |
| `/fpb help` / `status` | ヘルプ表示 / 現在の状態・参加人数・座標設定状況を表示 |

### 管理用（`fpb.admin`）

| コマンド | 説明 |
|---|---|
| `/fpb setstartspawn` | 初期リスポーン地点を設定 |
| `/fpb setlobby` | 待機ロビー地点を設定 |
| `/fpb setfield <1\|2>` | ゲームエリアの角を設定 |
| `/fpb setfishspot` | 高ポイントゾーンを設定（任意） |
| `/fpb setsign <join\|leave\|start>` | 視線先の看板を参加／退出／開始看板として登録（テキストは自動書き込み） |
| `/fpb setsign delete` | 視線先の看板の登録を解除 |
| `/fpb stop` | ゲームを強制終了 |
| `/fpb reload` | config.yml を再読み込み |

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `fpb.admin` | OP | `setstartspawn` / `setlobby` / `setfield` / `setfishspot` / `setsign` / `stop` / `reload` の使用、**開始看板のクリック** |
| `fpb.play` | 全員 | ゲームに参加できる（参加看板・`/fpb join`） |

!!! note "コマンド権限の設計"
    `/fpb` コマンド自体に権限制限はなく、サブコマンドごとに権限チェックします。**`join`・`leave`・`start`・`help`・`status` は権限不要（全員可）** で、参加・退出・開始は看板でもコマンドでも行えます。会場設定・`stop`・`reload` のみ `fpb.admin` が必要です。`start` には管理者権限は不要な点に注意してください。

## ゲームの運営

1. プレイヤーがロビー看板から参加し、`min-players`（既定1人）以上集まるのを待つ。
2. `/fpb status` で参加人数と座標設定を確認する。
3. `/fpb start` または `/fpb start <分>` でゲームを開始する。
4. 異常時は `/fpb stop` で強制終了する。

## トラブルシューティング

??? failure "`/fpb start` でゲームが開始できない"
    必須座標（初期リスポーン・ロビー・エリア角1/2）が未設定の可能性があります。`/fpb status` で各座標の設定状況を確認し、未設定の項目を `/fpb setstartspawn` / `setlobby` / `setfield 1` / `setfield 2` で設定してください。また参加者が `min-players` 未満の場合も開始できません。

??? failure "プレイヤーが看板でゲームに参加できない"
    看板が `/fpb setsign join` で登録されているか確認してください（`/fpb status` の「参加看板」欄で確認可能）。手書きで文字を書いただけの看板は機能しません。また、ロビー座標（`/fpb setlobby`）が設定されていないと参加できません。プレイヤーに `fpb.play` 権限があるかも確認してください。

??? failure "ポイントが入らない・倍率が反映されない"
    `points` セクションに無いアイテムはポイント0になります。配点を変更したい場合は `config.yml` を編集し `/fpb reload` を実行してください。釣りスポット倍率は `fishspot` が設定済みで、その半径内で釣った場合のみ適用されます。

??? failure "config.yml の変更が反映されない"
    編集後に `/fpb reload` を実行していない可能性があります。リロードを実行してください。なお `locations` セクションは設定コマンド（`setstartspawn` / `setlobby` / `setfield` / `setfishspot`）で管理されるため手動編集は推奨しません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← FishingPointBattle 概要へ](index.md){ .md-button }
