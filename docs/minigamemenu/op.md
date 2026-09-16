<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。使い方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# MiniGameMenu（ミニゲームメニュー）― OP・運営ガイド { .page-op #minigamemenu-op }

MiniGameMenu の導入・`/mgmenu` コマンド・**GUI編集モード（`/mgmenu edit`）**・config（メニューアイテム / GUI / ゲーム定義 / 連携コマンド）・権限をまとめます。ゲームの追加・並び替え・編集は、config を直接いじらずゲーム内GUIから行えます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | MiniGameMenu |
| メインコマンド | `/mgmenu`（エイリアス `/menu`・`/mgm`・`/ミニゲーム`） |
| プレイヤー操作 | 「生活鯖メニュー」の紙を右クリック（`mgmenu.use`、既定全員） |
| 依存プラグイン | なし（連携先の各ミニゲーム／カジノ等が別途必要） |
| 設定ファイル | `plugins/MiniGameMenu/config.yml` |
| api-version | 26.1.2（plugin.yml 表記は `1.21`） |

!!! info "仕組み: コマンド代行方式"
    本プラグインは各ミニゲーム本体を **改修しません**。GUIのボタンを押すと、config に書かれた既存ゲームのコマンド（例: `kakurenbo join`）を **プレイヤー本人**（`executor: player`）または **コンソール**（`executor: console`）として代理実行します。`%player%` はクリックしたプレイヤー名に置換されます。

## 導入手順

1. ビルドした `MiniGameMenu` の jar を `plugins/` に配置する。
2. サーバーを起動すると `plugins/MiniGameMenu/config.yml` が生成される（`saveDefaultConfig()` による初回生成）。
3. 連携先の各ミニゲーム／カジノプラグインが導入され、`join`・`leave`・`start` などのコマンドが動作することを確認する。
4. 必要に応じて config のゲーム定義・連携コマンド・便利ボタンを編集し、`/mgmenu reload` で反映する。

!!! tip "メニューアイテムは自動で配られます"
    既定では入室時にメニューアイテムが自動配布されます（後述の `settings`）。手動で渡したい場合は次のコマンドを使います。

```text title="自分にメニューアイテムを渡す"
/mgmenu give
```
```text title="指定プレイヤーにメニューアイテムを渡す（オンラインのみ）"
/mgmenu give <プレイヤー名>
```

## コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/mgmenu`（または `open`） | `mgmenu.use`（既定全員） | メニュー一覧GUIを開く（プレイヤー専用） |
| `/mgmenu give [プレイヤー]` | `mgmenu.admin`（既定OP） | メニューアイテムを配布。引数なしは自分に配布 |
| `/mgmenu reload` | `mgmenu.admin`（既定OP） | config.yml を再読み込み |
| `/mgmenu edit` | `mgmenu.admin`（既定OP） | **GUI編集モード**を開く（ゲームの追加・並び替え・編集。プレイヤー専用） |
| `/mgmenu help` | なし | 使い方を表示（未知のサブコマンドも help を表示） |

!!! note "引数なし = open"
    `/mgmenu` を引数なしで実行すると `open`（メニューを開く）として扱われます。`open` / `help` は全員、`give` / `reload` / `edit` はタブ補完上も `mgmenu.admin` 保持者にのみ候補表示されます。エイリアスは `/menu`・`/mgm`・`/ミニゲーム` です。

## 編集モード（`/mgmenu edit`）

config.yml を直接いじらなくても、**ゲーム内のGUIからメニューを編集** できます。`/mgmenu edit`（`mgmenu.admin`）で編集モードを開きます。**変更は自動保存**され、閉じると `config.yml` に反映されます。

### 編集一覧画面での操作

編集一覧には、カテゴリ見出しと各ゲームが並びます。ゲームアイコンに対して次の操作ができます。

| 操作 | 効果 |
|---|---|
| **左クリック**（1つ目→2つ目） | 1つ目を選択 → 2つ目を左クリックで **並び替え（入れ替え）**。同じものを再度クリックで選択解除 |
| **右クリック** | そのゲームの **個別編集画面** を開く |
| **Shift＋クリック** | そのゲームを **削除** |
| カテゴリ見出しをクリック | そのカテゴリに **ゲームを新規追加**（下記の追加フロー） |
| `« / »`（前／次） | ページ送り |
| 退出ボタン | 編集を終了（自動保存済み） |

