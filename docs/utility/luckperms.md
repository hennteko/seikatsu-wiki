<div class="audience-banner op">🛠️ OP・運営向けページ — LuckPerms は管理者（OP）専用の権限管理プラグインです。</div>

# LuckPerms ― OP・運営ガイド { .page-op }

<span class="badge done">公開プラグイン</span>

プレイヤー・グループごとに権限（パーミッション）を管理する、権限管理の定番プラグインです。各プラグインのコマンドを「誰が使えるか」を一元管理でき、ランク制度（プレイヤー / モデレーター / 運営など）の土台になります。GUI 風の **Web エディタ** で直感的に編集できるのが特長です。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | LuckPerms（Bukkit 版） |
| バージョン | 5.5.17 |
| 種別 | 公開プラグイン（lucko 製） |
| メインコマンド | `/lp`（エイリアス `/luckperms` `/perms` `/permissions`） |
| 依存 | なし |
| 設定ファイル | `plugins/LuckPerms/config.yml` |
| データ保存 | 既定は H2 データベース（`plugins/LuckPerms/luckperms-h2-v2.mv.db`）。MySQL 等にも変更可 |
| 公式サイト | https://luckperms.net （Wiki: https://luckperms.net/wiki ） |

!!! warning "コマンドは OP / コンソール専用"
    `/lp` 系コマンドは権限 `luckperms.*` を持つ管理者かコンソールのみ使用できます。一般プレイヤーに開放しないでください。誤って `luckperms.*` を配布すると全権限を自由に書き換えられてしまいます。

## 導入手順

1. [luckperms.net](https://luckperms.net/download) から Bukkit 版 jar を入手する。
2. `LuckPerms-Bukkit-5.5.17.jar` を `plugins/` フォルダに配置する。
3. サーバーを再起動すると `plugins/LuckPerms/` に `config.yml` と H2 データベースが自動生成される。
4. まず `/lp group default permission set <権限> true` 等で default グループから設定を始める。

## 基本概念

| 用語 | 説明 |
|---|---|
| ユーザー | プレイヤー個人。個別に権限・グループを付与できる |
| グループ | 権限のまとまり。全員が最初から所属する `default` グループが自動生成される |
| ノード | `essentials.fly` のような権限文字列。`true`（許可）/ `false`(拒否) を設定する |
| 継承（parent） | グループが他グループの権限を引き継ぐ仕組み。ランク階層を作れる |
| weight | グループの優先度。複数グループ所属時、weight が大きい方の設定が勝つ |
| コンテキスト | `world=lobby` など、特定条件下でのみ有効な権限を作る仕組み |

## Web エディタ（推奨）

ブラウザ上の GUI で権限を編集できます。コマンドを覚える前に、まずこれを使うのが手軽です。

1. ゲーム内またはコンソールで `/lp editor` を実行する。
2. チャットに表示された URL を開き、ブラウザで権限・グループ・プレフィックスを編集する。
3. 画面右上の保存ボタンを押すと適用コマンド（`/lp applyedits <コード>`）が発行されるので、ゲーム内で実行する。

## コマンド一覧

### 全般

| コマンド | 説明 |
|---|---|
| `/lp info` | プラグインの稼働情報を表示 |
| `/lp editor` | Web エディタを開く |
| `/lp verbose on` / `off` | 権限チェックの監視を開始 / 停止。「どの権限が足りないか」の調査に最有力 |
| `/lp search <ノード>` | その権限を持つユーザー・グループを横断検索 |
| `/lp listgroups` | グループ一覧を表示 |
| `/lp sync` | ストレージからデータを再読み込み |
| `/lp reloadconfig` | `config.yml` を再読み込み（ストレージ設定等は再起動が必要） |
| `/lp export <ファイル名>` | 全データをファイルに書き出し（バックアップ） |
| `/lp import <ファイル名>` | 書き出したデータを取り込み |

### ユーザー操作（`/lp user <プレイヤー名> ...`）

| コマンド | 説明 |
|---|---|
| `/lp user <名前> info` | 所属グループ・権限の概要を表示 |
| `/lp user <名前> permission set <ノード> true/false` | 権限を個別に付与 / 拒否 |
| `/lp user <名前> permission unset <ノード>` | 権限設定を削除 |
| `/lp user <名前> permission check <ノード>` | 権限の有無と、その理由（どこから継承したか）を確認 |
| `/lp user <名前> parent add <グループ>` | グループに所属させる |
| `/lp user <名前> parent remove <グループ>` | グループから外す |
| `/lp user <名前> parent set <グループ>` | 所属グループをそれ1つに置き換える |
| `/lp user <名前> clear` | そのユーザーの設定を全消去（default に戻る） |

### グループ操作（`/lp group <グループ名> ...`）

| コマンド | 説明 |
|---|---|
| `/lp creategroup <名前>` | グループを作成 |
| `/lp deletegroup <名前>` | グループを削除 |
| `/lp group <名前> info` | グループの概要を表示 |
| `/lp group <名前> permission set <ノード> true/false` | グループに権限を付与 / 拒否 |
| `/lp group <名前> permission info` | グループが持つ権限ノード一覧 |
| `/lp group <名前> parent add <親グループ>` | 親グループの権限を継承させる |
| `/lp group <名前> setweight <数値>` | グループの優先度を設定 |
| `/lp group <名前> meta setprefix "<プレフィックス>"` | チャット等のプレフィックスを設定（チャットプラグイン側の対応が必要） |

### 一時付与

`permission set` / `parent add` は末尾に期間を付けると **一時付与** にできます（例: `/lp user Steve parent addtemp vip 30d`）。イベント時の臨時権限に便利です。

## よく使う設定例

```text
# 「moderator」グループを作り、default を継承させ、優先度を設定
/lp creategroup moderator
/lp group moderator parent add default
/lp group moderator setweight 10

# moderator に LWC の管理権限を付与
/lp group moderator permission set lwc.mod true

# プレイヤーを moderator にする
/lp user Steve parent add moderator
```

## config.yml の主な項目

基本的には **既定値のままで動作** します。変更しうるのは以下程度です。

| キー | 既定値 | 説明 |
|---|---|---|
| `storage-method` | `h2` | データ保存先。複数サーバーで共有するなら `mysql` 等に変更 |
| `server` | `global` | サーバー識別名。コンテキスト `server=...` で使用 |
| `sync-minutes` | `-1`（無効） | 外部DB使用時の自動同期間隔 |

!!! note "OP との関係"
    Minecraft 標準の OP 権限は LuckPerms とは独立して機能します。OP はほとんどの権限チェックを素通しするため、**権限設定のテストは OP を外した状態で行ってください**（`/deop` してから確認するか、別アカウントを使う）。

## トラブルシューティング

| 症状 | 対処 |
|---|---|
| 権限を設定したのに反映されない | `/lp user <名前> permission check <ノード>` で実際の判定と理由を確認。OP 状態だと常に許可される点に注意 |
| どの権限ノードが必要か分からない | `/lp verbose on` を実行してから対象の操作をすると、チェックされたノードがすべて表示される。確認後 `/lp verbose off` |
| グループ設定が個人設定で上書きされる | ユーザー個別の設定はグループより優先される。`/lp user <名前> clear` で個別設定を整理 |
| 編集を誤って壊した | 定期的に `/lp export backup` を取っておき、`/lp import backup` で復元 |
