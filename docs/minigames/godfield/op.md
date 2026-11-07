<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ゴッドフィールド（GodField） ― OP・運営ガイド { .page-op #godfield-op }

ゴッドフィールドの導入・席とロビーの設定・看板・config・`cards.yml`・権限・管理コマンドをまとめます。地点・席・看板はコマンドや管理メニューで設定すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | GodField |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/godfield`（別名 `/gf`） |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/GodField/config.yml`・`plugins/GodField/cards.yml` |
| 権限ノード | `godfield.admin`（既定OP） |

## 導入手順

1. ビルドした `GodField` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml`・`cards.yml` が自動生成される（人数・ルール・神器は初期値で動作）。
3. `/gf setstartspawn`・`/gf setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/gf setspawn <1〜9>` で卓の席（プレイヤーが座る位置）を設定する（席が未設定でも、ロビーを中心に自動で円状に並びます）。
5. 参加・離脱・開始の看板を設置する。
6. `/gf status` で設定状況を確認する。

!!! note "席について"
    席（`/gf setspawn 1`〜`9`）は必須ではありません。設定しておくと試合でその位置を優先して使い、未設定のぶんはロビーを中心に半径 `table.auto-radius`（既定4.0）の円状へ自動配置します。`table.freeze-players` が true のときは、試合中は自分の席から動けなくなります。

## セットアップ手順（コマンド）

地点・席は **実行した位置** を使い、看板系は看板を **見ながら** 実行します。すべて `godfield.admin` 権限が必要です。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/gf setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/gf setlobby
```

```text title="卓の席を設定（席番号 1〜9・その場に立って実行）"
/gf setspawn 1
```

```text title="参加看板を登録（看板を見て実行）"
/gf setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/gf setsign leave
```

```text title="開始看板を登録（形式を指定・看板を見て実行）"
/gf setsign start ffa
```

```text title="視線先の看板の登録を解除"
/gf setsign delete
```

!!! note "開始看板の形式"
    開始看板は形式ごとに登録します（`ffa` / `team` / `ffa-cpu` / `team-cpu`）。識別子に「.」は使えません。`/gf start`（引数なし）や引数なしの開始看板では、config の `default-mode` の形式で始まります。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール** と **管理メニュー（GUI）** でも席・地点・看板を設定できます。どちらも `godfield.admin` 権限が必要です。

```text title="管理ツールを受け取る"
/gf wand
```

```text title="管理メニュー（GUI）を開く"
/gf menu
```

## config.yml 設定項目

コマンドで登録する地点・席・看板のデータ（`default-spawn`・`lobby-spawn`・`seats`・`signs`）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。設定の変更は「次の試合から」反映されます（`/gf reload` で再読み込み）。

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `max-lobby` | 16 | ロビーに入れる人数（出場できない人は観戦） |
| `max-players` | 9 | 1試合に出場できる人数（2〜9・参加順に出場） |
| `countdown-seconds` | 5 | 開始前のカウントダウン（秒） |
| `result-seconds` | 8 | 試合終了後、結果を表示しておく秒数 |
| `default-mode` | ffa | 形式省略時の形式（ffa / team / ffa-cpu / team-cpu） |

### ルール（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `game.start-hp` | 40 | 開始時のHP |
| `game.start-mp` | 10 | 開始時のMP |
| `game.start-yen` | 20 | 開始時の¥ |
| `game.max-hp` / `max-mp` / `max-yen` | 99 | HP・MP・¥の上限 |
| `game.start-hand` | 9 | 最初に配る手札の枚数 |
| `game.max-hand` | 15 | 手札の上限（GUIに並ぶのは18枚まで） |
| `game.guarantee-weapon` | true | 最初の手札に武器1・防具1を必ず入れる |
| `game.draw-per-turn` | 1 | 自分の番の始めに授かる枚数 |
| `game.pray-draw` | 1 | 「祈る」で授かる枚数 |
| `game.turn-seconds` | 40 | 自分の番の制限時間（時間切れは祈る扱い） |
| `game.defense-seconds` | 15 | 攻撃されたときに防具を選ぶ制限時間 |
| `game.sub-seconds` | 20 | 買う・売る・両替などの操作の制限時間 |
| `game.turn-gap-ticks` | 20 | 行動のあと次の人へ移るまでの間（20=1秒） |
| `game.max-rounds` | 30 | 最大ラウンド。超えたらHPの多い方の勝ち（0=無制限） |
| `game.team-count` | 2 | チーム戦のチーム数（2〜3） |
| `game.friendly-fire` | false | チーム戦で味方を攻撃の対象にできるか |
| `game.disease-worsen-chance` | 5 | 病が自分の番ごとに悪化する確率（%） |
| `game.dream-chance` | 50 | 夢の間、授かった神器が別物に見える確率（%） |
| `game.doomsday-round` | 20 | 終末の時が始まるラウンド（0=来ない） |
| `game.revive-hp` | 10 | 太陽のお守りで復活したときのHP |
| `game.exchange-step` | 5 | 両替1回で動かす量 |
| `game.exchange-limit` | 30 | 1回の両替カードで動かせる合計 |

### 終末の時（`doomsday`）

`game.doomsday-round` 以降、神器を授かるたびに下の確率で悪魔などが現れます。確率は1回の抽選ごとの%（合計が100を超えないように）。

| キー | 既定値 | 説明 |
|---|---|---|
| `doomsday.small-demon-chance` / `small-demon-damage` | 7 / 10 | 小悪魔の出現率とダメージ |
| `doomsday.mid-demon-chance` / `mid-demon-damage` | 5 / 20 | 中悪魔の出現率とダメージ |
| `doomsday.big-demon-chance` / `big-demon-damage` | 3 / 30 | 大悪魔の出現率とダメージ |
| `doomsday.prankster-chance` / `prankster-steal` | 5 / 2 | イタズラマン（手札を盗む）の出現率と枚数 |
| `doomsday.fairy-chance` / `fairy-amount` | 5 / 10 | めぐみの妖精（HP・MP・¥が増える）の出現率と量 |

### CPU（`cpu`）

| キー | 既定値 | 説明 |
|---|---|---|
| `cpu.fill-to` | 4 | CPU入りの形式で、合計人数がこの人数になるまでCPUを入れる（最低1体） |
| `cpu.think-ticks` | 30 | CPUが行動するまでの時間（20=1秒） |
| `cpu.defense-ticks` | 20 | CPUが防具を選ぶまでの時間 |
| `cpu.end-when-no-human` | true | 人間の出場者が全員いなくなったら、CPU同士で続けず引き分けで終える |

### 卓（`table`）・環境

| キー | 既定値 | 説明 |
|---|---|---|
| `table.freeze-players` | true | 試合中は自分の席から動けないようにする |
| `table.name-height` | 2.3 | 席の上に出す名前表示の高さ |
| `table.auto-radius` | 4.0 | 席が未設定のとき、ロビーを中心に自動で並べる円の半径 |
| `keep-food` | true | ロビー在籍中は満腹度を減らさない |
| `protect-lobby` | true | ロビー在籍中はダメージを受けない |

## cards.yml（神器の定義）

神器の中身は `plugins/GodField/cards.yml` で追加・調整できます。各神器は `name`・`type`（WEAPON / ADD / ARMOR / MIRACLE / ITEM）・`element`（NONE / FIRE / WATER / WOOD / EARTH / LIGHT / DARK）・`target`（ONE / ALL / SELF）・`power`・`mp`・`price`・`weight`（授かりやすさの重み）・`effect`・`status`・`icon`・`desc` などを持ちます。

!!! note "cards.yml の扱い"
    `cards-version` は神器定義の版で、**手で変えないでください**（古い版は起動時に新しい版へ置き換わります）。プラグイン更新で新しい神器が増えても、書き換えた値は上書きされません。いらない神器は **消さずに `enabled: false`** にしてください（消すと次の更新で戻ります）。書き換えたら `/gf reload` で反映されます。サーバーにある神器の一覧は `/gf cards` でも確認できます。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/gf setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/gf setlobby` | ロビー地点を設定 |
| `/gf setspawn <1-9>` | 卓の席を設定 |
| `/gf setsign <join\|leave\|start <形式>\|delete>` | 看板を登録／解除 |
| `/gf wand` | 管理ツールを受け取る |
| `/gf menu` | 管理メニュー（GUI）を開く |
| `/gf stop` | 試合を強制停止（勝敗なし・在籍は維持） |
| `/gf reload` | 設定（config.yml・cards.yml）を再読み込み |
| `/gf leave <プレイヤー>` | 指定した人を離脱させる |
| `/gf lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/gf status` | 設定状況・現在の状況を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/gf join` | ロビーに参加（参加看板と同等） |
| `/gf leave` | ロビーから離脱（離脱看板と同等。試合中は昇天扱い） |
| `/gf start [ffa\|team\|ffa-cpu\|team-cpu]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/gf hand` | 手札・防御の画面を開く |
| `/gf status` | 設定・状況を確認（全員可） |
| `/gf cards` | 神器の一覧を見る |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `godfield.admin` | OP | `setstartspawn`・`setlobby`・`setspawn`・`setsign`・`wand`・`menu`・`stop`・`reload`・`leave <プレイヤー>`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/gf join`・`/gf leave`（自分）・`/gf start`・`/gf hand`・`/gf status`・`/gf cards` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。`/gf leave <プレイヤー>` で他人を指定できるのは `godfield.admin` を持つ場合だけです。

## トラブルシューティング

??? failure "ゲームが開始できない"
    対人戦（`ffa`・`team`）はロビーに2人以上必要です。1人で遊ぶときは `ffa-cpu` か `team-cpu` で開始してください。チーム戦はチーム数（既定2）以上の人数が必要です。`cards.yml` に神器が1つもないと開始できません。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/gf lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/GodField/` に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ゴッドフィールド】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。試合結果も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない・ロックが残った場合はOPが `/gf lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません）。地点・席・看板などのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも手札の本・管理ツールを使え、登録看板は蝋引きして編集画面が開かないようにします。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | （使われません。2026-11-08 から、未復元の退避データは期限切れで捨てず、次のログインで必ず返します） |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | ゴッドフィールド | 「【】」に入れる正式名 |
| `name-tag.color` | GOLD | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも手札の本・管理ツールを使える |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ゴッドフィールド（GodField） 概要へ](index.md){ .md-button }