### ゲームの新規追加フロー

カテゴリ見出しをクリックすると、チャット入力で追加を進めます。

1. **アイコン** … クリック時に **手に持っていたアイテム** がアイコンになります（何も持っていなければ紙）。
2. **参加コマンド** … チャットで参加コマンドを入力します（例：`colortag join`。先頭の `/` は不要）。中止は `cancel`。
3. **表示名** … チャットで表示名を入力します（`&` で色指定可）。

追加したゲームには **参加（join）コマンドのみ** が設定されます。離脱・開始も付けたい場合は、後述の config.yml で `actions.leave` / `actions.start` を追記してください。

### 個別編集画面（ゲームを右クリック）

| ボタン | 効果 |
|---|---|
| 戻る | 編集一覧へ戻る |
| 削除 | このゲームを削除 |
| アイコン設定 | **手に持っているアイテム** をアイコンにする |
| カテゴリ移動 | もう一方のカテゴリへ移動（ミニゲーム⇔カジノ など） |
| 名前変更 | チャットで表示名を入力して変更 |
| コマンド変更 | チャットで参加コマンドを入力して変更 |

!!! tip "編集モードとconfigは併用できます"
    編集モードは並び替え・追加・名前／アイコン／参加コマンドの変更に対応します。**離脱・開始コマンドや便利ボタン（extras）・GUIの見た目**などの細かい設定は `config.yml` を直接編集し、`/mgmenu reload` で反映してください。編集モードでの変更も config.yml に自動保存されるので、両方を併用できます。

## config.yml

### settings（メニューアイテムの配布・保護）

```text title="設定例（settings セクション）"
settings:
  give-on-first-join: true
  give-on-join: true
  prevent-drop: true
  prevent-move: true
```

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.give-on-first-join` | `true` | 初参加時にメニューアイテムを配布する |
| `settings.give-on-join` | `true` | **毎回の入室時** にメニューアイテムを配布する |
| `settings.prevent-drop` | `true` | メニューアイテムのドロップを禁止する |
| `settings.prevent-move` | `true` | メニューアイテムのインベントリ移動を禁止する |

!!! warning "give-on-join が true だと毎回配布されます"
    配布判定は「初参加 かつ `give-on-first-join`」**または** `give-on-join` です。`give-on-join: true` の場合は初参加かどうかに関わらず **毎回の入室時** に配布されます（既に所持していれば配布されません）。初回のみ配りたい場合は `give-on-join: false` にしてください。

### menu-item（メニューアイテムの見た目・配置）

```text title="設定例（menu-item セクション）"
menu-item:
  material: PAPER
  name: "&6&l生活鯖メニュー &7(右クリック)"
  lore:
    - "&7右クリックでミニゲーム一覧を開く"
  custom-model-data: 0
  slot: 8
```

| キー | 既定値 | 説明 |
|---|---|---|
| `menu-item.material` | `PAPER` | アイテムの種類（読み込み側の内部既定は `WRITTEN_BOOK` だが、標準 config.yml は `PAPER`） |
| `menu-item.name` | 「生活鯖メニュー (右クリック)」 | 表示名（`&` カラーコード対応） |
| `menu-item.lore` | （上記） | 説明文 |
| `menu-item.custom-model-data` | `0` | カスタムモデルデータ |
| `menu-item.slot` | `8` | 配布時に入れるホットバースロット（0〜8。範囲外は自動補正） |

### gui（2つの画面のタイトル・サイズ）

```text title="設定例（gui セクション）"
gui:
  list:
    title: "&1&lミニゲームメニュー"
    rows: 6
    filler: GRAY_STAINED_GLASS_PANE
  action:
    title: "&1%game%"
    rows: 3
    filler: BLACK_STAINED_GLASS_PANE
