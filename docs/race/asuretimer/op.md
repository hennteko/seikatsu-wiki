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
| `default-course` | `"1000m"` | コースIDを省略したとき（看板・コマンド）に使われる既定コース |
| `game.result-seconds` | 10 | クリア後、結果発表（ランキング表示）を見せてからロビーへ戻すまでの秒数。0で即時 |
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
    本プラグインは起動時に共通の **ConfigUpdater** で `config.yml` を最新版へ自動更新します。旧バージョンから更新した場合も、`return-item` などの不足ブロックや新しい説明コメントが自動追記され、サーバー側で設定済みの値・看板（`signs`）・地点データ（`courses` / `default-spawn` / `lobby-spawn`）はそのまま保持されます。更新時は旧ファイルが `config.yml.bak-<日時>` に退避されます（最新5件まで）。手で編集したあとは `/asure reload` で再読み込みしてください。

### ソロモード設定（`solo`）

`/asure solo` またはソロ看板でソロロビーへ入り、メニューからコースを選んで1人で走れる機能です。

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | `true` | `false` でソロを無効化（開始看板からの走りは従来どおり） |
| `solo.menu-delay` | 3 | ゴール・ギブアップでソロロビーへ戻ってからソロメニューを開くまでの秒数 |
| `solo.countdown-seconds` | 3 | ソロ開始前のカウントダウン（秒。この間は動けない。0でなし） |
| `solo.broadcast-top` | `true` | 自己ベストでコース1位を更新したらサーバー全体に告知する |

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
    `signs` は `/asure setsign` で登録した看板の保存先で、ConfigUpdater の対象外（手動編集は基本不要）です。各エントリは `world/x/y/z`・`kind`（`start`/`goal`/`solo`/`ranking`）・`course`（solo は省略可＝メニュー）・`rank-type`（`ranking` のみ `time`/`distance`）を持ちます。

## コース・看板のセットアップ手順

OP権限を持った状態で、コース付近に立ってコマンドを実行します（実行位置が座標として保存されます）。

### 1. コースを登録する

```text title="コースを新規登録する"
/asure course create <コースID> <asure|dropper> [距離]
```

例: `/asure course create 1000m asure 1000`、`/asure course create dropper1 dropper`。距離を省略すると 1000 が使われます。

### 2. スポーン地点・スタート地点を設定する

設定したい位置に立って実行します。地点は3種類あります。

```text title="初期スポーン（途中抜け・切断時の戻り先）を現在地に設定"
/asure setstartspawn
```

```text title="ロビー（クリア・ギブアップ後の戻り先）を現在地に設定"
/asure setlobby [コースID]
```

```text title="スタート地点（開始時のTP先・距離計測の基準）を現在地に設定"
/asure setspawn [コースID]
```

コースIDを省略すると `default-course` が対象になります（`setstartspawn` はコース非依存の全体設定です）。

!!! note "地点の役割の違い"
    - **初期スポーン**（`setstartspawn`）… `/asure leave` や切断時の戻り先。全コース共通の1か所。config の `default-spawn` に保存されます。未設定の場合は最初のコースのロビー、それも無ければワールドスポーンへフォールバックします。
    - **ロビー**（`setlobby`）… クリア・ギブアップ後の戻り先。config の `courses.<ID>.spawn` に保存されます。
    - **スタート地点**（`setspawn`）… 開始時のテレポート先で、アスレの距離計測の基準（Z座標）にもなります。config の `courses.<ID>.start` に保存されます。

### 3. 看板を設置する

看板は **コマンドで登録** します。設置したい看板に視線を向けた状態（5ブロック以内）で、対応する `setsign` コマンドを実行すると、その看板が機能看板として登録され文面が自動整形されます。**看板に手書きしても登録されません**（旧仕様の手書き登録は廃止されました）。

```text title="開始看板を視線先の看板に登録（コースID省略時は default-course）"
/asure setsign start [コースID]
```

```text title="ゴール看板を視線先の看板に登録（コースID省略時は default-course）"
/asure setsign goal [コースID]
```

```text title="ランキング看板を視線先の看板に登録（種別は time または distance、両方とも必須）"
/asure setsign ranking <コースID> <time|distance>
```

```text title="ソロ看板を視線先の看板に登録（コース省略＝ソロメニュー、コース指定＝そのコースをソロで即開始）"
/asure setsign solo [コースID]
```

```text title="視線先の登録看板を解除する"
/asure setsign delete
```

!!! note "看板の仕様"
    - 看板の種別（アスレ／ドロッパー）は **コースの種別** で自動判定されます。看板側で `[asure]`/`[dropper]` を書き分ける必要はありません。
    - `start` / `goal` はコースIDを省略すると `default-course` が対象になります。`ranking` はコースIDと種別（`time`/`distance`）の両方が必須です。
    - `solo` はコースIDを省略するとソロメニューを開く看板に、コースIDを指定するとそのコースをソロで即開始する看板になります。
    - 看板の登録・解除には `asuretimer.admin` 権限が必要です。
    - 登録に成功するとプラグインが看板の文面を自動描画します（アスレは `[AsureTimer]` `>> START >>` `* GOAL *`、ドロッパーは `[Dropper]` `v START v` `* CLEAR *`、ランキングは `[Ranking]`）。
    - 登録看板を **破壊すると登録も自動解除** されます。
    - ランキング看板は `sign-update-interval`（既定30秒）ごとに自動更新され、表面に1位プレイヤー名、裏面にベスト記録が表示されます。

## 管理ツール（OP専用）
`/asure wand` で管理ツール（ブレイズロッド）を受け取り、右クリック（統合版はタップ/長押し）で管理メニューを開きます。`/asure menu` でも開けます。

