<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# CasinoPlugin ― OP・運営ガイド { .page-op #casino-plugin-op }

CasinoPlugin の導入・共通設定・モジュール構成・権限・コマンドの全体像をまとめます。各ゲームの個別設定は、それぞれの個別ページを参照してください。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | CasinoPlugin |
| api-version | 26.1.2 |
| メインクラス | `jp.casinoplugin.CasinoPlugin` |
| softdepend（任意連携） | WorldGuard（カート用）、Citizens（クイズの NPC 用） |
| 共通設定 | `plugins/CasinoPlugin/config.yml`（全体設定とモジュール有効フラグ） |
| データ | `plugins/CasinoPlugin/accounts.yml`、各モジュール用ファイル |

!!! info "CasinoPlugin の構成"
    CasinoPlugin は **銀行（bank）＋8ゲームの計9モジュール** を1つのjarに統合したプラグインです。`ModuleRegistry` が `config.yml` の `modules.<id>.enabled` を見て、有効なモジュールだけを起動します。あるモジュールが起動に失敗しても他のモジュールは動き続ける設計です。

## 導入手順

1. ビルドした `CasinoPlugin-*.jar` をサーバーの `plugins/` フォルダに配置する。
2. 必要に応じて `WorldGuard`・`Citizens` を導入する（softdepend。無くても本体は起動しますが、カートの一部機能・クイズの NPC 機能が使えません）。
3. サーバーを起動すると `plugins/CasinoPlugin/config.yml` ほか、各モジュールが必要とするデータファイル（`accounts.yml` などモジュールごとに異なる）が自動生成される。
4. `config.yml` の `modules:` ブロックで、使いたいモジュールだけを `enabled: true` にする。
5. 各ゲームの個別設定（座標・配当・確率など）は、それぞれのモジュールが管理する yml ファイルで編集する（詳細は各ゲームの個別ページ参照）。

!!! warning "bank モジュールは必須"
    `bank` モジュールはエメラルドの口座機能（共通通貨）を提供し、他のすべてのゲームがこれに依存します。`modules.bank.enabled` は **通常 true 固定** で運用してください。false にすると他ゲームの賭け金処理が動きません。

## 含まれるモジュール一覧

| モジュール ID | 内容 | 主なデータ／設定ファイル |
|---|---|---|
| `bank` | エメラルド銀行（経済基盤・共通通貨） | `accounts.yml`（残高） |
| `kart` | カートレース（マリオカート風） | モジュール内部で複数の yml を生成 |
| `poker` | ポーカー（テキサスホールデム） | `poker/` 配下 |
| `slot` | スロットマシン | `slot/` 配下 |
| `lottery` | 宝くじ | `lottery/` 配下 |
| `tintiro` | チンチロ（サイコロ賭博） | `tintiro/` 配下 |
| `blackjack` | ブラックジャック | `blackjack/` 配下 |
| `horse` | 競馬（7種類の馬券） | `horse/` 配下 |
| `quiz` | クイズ（デイリー＋タワー） | `quiz/` 配下 |

!!! note "各ゲームの詳細は個別ページへ"
    上記モジュールの個別設定（座標設定・配当率・確率調整など）は、このページでは深掘りしません。各ゲームの個別ページにまとめられています。

## config.yml 主要項目

`config.yml` は **全体設定とモジュール有効フラグのみ** を扱います。各ゲームの個別設定は `modules/<name>.yml` に分離されています。

| キー | 既定値 | 説明 |
|---|---|---|
| `locale` | `ja` | 言語ロケール（現状 `ja` のみ） |
| `server-account-uuid` | `00000000-0000-0000-0000-000000000000` | ハウス（ディーラー側）として使う固定 UUID。ベット金額の一時保管口座。通常は既定のままで問題なし |
| `modules.<id>.enabled` | `true` | 各モジュールの有効化フラグ。`false` にすると onEnable が呼ばれず、コマンド・リスナーも登録されない |
| `debug.verbose` | `false` | true で各モジュールの詳細ログを出力（本番では false 推奨） |