```

| キー | 既定値 | 説明 |
|---|---|---|
| `gui.list.title` | `ミニゲームメニュー` | 第1画面（一覧）のタイトル |
| `gui.list.rows` | `6` | 一覧画面の段数（1〜6） |
| `gui.list.filler` | `GRAY_STAINED_GLASS_PANE` | 空きスロットを埋める素材 |
| `gui.action.title` | `%game%` | 第2画面（操作）のタイトル。`%game%` はゲーム名に置換 |
| `gui.action.rows` | `3` | 操作画面の段数（1〜6） |
| `gui.action.filler` | `BLACK_STAINED_GLASS_PANE` | 操作画面の埋め素材 |

!!! note "旧キー互換"
    `gui.list` が無い場合は旧キー `gui.main` も読み込まれます。

### buttons（操作画面のボタン）

```text title="設定例（buttons セクション）"
buttons:
  join:  { material: LIME_WOOL,   name: "&a&l▶ 参加する", slot: 11 }
  leave: { material: RED_WOOL,    name: "&c&l◀ 離脱する", slot: 13 }
  start: { material: YELLOW_WOOL, name: "&e&l⚑ 開始する", slot: 15 }
  back:  { material: ARROW,       name: "&7« 戻る",       slot: 22 }
```

| キー | 既定 material / slot | 説明 |
|---|---|---|
| `buttons.join` | `LIME_WOOL` / 11 | 参加ボタン |
| `buttons.leave` | `RED_WOOL` / 13 | 離脱ボタン |
| `buttons.start` | `YELLOW_WOOL` / 15 | 開始ボタン |
| `buttons.back` | `ARROW` / 22 | 一覧へ戻るボタン |

ボタンは、そのゲームに対応する `actions` が定義されている場合のみ表示されます（未定義は非表示）。

### categories / games / actions（ゲーム定義）

カテゴリは **定義順に上から自動整列** されます（`slot` 指定は不要）。各カテゴリの左端にカテゴリ見出しが置かれ、同じ行の右側にゲームが並びます。

```text title="ゲーム定義の構造"
categories:
  minigame:                   # カテゴリID
    name: "&bミニゲーム"
    icon: NETHER_STAR
    games:
      kakurenbo:              # ゲームID
        name: "&aかくれんぼ"
        icon: OAK_LEAVES
        lore: ["&7隠れて逃げ切れ"]
        actions:
          join:  { executor: player, command: "kakurenbo join" }
          leave: { executor: player, command: "kakurenbo leave" }
          start: { executor: player, command: "kakurenbo start" }
```

| キー | 説明 |
|---|---|
| `categories.<id>.name` | カテゴリ見出しの表示名 |
| `categories.<id>.icon` | 見出しアイコン素材（既定 `CHEST`） |
| `categories.<id>.games.<id>.name` | ゲーム名（操作画面タイトルにも使用） |
| `categories.<id>.games.<id>.icon` | ゲームアイコン素材（既定 `PAPER`） |
| `categories.<id>.games.<id>.lore` | 説明文 |
| `…actions.{join,leave,start}.executor` | `player`＝本人実行／`console`＝コンソール実行 |
| `…actions.{join,leave,start}.command` | 実行するコマンド。文字列1つ、または複数行リスト可。`%player%` 置換対応 |

!!! tip "標準で収録されるゲーム"
    既定 config は **`minigame`・`casino` の2カテゴリ** 構成です。**ミニゲーム24種**（LOL/かくれんぼ/スプラ/ドロケイ/残機制PVP/雪合戦/雪鬼/青鬼/色鬼/ベッドウォーズ/PVP/TNTラン/アスレタイマー/バトルロイヤル/ボンバーマン/マリカー/人狼/釣りイベント/雪クラフト/2D宝探し/人狼RPG/DBD/エリトラレース/スナイパー）、**カジノ8種**（ブラックジャック/ポーカー/スロット/宝くじ/チンチロ/クイズ/競馬/育成競馬）が定義されています。連携コマンドは各ゲーム側の `join`/`leave`/`start` を呼び出します（スプラ・ドロケイ・アスレタイマーなど一部は `join`/`leave` のみ）。

!!! warning "command が無いアクションはスキップ"
    `actions` 下に `command` が無いアクションは警告ログを出してスキップされます（ボタンも表示されません）。連携先のコマンド名が変わった場合は、ここを実環境に合わせて修正してください。

### extras（一覧画面の便利ボタン）

一覧GUIに直接置く **単発コマンド実行ボタン** です。ゲームは最下段を除く全段（既定6段なら 0〜4段目＝スロット0〜44）に自動配置され、**最下段（スロット45〜53）** が便利ボタン・閉じるボタン用に確保されます。便利ボタンは config の `slot` で指定した位置に置かれます（既定では最下段のスロット45〜52）。最下段右端のスロット53には常に `BARRIER` の「閉じる」ボタンが置かれます。

```text title="便利ボタンの定義例"
extras:
  spawn-world:
    slot: 45
    material: RED_BED
    name: "&c&l初期スポーン"
    lore: ["&7ワールドの初期リスポーン地点へ移動"]
    executor: player
    command: "spawn"
  gs-give:
    slot: 51
    material: STICK
    name: "&6共有倉庫の棒を入手"
    executor: console
    command: "gs give %player%"