- コース：メニュー左上のボタンで設定するコースを切り替え（コースごとの地点・距離・種類はこのコースに設定されます）
- 数値：＋/−ボタンで 結果発表の時間・ソロのカウントダウン・ソロメニューを開くまでの秒数・区間の間隔（アスレ）・ランキング表示件数・コースの距離（アスレのみ）を変更（即保存）
- 地点：ボタンを押すと立ち位置に設定（初期スポーン／ソロロビー／コースのロビー／コースのスタート）
- コース管理：種類（アスレ⇔ドロッパー）の切り替え、コースの削除（5秒以内に2回押し）
- 看板：看板を見ながら（5ブロック以内）押すと開始／ゴール／ソロ（メニュー・コース指定）／ランキング（タイム・到達距離）看板を登録。コースが2つ以上あるときはコースを選ぶ画面が出ます。ハサミで登録解除
- 運用：開始（選択中のコースを自分で走るテスト）・設定状況（`/asure status` と同じ表示）・リロード・強制停止（強制停止は5秒以内に2回押し）
- アスレタイマーに参加中（走行中・結果発表中・ソロロビー）は受け取れません（`/asure leave` で離脱してから実行）。捨てると消えるので再度 `/asure wand` で取得してください
- 統合版：チェストGUIで操作できます。数値は左右クリックに頼らず＋/−の別ボタンで操作します

!!! note "管理ツールの仕様"
    - 管理ツールは PDC（`asuretimer:admin_wand`）で判定します。同じ名前のブレイズロッドを作っても使えません。
    - 受け取るとき・使うときの両方で `asuretimer.admin` を確認します。OP 以外が使おうとするとアイテムが消えます。
    - 額縁・防具立て・チェストなどには入れられません。ホッパーや OP 以外が拾おうとすると消えます。
    - 管理ツールを持って登録看板を右クリックしても、看板の動作（開始・ゴールなど）はせず管理メニューが開きます。
    - メニューの「開始」で走った場合、管理ツールは他の持ち物と一緒に退避され、走り終わると戻ります。

## 管理コマンド

メインコマンドは `/asure` です。

| コマンド | 説明 |
|---|---|
| `/asure start [コースID] [プレイヤー]` | スタート看板クリックと同じ処理でコースに参加・計測開始。自分の開始は `asuretimer.play`、他プレイヤー指定は `asuretimer.admin` が必要（`/asure join` も同じ） |
| `/asure leave [プレイヤー]` | 計測を中止して離脱（記録なし・初期スポーンへ）。他プレイヤー指定は `asuretimer.admin` 必要 |
| `/asure giveup` | ギブアップ（ギブアップアイテムと同じ。走行中のみ・誰でも実行可） |
| `/asure back` | 通過済みチェックポイントの選択GUIを開く（戻るアイテムと同じ。アスレ走行中のみ・誰でも実行可） |
| `/asure solo [コースID] [best\|top\|last\|off]` | ソロ（ソロロビーへ入ってメニュー／コース指定で即開始）。誰でも実行可（`asuretimer.play`） |
| `/asure retry` | 今の走りを記録なしで止め、同じコースを最初からやり直す（誰でも実行可） |
| `/asure ghost <best\|top\|last\|off>` | 自分のゴーストの種類を変更（誰でも実行可） |
| `/asure ghost reset <コースID> [プレイヤー]` | ゴースト・区間・前回の記録を消す（ランキングは消さない。`asuretimer.admin` 必要） |
| `/asure stop` | 走っている全員を強制終了（記録なし）。ソロ在籍者はソロロビーへ戻す（`asuretimer.admin` 必要） |
| `/asure status [コースID]` | 地点設定・看板登録数・現在のプレイ人数など設定状況を表示（誰でも実行可） |
| `/asure lobbyfix <プレイヤー> [force]` | 参加前の持ち物が戻らないときの復旧。`force` で退避データを破棄しロック解除（`asuretimer.admin` 必要） |
| `/asure wand` | 管理ツール（ブレイズロッド）を受け取る。右クリック/タップで管理メニュー（参加中は不可） |
| `/asure menu` | 管理メニューを開く（参加中でも可） |
| `/asure setstartspawn` | 現在地を初期スポーン（離脱・切断時の戻り先）に設定 |
| `/asure setlobby [コースID]` | 現在地をロビー（クリア・ギブアップ後の戻り先）に設定 |
| `/asure setspawn [コースID]` | 現在地をスタート地点（開始時のTP先・距離計測基準）に設定 |
| `/asure setsign <start\|goal\|solo\|ranking\|delete> ...` | 視線先の看板を機能看板として登録／解除 |
| `/asure course list` | 登録済みコースの一覧（種別・距離・設定状況）を表示 |
| `/asure course create <ID> <asure\|dropper> [距離]` | コースを新規作成 |
| `/asure course settype <ID> <asure\|dropper>` | コースの種別を変更 |
| `/asure course delete <ID>` | コースを削除 |
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
    - `start`／`leave` で **他プレイヤーを指定** した場合 … `asuretimer.admin` を実装側でチェック
    - `stats <他プレイヤー>` … `asuretimer.admin` を実装側でチェック
    - `start`（自分） / `solo` … `asuretimer.play` を実装側でチェック（既定全員可。`join` も同じ）
    - `leave`（自分） / `giveup` / `back` / `retry` / `ghost`（種類変更） / `status` / `stats`（自分） / `stats <コースID>`（自分） … 権限チェックなし（誰でも実行可）
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