!!! tip "モジュールの ON / OFF"
    `modules:` ブロックには `bank` / `kart` / `poker` / `slot` / `lottery` / `tintiro` / `blackjack` / `horse` / `quiz` の9項目があります。不要なゲームを `enabled: false` にすると、そのコマンドとリスナーは一切登録されず、サーバーが軽くなります。`bank` だけは前述の通り true 固定が前提です。

## 権限ノード

ゲーム別に整理します。各モジュールごとに個別の権限ノードがあります。

### 銀行（bank）

| 権限 | 既定 | 用途 |
|---|---|---|
| `emerald.list` | 全員 | 全プレイヤーの残高リストを表示 |
| `emerald.admin` | OP | 管理者用コマンド（ATM 看板設置など） |

### カート（kart）

| 権限 | 既定 | 用途 |
|---|---|---|
| `kartrace.play` | 全員 | レースに参加できる |
| `kartrace.garage` | 全員 | ガレージを開ける |
| `kartrace.shop` | 全員 | カートショップを利用できる |
| `kartrace.admin` | OP | KartRace の管理コマンド全般 |
| `kartrace.sign.create` | OP | 参加・退出看板を設置できる |

### ポーカー（poker）

| 権限 | 既定 | 用途 |
|---|---|---|
| `poker.admin` | OP | ポーカー管理コマンドの全権限（下記3つを含む） |
| `poker.admin.setup` | OP | セットアップ系コマンド |
| `poker.admin.control` | OP | ゲーム制御系コマンド |
| `poker.admin.debug` | OP | デバッグ系コマンド |

### スロット（slot）

| 権限 | 既定 | 用途 |
|---|---|---|
| `slot.use` | 全員 | スロットを利用できる |
| `slot.remote` | OP | リモートスロットアイテムを取得できる |

### 宝くじ（lottery）

| 権限 | 既定 | 用途 |
|---|---|---|
| `kuzi.reload` | OP | 宝くじ機能のリロード権限 |

### ブラックジャック（blackjack）

| 権限 | 既定 | 用途 |
|---|---|---|
| `blackjack.start` | OP | ブラックジャックを開始する |
| `blackjack.stop` | OP | ブラックジャックを強制終了する |
| `blackjack.setup` | OP | 看板・地点の設定 |

!!! note "プレイヤーは看板から参加"
    `/blackjack` コマンドは原則 OP 専用です。プレイヤーは `[BlackJack]` 看板を経由して参加します。

### チンチロ（tintiro）

| 権限 | 既定 | 用途 |
|---|---|---|
| `tintiro.admin` | OP | チンチロの管理コマンド全権限 |

!!! note "プレイヤーは看板から参加"
    `/tintiro` コマンドは原則 OP 専用です。プレイヤーは `[チンチロ]` 看板（lobby/leave/金額/open）から参加します。

### 競馬（horse）

| 権限 | 既定 | 用途 |
|---|---|---|
| `horseracing.admin` | OP | 競馬の管理コマンド全権限 |
| `horseracing.bet` | OP | `/horseracing bet` の実行権限（プレイヤーは getitem 看板で取得した馬券アイテムからベット） |
| `horseracing.sign.create` | OP | 馬券販売所/結果掲示看板の設置 |

### クイズ（quiz）

| 権限 | 既定 | 用途 |
|---|---|---|
| `quiz.use` | false | クイズ機能（デイリー / タワー / ランキング）を利用できる |
| `quiz.daily` | false | デイリークイズを利用できる |
| `quiz.tower` | false | クイズタワーを利用できる |
| `quiz.ranking` | false | クイズランキングを利用できる |
| `quiz.admin` | false | クイズ管理コマンド全権限 |
| `quiz.admin.reload` | false | クイズの reload |
| `quiz.admin.npc` | false | クイズ NPC の管理 |
| `quiz.admin.sign` | false | ランキング看板の管理 |

!!! warning "クイズの権限は既定 false"
    `quiz.*` 系の権限は **既定値が false** です。プレイヤーにクイズを遊ばせたい場合は、権限プラグイン（LuckPerms 等）で `quiz.use` を付与してください。