```

| キー | 説明 |
|---|---|
| `extras.<id>.slot` | 配置スロット（必須。未指定はスキップ） |
| `extras.<id>.material` | 素材（既定 `PAPER`） |
| `extras.<id>.name` | 表示名 |
| `extras.<id>.lore` | 説明文 |
| `extras.<id>.executor` | `player` または `console`（`%player%` 置換対応） |
| `extras.<id>.command` | 実行コマンド（必須。文字列／リスト可。未指定はスキップ） |

標準では初期スポーン・スポーン1〜5（`/spawn` 系）、共有倉庫の棒入手（`gs give %player%`）、個人倉庫の本入手（`ps give %player%`）が定義されています。`gs give`・`ps give` はOP専用コマンドのため `executor: console` で実行されます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `mgmenu.use` | 全員 | メニューを開ける（`/mgmenu open`・メニューアイテム右クリック） |
| `mgmenu.admin` | OP | `give` / `reload` などの管理コマンド |

!!! note "実行されるコマンド側の権限に注意"
    メニューはあくまでコマンドを **代理実行** するだけです。`executor: player` の場合、その連携コマンドはプレイヤー本人の権限で実行されます。管理者専用コマンドを呼ぶボタンは `executor: console` を使ってください（標準の `gs give`・`ps give` がこの方式です）。

## config 自動生成・再読込について

!!! warning "既存サーバーは config が自動追記されません"
    本プラグインは `onEnable` で `saveDefaultConfig()` のみを行います。**config.yml が無ければ初回に生成** されますが、既存の `config.yml` に新キーを自動追記することはありません。新しいゲームや便利ボタンを追加したい場合は **手動で追記**、または既存 config を退避して再生成してください。読み込み側にも既定値があるため、キーが欠けていても基本動作はします。

!!! tip "編集後は reload で反映"
    config を編集したら `/mgmenu reload` で再読み込みできます（サーバー再起動は不要）。読み込み時、不明な material はログ警告を出して既定素材にフォールバックします。

## トラブルシューティング

??? failure "ボタンを押してもゲームに参加できない"
    連携先プラグインが導入され、対応コマンド（`actions.*.command`）が実際に動作するか確認してください。コマンド名が異なる場合は config を実環境に合わせて修正します。

??? failure "あるゲームに「開始」ボタンが出ない"
    そのゲームの `actions` に `start` が定義されていません。定義が無いアクションのボタンは表示されない仕様です。必要なら `actions.start` を追記してください。

??? failure "便利ボタンが表示されない"
    `extras.<id>.slot` または `command` が未設定だとスキップされます（警告ログ）。`slot` は一覧GUIの範囲内（既定6段=0〜53、便利ボタン用の最下段は45〜52、スロット53は閉じるボタンで固定）にしてください。

??? failure "メニューアイテムが配られない／毎回配られる"
    `settings.give-on-first-join` と `give-on-join` を確認してください。初回のみ配布したい場合は `give-on-join: false` にします。既に所持している場合は配布されません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← MiniGameMenu 概要へ](index.md){ .md-button }
