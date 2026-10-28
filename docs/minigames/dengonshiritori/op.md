<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 伝言しりとり ― OP・運営ガイド { .page-op #dengonshiritori-op }

伝言しりとり（DengonShiritori）の導入・会場設定・看板・config・権限・管理コマンドをまとめます。地点・看板・会場はコマンド（または管理メニュー）で登録すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | DengonShiritori |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/dengon` |
| api-version | 26.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/DengonShiritori/config.yml` |
| 単語辞書 | `plugins/DengonShiritori/words.yml`（ヒント・候補用の単語） |
| 権限ノード | `dengon.admin`（既定OP） |

## 導入手順

1. ビルドした `DengonShiritori` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml`・`words.yml` が自動生成される（人数・時間などは初期値で動作）。
3. `/dengon setstartspawn`・`/dengon setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/dengon create <会場>` で会場を作り、`/dengon setorigin`・`/dengon setplotsize`・`/dengon setview` で区画を設定する。
5. 参加・離脱・開始の看板を設置する（ランキング看板は任意）。
6. `/dengon status` で設定状況を確認する。

!!! note "会場（venue）とは"
    会場は、建築する区画の集まりです。`setorigin` で区画の基準点、`setplotsize` で区画のサイズ、`setview` で答え合わせ時の観覧位置を決めます。区画は試合開始時に自動生成され、終了時に片付けられます（建材・床・壁はブロックで自動構築）。会場は複数登録でき、開始看板や `/dengon start <会場>` で選べます。

## セットアップ手順（コマンド）

地点・区画は **実行した位置** を使い、看板系は看板を **見ながら**（近くで）実行します。すべて `dengon.admin` 権限が必要です。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/dengon setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/dengon setlobby
```

```text title="会場を作成"
/dengon create <会場>
```

```text title="区画の基準点を設定（その場に立って実行）"
/dengon setorigin <会場>
```

```text title="区画のサイズを設定（幅5〜15・高さ5〜20）"
/dengon setplotsize <会場> <幅> <高さ>
```

```text title="答え合わせの観覧位置を設定（その場に立って実行）"
/dengon setview <会場>
```

```text title="区画を試し生成してプレビュー"
/dengon preview <会場>
```

```text title="会場を削除（確認付き）"
/dengon remove <会場> confirm
```

```text title="参加看板を登録（看板を見て実行）"
/dengon setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/dengon setsign leave
```

```text title="開始看板を登録（会場指定・看板を見て実行）"
/dengon setsign start <会場>
```

```text title="ランキング看板を登録（順位指定・看板を見て実行）"
/dengon setsign rank <順位>
```

```text title="視線先の看板の登録を解除"
/dengon setsign delete
```

## 設定ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール（ブレイズロッド）** と **管理メニュー（GUI）** でも地点・区画・看板を設定できます。どちらも `dengon.admin` 権限が必要です。

```text title="管理ツール（ブレイズロッド）を受け取る"
/dengon wand
```

```text title="管理メニュー（GUI）を開く"
/dengon menu
```

- 管理メニューからは、会場の作成・区画やスポーンの「現在地を設定」・看板モードの切り替え・設定状況の確認などを操作できます。
- 管理ツールは、空中をタップ（統合版は腕振り）でメニューを開けます。
- 管理ツールはOP専用で、受け取り時・使用時の両方で `dengon.admin` を確認します。伝言しりとりに参加中は受け取れないので、先に `/dengon leave` で離脱してください。

## config.yml 設定項目

コマンドで登録する地点・看板・会場のデータ（`default-spawn`・`lobby-spawn`・各 `*-sign`・`venues` など）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。以下は数値・ルールなどの設定項目です（変更は `/dengon reload` で反映、試合中は不可）。

### 人数・ラウンド

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 3 | 開始に必要な人数（2〜8） |
| `max-players` | 8 | 最大人数（3〜8。区画の列数の上限にもなる） |
| `start-countdown` | 10 | 開始のカウントダウン（秒） |
| `max-build-rounds` | 4 | 建築ラウンドの最大数（2〜8。実際は人数との小さい方） |

### 時間（秒）

| キー | 既定値 | 説明 |
|---|---|---|
| `word-seconds` | 25 | 言葉決めの時間（10〜60） |
| `build-seconds` | 150 | 建築の時間（60〜300） |
| `guess-seconds` | 40 | 回答の時間（15〜90） |
| `reveal-step-seconds` | 6 | 答え合わせ1ステップの時間（3〜10） |
| `allow-skip` | true | 答え合わせで全員がスキップを押したら、その列を早送りする |

### 言葉のルール

| キー | 既定値 | 説明 |
|---|---|---|
| `min-word-length` | 2 | 言葉の最短文字数 |
| `max-word-length` | 12 | 言葉の最長文字数（統合版でタイトルが切れないよう12推奨） |
| `chouon-rule` | PREVIOUS | 長音で終わる言葉の最後の文字（PREVIOUS=長音の前の文字 / VOWEL=その母音） |
| `allow-n-ending` | false | 「ん」で終わる言葉を許すか |
| `dakuten-flex` | true | 最初の文字の濁点・半濁点を区別しない（「は」指定で「ば」「ぱ」も可） |
| `match-loose` | true | 正解判定で小さい文字・長音の違いを無視する |
| `show-start-char` | true | 回答のときに、作品の最初の文字を教える |

### 区画（新規会場の既定値）

| キー | 既定値 | 説明 |
|---|---|---|
| `plot-size` | 9 | 区画の内側の幅（5〜15）。会場作成後は `setplotsize` で変更 |
| `plot-height` | 10 | 区画の高さ（5〜20） |
| `wall-block` | WHITE_CONCRETE | 区画の壁ブロック |
| `floor-block` | SMOOTH_STONE | 区画の床ブロック |
| `build-blocks-per-tick` | 4000 | 区画の生成・片付けで1tickに置くブロック数（重いときは下げる） |

### 建築

| キー | 既定値 | 説明 |
|---|---|---|
| `allow-water` | true | 水バケツを使えるか（流れは区画の外へ出ない） |
| `banned-items` | TNT ほか | 建築中に使えないブロック（看板・旗は設定に関係なく常に禁止）。`#` で始めるとブロックタグ |

!!! note "既定の禁止ブロック"
    初期設定では TNT・各種コマンドブロック・ストラクチャーブロック・スポナー・エンダーチェスト・各種ピストン・ディスペンサー／ドロッパー・オブザーバー・書見台・頭・バリア・リスポーンアンカー・各種タマゴなど、仕掛け・爆発・モブ生成に使えるものが禁止されています。看板・旗（文字が書けるもの）は常に禁止です。

### チャット・コマンド

| キー | 既定値 | 説明 |
|---|---|---|
| `mute-chat` | true | 試合中の参加者のチャットを全体に流さない（答えのネタバレ防止） |
| `blocked-commands` | msg, tell ほか | 試合中の参加者が使えないコマンド（内緒話・say など） |

### 得点

| キー | 既定値 | 説明 |
|---|---|---|
| `points-correct-guesser` | 1 | 正解した回答者への加点 |
| `points-correct-builder` | 1 | 作品が伝わった建築者への加点 |
| `points-perfect-chain` | 2 | 完全伝言ボーナス（列の全員） |
| `ranking-size` | 10 | `/dengon top` とランキング看板の表示件数 |

### 環境・その他

| キー | 既定値 | 説明 |
|---|---|---|
| `protect-lobby` | true | ロビー待機中もダメージを受けない |
| `debug` | false | 列の割り当て・判定結果をログに出す |

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/dengon setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/dengon setlobby` | ロビー地点を設定 |
| `/dengon create <会場>` | 会場を作成 |
| `/dengon setorigin <会場>` | 区画の基準点を設定（現在地） |
| `/dengon setplotsize <会場> <幅> <高さ>` | 区画のサイズを設定（幅5〜15・高さ5〜20） |
| `/dengon setview <会場>` | 答え合わせの観覧位置を設定（現在地） |
| `/dengon preview <会場>` | 区画を試し生成してプレビュー |
| `/dengon remove <会場> confirm` | 会場を削除（元に戻せない） |
| `/dengon setsign <join\|leave\|start <会場>\|rank <順位>\|delete>` | 看板を登録／解除 |
| `/dengon wand` | 管理ツール（ブレイズロッド）を受け取る |
| `/dengon menu` | 管理メニュー（GUI）を開く |
| `/dengon test [会場]` | テスト起動（開始条件を緩めて動作確認） |
| `/dengon stop [会場]` | 試合を強制停止 |
| `/dengon reload` | 設定を再読み込み（試合中は不可） |
| `/dengon lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/dengon join` | ロビーに参加（参加看板と同等） |
| `/dengon leave` | ロビーから離脱（離脱看板と同等。試合中は脱落扱い） |
| `/dengon start [会場]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/dengon status` | 設定・状況を確認（読み取り専用） |
| `/dengon stats [プレイヤー]` | 記録を確認 |
| `/dengon top` | ランキングを確認 |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `dengon.admin` | OP | 会場・地点・看板の各 set/create/remove 系・`wand`・`menu`・`test`・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/dengon join`・`/dengon leave`・`/dengon start`・`/dengon status`・`/dengon stats`・`/dengon top` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/dengon status` を確認してください。会場（基準点・区画サイズ・観覧位置）が設定済みで、ロビーに最低人数（既定3人）以上いる必要があります。

??? failure "設定を変更しても反映されない"
    config の変更は `/dengon reload` で再読み込みします（試合中は不可）。多くの値は次の試合から反映されます。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/dengon lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/DengonShiritori/` 以下に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【伝言しりとり】名前」になり、離脱・ログアウト・プラグイン停止で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない場合はOPが `/dengon lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は旧ファイルを `config.yml.bak-日時` に退避）。地点・看板・会場などのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも管理ツール・ヒント・建築完了・スキップを使え、登録看板は蝋引きして編集画面が開かないようにします。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | 未復元の退避データの保持日数 |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | 伝言しりとり | 「【】」に入れる正式名 |
| `name-tag.color` | GOLD | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でもツール・ヒント・完了・スキップを使える |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 伝言しりとり 概要へ](index.md){ .md-button }