## コマンド一覧

ゲーム別に整理します。

### 銀行（bank）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/emerald` | `/eb` | Emerald Bank メインコマンド（book / money / deposit / withdraw / send / list / wallet / gui / ranking / blankbook / blankwallet） |
| `/ed <金額>` | ― | `/emerald deposit` のエイリアス（預け入れ） |
| `/ew <金額>` | ― | `/emerald withdraw` のエイリアス（引き出し） |

### カート（kart）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/kartrace <subcommand>` | `/kr` `/kart` `/race` | KartRace メインコマンド |
| `/kartadmin <subcommand>` | `/kra` | KartRace 管理コマンド |

### ポーカー（poker）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/poker <setup\|bet\|sign1-5\|start\|stop\|status>` | `/pk` | テキサスホールデムポーカー |

### スロット（slot）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/slot [remote\|setsign]` | ― | スロットマシン GUI / 起動ロッド取得 / 看板登録（OP専用、権限 `slot.admin`） |

### 宝くじ（lottery）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/kuzi <種類> <枚数>` | `/lottery` | 宝くじ機能 |

### チンチロ（tintiro）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/tintiro [subcommand]` | `/ti` | チンチロゲームコマンド |

### ブラックジャック（blackjack）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/blackjack <join\|leave\|bet\|start\|stop\|setup>` | ― | ブラックジャックゲーム |

### 競馬（horse）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/horseracing <subcommand>` | ― | 競馬プラグインコマンド |

### クイズ（quiz）

| コマンド | エイリアス | 説明 |
|---|---|---|
| `/quiz [daily\|tower\|ranking\|admin]` | `/q` | クイズメインコマンド（権限 `quiz.use`） |
| `/qd` | ― | デイリークイズの直接起動（権限 `quiz.use`） |
| `/qt` | ― | クイズタワーの直接起動（権限 `quiz.use`） |
| `/quiznpc [create\|remove\|settype]` | ― | クイズ NPC 管理（Citizens 必須、権限 `quiz.admin`） |
| `/quizsign [create\|remove\|update\|list]` | ― | ランキング看板の管理（権限 `quiz.admin`） |

!!! note "各ゲームのサブコマンドの詳細"
    上記の各コマンドのサブコマンドの細かい使い方（座標設定・運営フローなど）は、それぞれのゲームの個別ページにまとめられています。

## トラブルシューティング

??? failure "あるゲームのコマンドが「不明なコマンド」になる"
    そのモジュールが `config.yml` の `modules.<id>.enabled: false` で無効化されている可能性があります。無効モジュールはコマンド・リスナーが一切登録されません。起動ログに「module [<id>] は config で無効化されています」が出ていないか確認してください。

??? failure "起動ログに「module [<id>] の有効化に失敗しました」と出る"
    そのモジュールの onEnable で例外が発生しています。`ModuleRegistry` は例外を捕捉してログに残すため、他モジュールは動き続けますが、該当モジュールは停止状態です。スタックトレースを確認し、`modules/<name>.yml` の設定不備などを疑ってください。

??? failure "クイズの NPC コマンドが使えない / NPC を作れない"
    クイズの NPC 機能（`/quiznpc`）は **Citizens** に依存します。Citizens が導入されているか確認してください。Citizens は softdepend なので、未導入でも CasinoPlugin 本体は起動しますが NPC 機能は使えません。

??? failure "カートのエリア保護が効かない"
    カートの一部機能は **WorldGuard** と連携します（softdepend）。WorldGuard が導入されているか確認してください。詳細はカートの個別ページを参照してください。

??? failure "プレイヤーがクイズを遊べない"
    `quiz.use` 系の権限は **既定 false** です。権限プラグインで `quiz.use` を付与してください。

??? failure "賭け金の処理が動かない / 残高が反映されない"
    `bank` モジュールが有効になっているか確認してください。`bank` は共通通貨の基盤で、`modules.bank.enabled` が false だと他ゲームの賭け金・配当処理が機能しません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← CasinoPlugin 概要へ](index.md){ .md-button }
