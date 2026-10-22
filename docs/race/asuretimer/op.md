<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# AsureTimer ― OP・運営ガイド { .page-op #asuretimer-op }

AsureTimer の導入・config.yml・コース／看板のセットアップ・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | AsureTimer |
| バージョン | `${project.version}`（ビルド時に決定） |
| api-version | 26.1.2 |
| メインコマンド | `/asure` |
| 依存プラグイン | なし |
| データベース | SQLite（`plugins/AsureTimer/data.db`） |
| 設定ファイル | `plugins/AsureTimer/config.yml` |

!!! info "プラグインの概要"
    AsureTimer は単独のプラグインです。アスレチック（`asure`）コースとドロッパー（`dropper`）コースのタイム計測・ランキング機能を提供します。コースの開始・ゴール・ランキング表示は **看板** で行い、記録は SQLite データベースに保存されます。

## 導入手順

1. ビルドした AsureTimer の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/AsureTimer/` に `config.yml` が自動生成され、`data.db`（SQLite）が作成される。
3. アスレ／ドロッパー用のコースワールドを用意する。
4. 後述の手順でコースを登録し、スポーン地点・スタート地点・看板を設置する。
5. 設定を変更したら `/asure reload` で再読み込みする。

## config.yml 設定項目

### 全体設定

| キー | 既定値 | 説明 |
|---|---|---|
| `default-course` | `"1000m"` | `/asure ranking` でステージ名を省略したときのステージ（join / start / solo などはステージが1つなら省略可、2つ以上なら指定が必要） |
| `game.result-seconds` | 10 | マルチでゴール後、結果発表（ランキング表示）を見せてからロビーへ戻すまでの秒数。0で即時 |
| `game.countdown-seconds` | 3 | マルチの一斉スタート・途中参加の前のカウントダウン（秒。この間は動けない。0でなし） |
| `game.broadcast-result` | `true` | 試合が終わったら上位3人をサーバー全体に告知する |
| `actionbar-interval` | 5 | 計測中アクションバーの更新間隔（tick／20=1秒） |
| `sign-update-interval` | 30 | ランキング看板の自動更新間隔（秒） |
| `ranking-display-count` | 10 | チャット表示時のランキング件数（`/asure ranking`・クリア／ギブアップ後の表示に使用。コンソール実行時のみ10件固定） |

### コース設定（`courses`）

`courses.<コースID>` 以下に各コースを定義します。`spawn` / `start` 地点はコマンド（後述）で設定するのが簡単です。

| キー | 既定値 | 説明 |
|---|---|---|
| `courses.<ID>.type` | `asure` | コース種別。`asure`（アスレ）または `dropper`（ドロッパー） |
| `courses.<ID>.distance` | 1000 | アスレのゴール距離（m）。アクションバーの分母に使用。ドロッパーは `0` でよい |
| `courses.<ID>.spawn` | - | ロビー（スポーン）地点。クリア・ギブアップ後のテレポート先 |
| `courses.<ID>.start` | - | スタート地点。看板クリック時のテレポート先・距離計測の基準 |

`spawn` / `start` はそれぞれ `world` / `x` / `y` / `z` / `yaw` / `pitch` を持つ座標ブロックです。

### メッセージ設定（`messages`）

`messages` 以下でチャットメッセージを `&` カラーコード付きで編集できます。アスレ用（`start` / `giveup` / `clear` / `checkpoint` / `death-respawn` / `ranking-header` 等）とドロッパー用（`dropper-start` / `dropper-clear` / `dropper-giveup` / `dropper-ranking-header`）に加え、**戻るアイテム用（`return-teleport` / `return-no-checkpoint` / `return-menu-start` / `return-menu-entry`）** が用意されています。`{time}` `{distance}` `{rank}` `{player}` `{course}` `{index}` などのプレースホルダーが使えます。

### ギブアップアイテム設定（`giveup-item`）

| キー | 既定値 | 説明 |
|---|---|---|
| `giveup-item.material` | `NETHER_STAR` | ギブアップアイテムの素材（`Material` 名で指定。不正値は警告を出し `NETHER_STAR` にフォールバック） |
| `giveup-item.name` | `&c&lギブアップ` | 表示名（`&` カラーコード対応、イタリック装飾は自動でオフ） |
| `giveup-item.lore` | （2行） | アイテムの説明文（lore） |

!!! info "ギブアップアイテムの判定について"
    ギブアップ判定は **PersistentDataContainer（PDC）に専用タグが付いているか** で行われます。プラグインが配布したアイテムにだけ反応するため、同じ素材・同じ名前の通常アイテムを持っていても誤作動しません。`giveup-item.material` を別の素材（例: `BARRIER`）に変えても、配布されたアイテムが正しくギブアップアイテムとして機能します。素材変更後は `/asure reload` を実行してください（既存セッション中のアイテムは古い素材のままです）。

### 戻るアイテム設定（`return-item`）

アスレ計測中に配られる、通過済みチェックポイントへワープできるアイテムの設定です（**ドロッパーでは配られません**）。右クリックで通過済みCPの選択GUIが開き、選んだCPへワープします（消費されず、タイム・距離記録も減りません）。判定はギブアップ同様にPDCタグで行うため、同素材の通常アイテムには反応しません。

| キー | 既定値 | 説明 |
|---|---|---|
| `return-item.material` | `RECOVERY_COMPASS` | 戻るアイテムの素材（不正値は警告を出し `RECOVERY_COMPASS` にフォールバック） |
| `return-item.name` | `&b&l前のチェックポイントに戻る` | 表示名（`&` カラーコード対応） |
| `return-item.menu-title` | `&8チェックポイント選択` | 選択GUIのタイトル |
| `return-item.lore` | （2行） | アイテムの説明文（lore） |

!!! note "config.yml は起動時に自動で最新化されます"
    本プラグインは起動時に共通の **ConfigUpdater** で `config.yml` を最新版へ自動更新します。旧バージョンから更新した場合も、`return-item` などの不足ブロックや新しい説明コメントが自動追記され、サーバー側で設定済みの値・看板（`signs`）・地点データ（`courses` / `default-spawn`）はそのまま保持されます。更新時は旧ファイルが `config.yml.bak-<日時>` に退避されます（最新5件まで）。手で編集したあとは `/asure reload` で再読み込みしてください。

### ソロモード設定（`solo`）

`/asure solo <ステージ>` またはソロ看板で、1人ですぐ走り始める機能です。ゴールすると自動で同じステージを再スタートし、`/asure leave` かギブアップで抜けます。

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | `true` | `false` でソロを無効化（マルチはそのまま） |
| `solo.restart-seconds` | 5 | ゴールしてから自動で再スタートするまでの秒数（この間に結果を表示） |
| `solo.countdown-seconds` | 3 | ソロ開始前のカウントダウン（秒。この間は動けない。0でなし） |
| `solo.broadcast-top` | `true` | 自己ベストでステージ1位を更新したらサーバー全体に告知する（マルチ・ソロ共通） |

!!! note "旧設定からの移行"
    以前の `solo.menu-delay`（ソロメニューを開くまでの秒数）は、起動時に `solo.restart-seconds` へ値を引き継いで削除されます。ソロロビー地点（`lobby-spawn`）は使わなくなったため削除されます（マルチのロビーはステージごとの `/asure setlobby <ステージ>`）。どちらもコンソールにログが出ます。

### 区間タイム設定（`splits`）

| キー | 既定値 | 説明 |
|---|---|---|
| `splits.enabled` | `true` | `false` で区間表示・区間表・BossBar を出さない |
| `splits.asure-every` | 100 | アスレで区間を区切る間隔（m）。0で区切らない。変えると過去の区間・ゴーストは比較に使えなくなる |
| `splits.bossbar` | `true` | 走行中に BossBar（経過／比べる相手のタイム）を表示する |

### ゴースト設定（`ghost`）

自己ベスト・1位・前回の走りを、本人にだけ見える姿で再生します。保存先は `plugins/AsureTimer/ghosts/<コース>/`。

| キー | 既定値 | 説明 |
|---|---|---|
| `ghost.enabled` | `true` | `false` でゴーストを記録・再生しない |
| `ghost.default` | `best` | 初めて遊ぶ人の既定の種類（`best` / `top` / `last` / `off`） |
| `ghost.sample-ticks` | 2 | 位置を記録する間隔（tick。小さいほど滑らかだがファイルが大きい） |
| `ghost.max-seconds` | 900 | これより長い走りはゴーストを保存しない（秒） |
| `ghost.entity` | `ARMOR_STAND` | Java版の走者に見せる姿（`ARMOR_STAND` / `MANNEQUIN`）。統合版には常に `ARMOR_STAND` |
| `ghost.trail` | `true` | ゴーストの位置に小さなパーティクルを出す |

### ロビー退避・名前表示・統合版（ミニゲーム共通）

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | `true` | 走行中（結果発表待ち含む）の持ち物退避・復元機能。`false` で従来挙動 |
| `lobby-inventory.gamemode` | `ADVENTURE` | 走行中のゲームモード（`ADVENTURE` / `SURVIVAL` / `CREATIVE` / `KEEP`） |
| `lobby-inventory.clear-effects` | `true` | 開始時にポーション効果を消す |
| `lobby-inventory.reset-health` | `true` | 開始時に最大体力を既定へ戻し、体力・満腹度を全回復する |
| `lobby-inventory.reset-exp` | `true` | 開始時に経験値とレベルを0にする |
| `lobby-inventory.expire-days` | 30 | 復元されないまま残った退避データの保持日数 |
| `name-tag.enabled` | `true` | 参加中の頭上・Tab・チャット名を「【ゲーム名】名前」にする |
| `name-tag.display-name` | `アスレタイマー` | 【】に入れる正式名 |
| `name-tag.color` | `GOLD` | 【ゲーム名】の色（`GOLD` / `AQUA` / `GREEN` など） |
| `name-tag.announce-join` | `true` | `false` でサーバー全体への参加通知を出さない |
| `bedrock.wax-signs` | `true` | 登録看板を蝋引きし、統合版のタップで編集画面が開かないようにする |
| `bedrock.swing-to-use` | `true` | 統合版の空中タップ（腕振り）でギブアップ／戻るアイテムを使えるようにする（ギブアップは3秒以内に2回で確定） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

!!! note "`signs` はデータ置き場です"
    `signs` は `/asure setsign` で登録した看板の保存先で、ConfigUpdater の対象外（手動編集は基本不要）です。各エントリは `world/x/y/z`・`kind`（`join`/`leave`/`start`/`goal`/`solo`/`ranking`）・`course`（`leave` 以外は必須）・`rank-type`（`ranking` のみ `time`/`distance`）を持ちます。

## コース・看板のセットアップ手順

OP権限を持った状態で、コース付近に立ってコマンドを実行します（実行位置が座標として保存されます）。

### 1. コースを登録する

```text title="コースを新規登録する"
/asure course create <コースID> <asure|dropper> [距離]
```

例: `/asure course create 1000m asure 1000`、`/asure course create dropper1 dropper`。距離を省略すると 1000 が使われます。

コース名に使えるのは文字・数字・`_`・`-` だけです（32文字まで。`.` や空白は使えません）。管理メニューの「コースを作る」ボタンからも作れます（[管理ツール](#管理ツールop専用) を参照）。

### 2. スポーン地点・スタート地点を設定する

設定したい位置に立って実行します。地点は3種類あります。ステージが1つだけならステージ名は省略できます。

```text title="初期スポーン（離脱・切断時の戻り先）を現在地に設定"
/asure setstartspawn
```

```text title="ステージのロビー（マルチの待機場所・ゴール後の戻り先）を現在地に設定"
/asure setlobby <ステージ>
```

```text title="スタート地点（開始時のTP先・距離計測の基準）を現在地に設定"
/asure setspawn <ステージ>
```

!!! note "地点の役割の違い"
    - **初期スポーン**（`setstartspawn`）… `/asure leave`・ギブアップ・切断時の戻り先。全ステージ共通の1か所。config の `default-spawn` に保存されます。未設定の場合は最初のステージのロビー、それも無ければワールドスポーンへフォールバックします。
    - **ロビー**（`setlobby`）… マルチで `/asure join` したときの待機場所、ゴール後の戻り先。config の `courses.<ID>.spawn` に保存されます。未設定なら初期スポーンを使います。
    - **スタート地点**（`setspawn`）… 開始時のテレポート先で、アスレの距離計測の基準（Z座標）にもなります。config の `courses.<ID>.start` に保存されます。

### 3. 看板を設置する

看板は **コマンドで登録** します。設置したい看板に視線を向けた状態（5ブロック以内）で、対応する `setsign` コマンドを実行すると、その看板が機能看板として登録され文面が自動整形されます。**看板に手書きしても登録されません**。ステージ名は省略できません。

```text title="参加看板（クリックでステージのロビーへ。試合中なら途中参加）"
/asure setsign join <ステージ>
```

```text title="離脱看板（クリックで離脱して初期スポーンへ）"
/asure setsign leave
```

```text title="開始看板（クリックでロビーにいる全員を一斉スタート）"
/asure setsign start <ステージ>
```

```text title="ゴール看板"
/asure setsign goal <ステージ>
```

```text title="ソロ看板（クリックでそのステージをソロですぐ開始）"
/asure setsign solo <ステージ>
```

```text title="ランキング看板（種別は time または distance）"
/asure setsign ranking <ステージ> <time|distance>
```

```text title="視線先の登録看板を解除する"
/asure setsign delete
```

!!! note "看板の仕様"
    - 看板の色（アスレ／ドロッパー）は **ステージの種別** で自動判定されます。
    - 参加看板には待機人数（試合中は走行人数）、開始看板には待機人数か試合の状態が表示され、自動で更新されます。
    - 開始看板は誰でも押せます（ロビーに誰もいない・試合中・スタート地点未設定のときは開始しません）。
    - **以前の開始看板**（すぐ1人で走る看板）は、そのまま **一斉スタートの開始看板** として動きます。1人ですぐ走る看板にしたい場合は `/asure setsign solo <ステージ>` で登録し直してください。
    - **以前のソロ看板（ステージ指定なし＝ソロメニュー）** は使えなくなりました。看板に「ステージ未指定」と表示されるので、`/asure setsign solo <ステージ>` で登録し直してください（`/asure status` に件数が出ます）。
    - 看板の登録・解除には `asuretimer.admin` 権限が必要です。登録看板を **破壊すると登録も自動解除** されます。
    - ランキング看板は `sign-update-interval`（既定30秒）ごとに自動更新され、表面に1位プレイヤー名、裏面にベスト記録が表示されます。

## マルチ・ソロの流れ

| 項目 | マルチ | ソロ |
|---|---|---|
| 参加 | `/asure join <ステージ>`・参加看板 → ステージのロビーへ（持ち物を預かる） | `/asure solo <ステージ>`・ソロ看板 → すぐスタート（持ち物を預かる） |
| スタート | `/asure start <ステージ>`・開始看板・管理メニューで、ロビーの全員をスタート地点へ送り、`game.countdown-seconds` のあと一斉に GO | `solo.countdown-seconds` のあと GO |
| 途中参加 | 試合中の `join` は、自分のカウントダウンのあとすぐ走り出す（タイムは走り出した時点から）。カウントダウン中の `join` は全員と一緒に GO | — |
| ゴール | 記録 → `game.result-seconds` 後にロビーへ戻って待機（在籍は続く） | 記録 → `solo.restart-seconds` 後に自動で再スタート |
| 試合の終了 | 走っている人が0人になったとき（全員ゴール・離脱）。ゴール順をロビーの全員に表示し、`game.broadcast-result` なら上位3人を全体告知 | — |
| 離脱 | `/asure leave`・離脱看板・ギブアップ（アスレは到達距離を記録）→ 持ち物を戻して初期スポーンへ | 同じ |
| 切断 | 離脱扱い（記録なし）。再ログインで初期スポーンへ、試合には戻らない | 同じ |
| 強制停止 | `/asure stop [ステージ]` で走っている人をロビーへ戻す（在籍は続く） | ソロを終えて初期スポーンへ |

## 管理ツール（OP専用）
`/asure wand` で管理ツール（ブレイズロッド）を受け取り、右クリック（統合版はタップ/長押し）で管理メニューを開きます。`/asure menu` でも開けます。

- ステージ：メニュー左上のボタンで設定するステージを切り替え（ステージごとの地点・距離・種類はこのステージに設定されます）
- ステージの作成：「＋ アスレのステージを作る」「＋ ドロッパーのステージを作る」を押すとメニューが閉じるので、チャットにステージ名を入力（60秒以内）。`auto` で自動の名前（asure1・dropper1…）、`cancel` で中止。入力したチャットは全体には流れません。作ったステージが選択中になるので、続けてスタート・ロビー地点と看板を設定します（アスレの距離は 1000m で作成。＋/−で変更）
- 数値：＋/−ボタンで 結果発表の時間・一斉スタートのカウントダウン・ソロのカウントダウン・ソロの再スタートまでの秒数・区間の間隔（アスレ）・ランキング表示件数・ステージの距離（アスレのみ）を変更（即保存）
- 地点：ボタンを押すと立ち位置に設定（初期スポーン／ステージのロビー／ステージのスタート）
- ステージ管理：種類（アスレ⇔ドロッパー）の切り替え、ステージの削除（5秒以内に2回押し。参加中の人がいると削除できません）
- 看板：看板を見ながら（5ブロック以内）押すと参加／離脱／開始（一斉スタート）／ゴール／ソロ／ランキング（タイム・到達距離）看板を登録。ステージが2つ以上あるときはステージを選ぶ画面が出ます。ハサミで登録解除
- 運用：一斉スタート（選択中のステージ。`/asure start` と同じ）・設定状況（`/asure status` と同じ表示）・リロード・強制停止（全ステージ。5秒以内に2回押し）
- アスレタイマーに参加中（マルチのロビー・ソロ・走行中）は受け取れません（`/asure leave` で離脱してから実行）。捨てると消えるので再度 `/asure wand` で取得してください
- 統合版：チェストGUIで操作できます。数値は左右クリックに頼らず＋/−の別ボタンで操作します

!!! note "管理ツールの仕様"
    - 管理ツールは PDC（`asuretimer:admin_wand`）で判定します。同じ名前のブレイズロッドを作っても使えません。
    - 受け取るとき・使うときの両方で `asuretimer.admin` を確認します。OP 以外が使おうとするとアイテムが消えます。
    - 額縁・防具立て・チェストなどには入れられません。ホッパーや OP 以外が拾おうとすると消えます。
    - 管理ツールを持って登録看板を右クリックしても、看板の動作（開始・ゴールなど）はせず管理メニューが開きます。
    - 管理ツールを持ったまま参加した場合、管理ツールは他の持ち物と一緒に預けられ、離脱すると戻ります。

## 管理コマンド

メインコマンドは `/asure` です。

| コマンド | 説明 |
|---|---|
| `/asure join [ステージ] [プレイヤー]` | マルチ：ステージのロビーへ入る（試合中なら途中参加）。自分は `asuretimer.play`、他プレイヤー指定は `asuretimer.admin`（コンソール・コマンドブロック可） |
| `/asure start [ステージ]` | マルチ：ロビーにいる全員を一斉スタート（誰でも実行可。コンソール・コマンドブロック可） |
| `/asure leave [プレイヤー]` | 離脱（走行中なら記録なしで中断・持ち物を戻して初期スポーンへ）。他プレイヤー指定は `asuretimer.admin` 必要 |
| `/asure giveup` | ギブアップして離脱（ギブアップアイテムと同じ。アスレは到達距離を記録。走行中のみ・誰でも実行可） |
| `/asure back` | 通過済みチェックポイントの選択GUIを開く（戻るアイテムと同じ。アスレ走行中のみ・誰でも実行可） |
| `/asure solo [ステージ] [best\|top\|last\|off]` | ソロ：すぐスタート（ゴール後は自動で再スタート）。誰でも実行可（`asuretimer.play`） |
| `/asure retry` | 今の走りを記録なしで止め、同じコースを最初からやり直す（誰でも実行可） |
| `/asure ghost <best\|top\|last\|off>` | 自分のゴーストの種類を変更（誰でも実行可） |
| `/asure splits <title\|chat\|off>` | 自分の区間タイムの出し方を変更（誰でも実行可） |
| `/asure ghost reset <コースID> [プレイヤー]` | ゴースト・区間・前回の記録を消す（ランキングは消さない。`asuretimer.admin` 必要） |
| `/asure stop [ステージ]` | 試合を強制停止（記録なし。省略で全ステージ）。マルチは走っている人をロビーへ戻し（在籍は続く）、ソロは終了して初期スポーンへ（`asuretimer.admin` 必要） |
| `/asure status [コースID]` | 地点設定・看板登録数・現在のプレイ人数など設定状況を表示（誰でも実行可） |
| `/asure lobbyfix <プレイヤー> [force]` | 参加前の持ち物が戻らないときの復旧。`force` で退避データを破棄しロック解除（`asuretimer.admin` 必要） |
| `/asure wand` | 管理ツール（ブレイズロッド）を受け取る。右クリック/タップで管理メニュー（参加中は不可） |
| `/asure menu` | 管理メニューを開く（参加中でも可） |
| `/asure setstartspawn` | 現在地を初期スポーン（離脱・切断時の戻り先）に設定 |
| `/asure setlobby <ステージ>` | 現在地をステージのロビー（マルチの待機場所・ゴール後の戻り先）に設定 |
| `/asure setspawn <ステージ>` | 現在地をスタート地点（開始時のTP先・距離計測基準）に設定 |
| `/asure setsign <join\|leave\|start\|goal\|solo\|ranking\|delete> ...` | 視線先の看板を機能看板として登録／解除 |
| `/asure course list` | 登録済みコースの一覧（種別・距離・設定状況）を表示 |
| `/asure course create <ID> <asure\|dropper> [距離]` | コースを新規作成 |
| `/asure course settype <ID> <asure\|dropper>` | コースの種別を変更 |
| `/asure course delete <ID>` | コースを削除（参加中の人がいるときは削除できません） |
| `/asure ranking [コースID] [time\|distance]` | ランキングを表示 |
| `/asure reload` | config.yml・コース定義・看板・ギブアップ／戻るアイテム素材を再読み込み |
| `/asure stats` | 自分の全コース統計を表示 |
| `/asure stats <プレイヤー>` | 指定プレイヤーの全コース統計を表示（他人指定は `asuretimer.admin` 必要） |
| `/asure stats <プレイヤー> <コースID>` | 指定プレイヤー・コースの統計を表示 |
| `/asure stats <コースID>` | 自分の指定コース統計を表示（コース名のみ指定の短縮形） |

!!! note "stats コマンドの表示内容"
    `stats` ではコースごとに以下が表示されます。

    - 種別アイコン（`[Asure]` / `[Dropper]`）／コースID
    - 挑戦回数・クリア数・クリア率（%）
    - ベストタイムとタイムランキング順位（記録がある場合）
    - 最大到達距離と距離ランキング順位（アスレのみ、記録がある場合）

    他プレイヤー名はオンライン・オフラインどちらでも検索できます。プラグインの記録テーブルに名前が残っていれば、過去にサーバーへ来たことがあるプレイヤーの統計も参照可能です（大文字小文字は区別しません）。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `asuretimer.admin` | OP | コマンド全般・看板の作成・管理 |
| `asuretimer.play` | 全員 | スタート看板からアスレ／ドロッパーをプレイする |

!!! note "サブコマンドごとの権限ガード"
    `/asure` コマンド自体には plugin.yml で `permission` が指定されていないため、コマンド自体は誰でも叩けます。サブコマンドごとに以下のチェックが入ります。

    - `wand` / `menu` / `setstartspawn` / `setlobby` / `setspawn` / `setsign` / `reload` / `course` / `stop` / `lobbyfix` / `ghost reset` … `asuretimer.admin` を実装側でチェック（管理ツールは使うときにも、管理メニューはボタンを押すときにも確認）
    - `join`／`leave` で **他プレイヤーを指定** した場合 … `asuretimer.admin` を実装側でチェック
    - `stats <他プレイヤー>` … `asuretimer.admin` を実装側でチェック
    - `join`（自分） / `solo` … `asuretimer.play` を実装側でチェック（既定全員可）
    - `start` … 権限チェックなし（誰でも・コンソール・コマンドブロックから実行可）
    - `leave`（自分） / `giveup` / `back` / `retry` / `ghost`（種類変更） / `splits` / `status` / `stats`（自分） / `stats <コースID>`（自分） … 権限チェックなし（誰でも実行可）
    - `ranking` … 権限チェックなし（誰でも実行可）

    そのため一般プレイヤーは `/asure ranking` や `/asure stats`、`/asure status` を実行できます。アクションバー / 看板 / GUI と併用する運用で問題ありません。

## トラブルシューティング

??? failure "看板をクリックしても計測が始まらない"
    看板が AsureTimer の看板として登録されているか確認してください（`/asure setsign start` 等で登録すると文面が自動整形されます。手書きでは登録されません）。また、看板に紐づくコースIDが `courses` に登録され、`start` 地点が設定済みである必要があります。`/asure course list` で `[設定済]`、`/asure status` で看板登録数を確認してください。

??? failure "「コースのスタート地点が未設定です」と表示される"
    対象コースの **スタート地点が未設定** です。スタート地点に立って `/asure setspawn <コースID>` を実行してください。ロビー地点も `/asure setlobby <コースID>` で設定しておくと、クリア・ギブアップ後に正しくロビーへ戻れます。

??? failure "コースが作成・登録できない"
    `/asure course create <ID> <asure|dropper> [距離]` の形式を確認してください。種別は `asure` か `dropper` のみ、距離は数値で指定します。実行には `asuretimer.admin` 権限が必要です。

??? failure "ランキング看板が更新されない / 1位が表示されない"
    ランキング看板はチャンクが読み込まれている範囲のみ `sign-update-interval`（既定30秒）ごとに更新されます。記録が1件も無いコースでは `---` が表示されます。看板1行目が `[Ranking]` に整形されているか確認してください。なおドロッパーコースのランキングは距離概念が無いため常にタイム表示になります。

??? failure "ギブアップアイテムが効かない"
    ギブアップ判定はアイテムの PersistentDataContainer タグで行われます。プラグインが配布したアイテムのみ反応するため、自分でクリエイティブ等で出した同名アイテムは機能しません。スタート看板から再度コースを開始すれば、新しい正規のギブアップアイテムが配布されます。`giveup-item.material` を変更した直後は `/asure reload` を実行し、その後コースを再スタートしてください。

??? failure "チェックポイントが機能しない（アスレ）"
    チェックポイントは **金の感圧板（軽量用感圧板 / LIGHT_WEIGHTED_PRESSURE_PLATE）** を踏むことで記録されます。コース上に金の感圧板を設置してください。なお距離は `start` 地点からの **Z方向の進行** で計算されるため、コースはZ方向に伸びるよう設計すると距離表示が正しく機能します。

??? failure "設定変更が反映されない"
    `config.yml` を編集したら `/asure reload` を実行してください。コース定義・座標・看板登録・ギブアップ／戻るアイテム素材が再読み込みされ、登録済み看板の文面も再描画されます。なお `messages`（チャット文面）は実行時に都度参照されるため即時反映されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← AsureTimer 概要へ](index.md){ .md-button }
