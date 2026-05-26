<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Ranking ― OP・運営ガイド { .page-op #ranking-op }

Ranking の導入・config・ランキング看板の設置・権限・管理コマンド・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Ranking |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/ranking`（エイリアス `/rank`、`/miningrank`、`/mrank`、`/digcount`） |
| 依存プラグイン | **CasinoPlugin**（`depend` 指定。報酬支払いに EmeraldAPI を使用） |
| 設定ファイル | `plugins/Ranking/config.yml`、`signs.yml`（看板）、`archives/`（月次アーカイブ） |
| カウント対象 | 採掘・釣り・設置・食事・討伐・レベル・与ダメージの7統計 |

!!! info "CasinoPlugin への依存について"
    Ranking はエメラルド報酬の支払いに、CasinoPlugin に同梱の `EmeraldAPI` ファサードを利用します。`plugin.yml` で `depend: [CasinoPlugin]` を宣言しているため、CasinoPlugin が無い・無効だと **Ranking 自体が起動しません**。CasinoPlugin が読み込まれていても EmeraldAPI が利用不可の場合は、統計カウント・ランキング表示は動作しますが、報酬の支払いだけが行われません（起動時ログに警告が出ます）。

## 導入手順

1. **先に CasinoPlugin を導入する。** CasinoPlugin の jar を `plugins/` に配置し、正常に動作することを確認する。
2. ビルドした `Ranking-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
3. サーバーを起動すると `plugins/Ranking/config.yml` が自動生成され、`archives/` フォルダが作成される。
4. 起動ログに `Successfully linked with CasinoPlugin (EmeraldAPI).` が出れば連携成功。
5. 設定を変更したら `/ranking reload` で再読み込みする。

!!! warning "ロード順に注意"
    `depend: [CasinoPlugin]` により、Bukkit は CasinoPlugin の有効化が完了してから Ranking を有効化します。CasinoPlugin が `plugins/` に無い、または起動に失敗していると Ranking はロードされません。必ず CasinoPlugin を先に導入・動作確認してください。

## config.yml 設定項目

### 自動保存（`auto-save`）

| キー | 既定値 | 説明 |
|---|---|---|
| `auto-save.interval-seconds` | 300 | 定期保存の間隔（秒）。推奨60～600秒 |
| `auto-save.save-on-action` | false | アクションのたびに即時保存するか。`true` は安全だが負荷が高い |
| `auto-save.save-every-actions` | 10 | `save-on-action` が `true` のとき、何アクションごとに保存するか |
| `auto-save.log-saves` | false | 保存時にログを出力するか（デバッグ用） |

### 報酬（`rewards`）

統計タイプごとに `enabled` / `actions-per-emerald` / `emerald-amount` を設定します。

| キー | 説明 |
|---|---|
| `rewards.<種類>.enabled` | その統計の報酬を有効にするか |
| `rewards.<種類>.actions-per-emerald` | 報酬1回あたりに必要な行動回数 |
| `rewards.<種類>.emerald-amount` | 報酬1回で支払うエメラルド数 |

初期設定値（`<種類>` は `mining` `fishing` `placing` `eating` `killing` `level` `damage`）:

| 種類 | enabled | actions-per-emerald | emerald-amount | 初期設定の意味 |
|---|---|---|---|---|
| `mining`（採掘） | true | 20 | 1 | 20ブロック採掘で1エメラルド |
| `fishing`（釣り） | true | 30 | 1 | 30回釣りで1エメラルド |
| `placing`（設置） | false | 50 | 1 | 報酬なし（無効） |
| `eating`（食事） | false | 100 | 1 | 報酬なし（無効） |
| `killing`（討伐） | true | 50 | 2 | 50体討伐で2エメラルド |
| `level`（レベル） | false | 10 | 1 | 報酬なし（無効） |
| `damage`（与ダメージ） | false | 1000 | 1 | 報酬なし（無効） |

!!! note "報酬の支払い経路"
    報酬は `EmeraldAPI.deposit()` を通じてプレイヤーの口座残高に直接入金されます（CasinoPlugin の銀行モジュール経由）。Ranking 側でエメラルドアイテムを配るわけではありません。EmeraldAPI が利用不可のときは支払われず、対象プレイヤーに警告メッセージが1度だけ表示されます。

### ランキング表示（`ranking`）

| キー | 既定値 | 説明 |
|---|---|---|
| `ranking.display-limit` | 10 | `/ranking ranking` で人数省略時のデフォルト表示人数 |

### 看板（`signs`）

| キー | 既定値 | 説明 |
|---|---|---|
| `signs.update-interval` | 300 | ランキング看板の自動更新間隔（秒）。推奨60～600秒。短すぎると負荷増 |

### 統計の有効化（`stats-enabled`）

各統計をカウント対象とするかどうかを切り替えます。`true` でカウント有効。

| キー | 既定値 | 統計 |
|---|---|---|
| `stats-enabled.mining` | true | 採掘 |
| `stats-enabled.fishing` | true | 釣り |
| `stats-enabled.placing` | true | 設置 |
| `stats-enabled.eating` | true | 食事 |
| `stats-enabled.killing` | true | 討伐 |
| `stats-enabled.level` | true | レベル |
| `stats-enabled.damage` | true | 与ダメージ |

### メッセージ（`messages`）・その他

| キー | 既定値 | 説明 |
|---|---|---|
| `messages.reward-received` | `&a&l[採掘報酬] ...` | 報酬獲得時のメッセージ。`&` カラーコード対応。プレースホルダ `{total}`（累計）、`{reward}`（獲得数）、`{interval}`（必要回数）、`{action}`（行動名）が使える |
| `messages.play-sound` | true | 報酬獲得時に効果音を再生するか |
| `last-reset-month` | `""` | 最後にリセットした月。プラグインが自動設定するため手動編集不要 |

!!! note "月次リセットの挙動"
    Ranking は1時間ごとに日付をチェックし、**日本時間（Asia/Tokyo）で毎月1日** かつ未リセットの月であれば自動的にリセットを実行します。リセット時は前月データを `archives/` フォルダにアーカイブ保存し、全体チャットへ告知し、看板を更新します。`last-reset-month` は二重実行防止のための記録です。

## ランキング看板の設置

看板に統計ごとの上位プレイヤーを表示し、`signs.update-interval` ごとに自動更新する **ランキング看板** を設置できます。

1. 表示したい場所に **看板** を設置する。
2. `/ranking setsign <種類> <順位>` を実行する（`ranking.admin` 権限が必要）。
3. 「看板を設置モードになりました」と表示されたら、対象の看板を **右クリック** する。
4. 看板がランキング表示に変換され、登録される。

| 引数 | 説明 |
|---|---|
| `<種類>` | `mining` `fishing` `placing` `eating` `killing` `level` `damage` のいずれか |
| `<順位>` | 単一順位（例：`1`）または範囲（例：`1-3`、`5-8`） |

- 単一順位（例：`1`）… その順位のプレイヤー名・記録・単位を1枚の看板に表示。
- 範囲指定（例：`1-3`）… 範囲内の複数順位を1枚にまとめて表示。範囲は **最大4人まで**（`1-4` 可、`1-5` 不可）。
- 例：`/ranking setsign fishing 1-3` で釣りランキング1～3位を表示する看板になります。

!!! tip "看板の更新"
    看板は `signs.update-interval` 秒ごとに自動更新されるほか、`/ranking updatesigns` で全看板を即座に更新できます。月次リセット時にも自動で更新されます。

!!! warning "看板データの保存先"
    ランキング看板の位置・設定は `plugins/Ranking/signs.yml` に保存されます。看板のあるワールドが未ロードの場合、その看板は起動時にスキップされます（ログに警告）。

### 看板の削除

`/ranking removesign` を実行し、削除モードになった状態で対象のランキング看板を **右クリック** すると登録が解除されます。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/ranking stats <プレイヤー>` | `ranking.stats.others` | 他プレイヤーの統計を表示 |
| `/ranking setsign <種類> <順位>` | `ranking.admin` | ランキング看板の設置モードを開始 |
| `/ranking removesign` | `ranking.admin` | ランキング看板の削除モードを開始 |
| `/ranking updatesigns` | `ranking.admin` | 登録済みの全看板を即座に更新 |
| `/ranking reload` | `ranking.admin` | config.yml を再読み込み（データも保存） |
| `/ranking reset` | `ranking.admin` | 全データの手動リセット（確認あり） |
| `/ranking reset confirm` | `ranking.admin` | データリセットを実際に実行（アーカイブ保存つき） |

!!! warning "手動リセットについて"
    `/ranking reset` のみでは確認メッセージが出るだけで、実行されません。`/ranking reset confirm` で初めてリセットが実行されます。リセット時は月次リセットと同様に、現在のデータが `archives/` にアーカイブ保存されてから全統計が初期化されます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `ranking.stats` | 全員（`true`） | 自分の統計を表示（`/ranking`、`/ranking stats`） |
| `ranking.stats.others` | OP | 他プレイヤーの統計を表示（`/ranking stats <名前>`） |
| `ranking.ranking` | 全員（`true`） | ランキングを表示 |
| `ranking.admin` | OP | 管理者コマンド全般（看板設置・削除・更新、reload、reset）。`ranking.stats`・`ranking.stats.others`・`ranking.ranking` を子として含む |

!!! note "ランキング表示コマンドの権限について"
    `plugin.yml` には `ranking.ranking`（ランキング閲覧権限、既定 `true`）が定義されていますが、実装上 `/ranking ranking`・`/ranking top` の各処理ではこの権限のチェックが行われていません。実際にはランキング表示は全員が実行できます。`ranking.stats.others` と `ranking.admin` は実装側でもチェックされています。

## トラブルシューティング

??? failure "Ranking が起動しない / ロードされない"
    Ranking は `depend: [CasinoPlugin]` で CasinoPlugin に依存しています。CasinoPlugin が `plugins/` に無い、または起動に失敗していると Ranking はロードされません。先に CasinoPlugin を導入し、正常に動作することを確認してください。

??? failure "報酬（エメラルド）が支払われない"
    起動ログに `CasinoPlugin (or EmeraldAPI) is not available. Reward system will not work.` が出ていないか確認してください。EmeraldAPI が利用不可だと統計カウントは動作しても報酬は支払われません。また、対象の統計の `rewards.<種類>.enabled` が `true` になっているかも確認してください（設置・食事・レベル・与ダメージは初期設定では無効です）。

??? failure "統計がカウントされない"
    `config.yml` の `stats-enabled.<種類>` が `true` か確認してください。なお、クリエイティブモードの採掘・設置、PvP の討伐・与ダメージはそもそもカウント対象外です。設定変更後は `/ranking reload` を実行してください。

??? failure "ランキング看板が表示されない / 更新されない"
    看板が `/ranking setsign` で正しく登録されているか確認してください。看板のあるワールドが起動時に未ロードだとスキップされます（ログに警告）。手動更新は `/ranking updatesigns` で行えます。自動更新の間隔は `signs.update-interval` で調整できます。

??? failure "`/ranking setsign` で「看板の設置に失敗しました」と出る"
    順位の形式が不正です。単一順位（例：`1`）または範囲（例：`1-3`）で指定し、範囲は **開始 ≤ 終了** かつ **最大4人まで**（`end - start ≤ 3`）にしてください。`1-5` のような5人以上の範囲は登録できません。

??? failure "データが消えた / リセットされた"
    Ranking は日本時間で毎月1日に統計を自動リセットします。前月のデータは `plugins/Ranking/archives/` フォルダにアーカイブ保存されています。意図しないリセットが起きた場合は `last-reset-month` の値とサーバー時刻を確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Ranking 概要へ](index.md){ .md-button }
