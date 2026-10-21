<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Yukigassen ― OP・運営ガイド { .page-op #yukigassen-op }

Yukigassen の導入・地点のセットアップ・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Yukigassen |
| バージョン | 1.1.0 |
| api-version | 26.1.2 |
| メインコマンド | `/yukigassen <subcommand>` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Yukigassen/config.yml` |

## 導入手順

1. ビルドした `Yukigassen` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Yukigassen/config.yml` が自動生成される。
3. 後述の手順で各種地点を設定する。
4. プレイヤーが集まったら `/yukigassen start` でゲームを開始する。

## config.yml 設定項目

設定ファイルは以下のキーで構成されます。地点（`locations`）はセットアップコマンドで自動的に書き込まれるため、手動編集は不要です。

| キー | 既定値 | 説明 |
|---|---|---|
| `default-lives` | 3 | プレイヤーの初期残機数 |
| `max-players` | 16 | 最大参加人数（満員時は参加拒否） |
| `min-players` | 1 | 最小開始人数（0以下は1扱い） |
| `game.respawn-invincible-seconds` | 3 | リスポーン直後の無敵秒数（スポーンキル防止。0で無効） |
| `game.result-seconds` | 10 | 勝敗確定後、結果発表を表示してからロビーへ戻すまでの秒数 |
| `loadout` | シャベル＋雪玉16 | 配布ロードアウト（リスポーン時にも再付与） |
| `locations.spawn` | 未設定 | 初期リスポーン地点（途中抜け時の戻り先） |
| `locations.lobby` | 未設定 | ロビー地点（受付エリア・ゲーム終了後の戻り先） |
| `locations.spawnred` | 未設定 | 赤チームのスポーン地点（ゲーム中のリスポーン先） |
| `locations.spawnblue` | 未設定 | 青チームのスポーン地点（ゲーム中のリスポーン先） |
| `locations.area.pos1` | 未設定 | ゲームエリアの角1 |
| `locations.area.pos2` | 未設定 | ゲームエリアの角2 |
| `signs.join` / `signs.leave` / `signs.start` / `signs.solo` | `[]` | 看板の位置リスト（`setsign` で自動保存。手動編集不要） |

### CPUチーム戦（`cpu-fill`）

通常戦で人数が足りないチームを CPU で補充します。`/yukigassen setcpu on\|off` でも切り替えられます。

| キー | 既定値 | 説明 |
|---|---|---|
| `cpu-fill.enabled` | false | CPUチーム戦を有効にするか |
| `cpu-fill.team-size` | 3 | 1チームの人数（人間＋CPU）。人間が少ないチームをここまで CPU で埋める |
| `cpu-fill.difficulty` | normal | CPU の強さ（`solo.difficulty` の名前） |
| `cpu-fill.golem-ratio` | 0.4 | CPU のうち砲台（スノーゴーレム）の割合。残りは歩兵（スケルトン） |

### ソロモード（`solo`）

1人で CPU と戦うソロモードの設定です。`/yukigassen solo`・ソロ看板から開始します。

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.menu-delay` | 3 | 試合後、ロビーへ戻ってからソロメニューを開くまでの秒数 |
| `solo.result-seconds` | 3 | 結果表示からロビーへ戻すまでの秒数 |
| `solo.countdown` | 3 | 開始前のカウントダウン秒数 |
| `solo.default-difficulty` | normal | 難易度を省略したときに使う難易度 |
| `solo.player-hits` | 5 | 何回被弾したら終了か（雪だるま防衛では一時凍結） |
| `solo.hit-invincible-ticks` | 10 | 被弾後の無敵時間（tick。20tick=1秒） |
| `solo.snowballs` | 32 | 開始時・補充時に持たせる雪玉の数 |
| `solo.spawn-radius` | 6 | 敵 CPU の出現範囲（青スポーンからの半径） |
| `solo.broadcast-best` | true | 自己ベスト更新をサーバー全体に告知するか |
| `solo.cpu.golem-hp` / `soldier-hp` / `captain-hp` | 2 / 3 / 8 | CPU を倒すのに必要な命中数（難易度の health 倍率がかかる） |
| `solo.cpu.range` | 24 | CPU が狙う最大距離（ブロック） |
| `solo.cpu.max-alive` | 20 | 同時に出る敵 CPU の上限 |
| `solo.difficulty.<id>` | easy/normal/hard | 難易度の定義（`display`・`cpu-multiplier`・`fire-interval`・`spread`・`health`・`icon`）。増減可（名前に「.」は不可） |
| `solo.modes.timeattack` | enabled:true | 殲滅タイムアタック（`golems`・`skeletons`・`time-limit`） |
| `solo.modes.wave` | enabled:true | ウェーブ防衛（`base`・`per-wave`・`captain-every`・`break-seconds`・`golem-ratio`） |
| `solo.modes.snowman` | enabled:true | 雪だるま防衛（`snowman-hp`・`wave-interval`・`freeze-seconds`） |

!!! note "残機・人数設定について"
    `config.yml` の `default-lives` はプラグイン起動時の既定残機です。`/yukigassen setzanki <数>`・`/yukigassen setmax <数>`・`/yukigassen setmin <数>` で変更でき、これらは config.yml にも保存されます。

!!! info "ロードアウトのカスタマイズ"
    `loadout` は `material`（Bukkit Material 名）・`amount`（個数）・`name`（任意・表示名）・`enchants`（任意・「エンチャント名: レベル」のマップ）で指定します。未設定の場合はダイヤのシャベル1本＋雪玉16個が既定として配られます。ソロモードでも同じロードアウトが配られます。

## セットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **その場に立って** 実行します（実行位置が座標として保存されます）。

```text title="初期リスポーン地点（途中抜け時の戻り先）"
/yukigassen setstartspawn
```

```text title="受付ロビー地点"
/yukigassen setlobby
```

```text title="赤チームのスポーン地点"
/yukigassen setspawn red
```

```text title="青チームのスポーン地点"
/yukigassen setspawn blue
```

```text title="ゲームエリアの角1"
/yukigassen setfield 1
```

```text title="ゲームエリアの角2"
/yukigassen setfield 2
```

!!! tip "ゲーム開始の必須条件"
    `/yukigassen start` には **赤チームと青チームのスポーン地点が両方とも設定済み** であることが必要です。`setspawn red` と `setspawn blue` を必ず実行してください。エリア（`setfield`）が未設定の場合はエリア外移動の制限がかからない（マップ全体が有効）扱いになります。

### 参加用看板の設置

ロビーに看板を設置し、**看板を見た状態**で以下のコマンドを実行します（`yukigassen.admin` 権限が必要）。

看板は **参加 / 退出 / 開始 / ソロ** の4種類です。`yukigassen.admin` 権限で、看板を見ながら登録します。

```text title="参加看板として登録"
/yukigassen setsign join
```

```text title="退出看板として登録"
/yukigassen setsign leave
```

```text title="開始看板として登録（クリックでゲーム開始）"
/yukigassen setsign start
```

```text title="ソロ看板として登録（クリックでソロモード）"
/yukigassen setsign solo
```

```text title="登録済み看板の解除（視線先の看板を見て実行）"
/yukigassen setsign delete
```

コマンドを実行すると、プラグインが看板テキストを自動書き込みし、位置を config の `signs.join` / `signs.leave` / `signs.start` / `signs.solo` に保存します（座標で判定するため手書き登録は廃止）。登録看板は統合版で編集画面が開かないよう waxed（蝋がけ）になり、解除すると元に戻ります。開始看板は `/yukigassen setsign start` で登録します（独立した `setstart` コマンドはありません）。`/yukigassen setsign delete` は視線先の登録看板を種別自動判別で解除し、看板テキストも消去します。

## コマンド

### プレイヤー用（全員可）

| コマンド | 説明 |
|---|---|
| `/yukigassen join` | ロビーに参加する（参加看板と同等） |
| `/yukigassen leave` | ロビーから退出する（退出看板と同等。ソロも共通） |
| `/yukigassen start` | ゲームを開始する（開始看板と同等。コマブロ/コンソール可） |
| `/yukigassen solo [モード] [難易度]` | ソロモードに参加する／メニューを開く（ソロ看板と同等） |
| `/yukigassen ranking solo <モード> [難易度]` | ソロのランキングを確認する |
| `/yukigassen status` | 設定状況（地点・看板登録数・人数・ゲーム状態・CPU/ソロ状況）を確認する |
| `/yukigassen stats [名前]` | 成績（キル/デス/勝利/試合数）を確認する（名前なし＝自分） |

!!! note "他プレイヤー指定はadmin権限"
    `join` / `leave` / `solo` に **対象（プレイヤー名や `@a` などのセレクタ）を指定する場合のみ** `yukigassen.admin` が必要です。引数を省略すると実行者自身が対象になり、権限は不要です。`start` は誰でも実行できます。`teamjoinred` / `teamjoinblue` というコマンドは存在しません（チーム振り分けは開始時に自動）。

### 管理用（`yukigassen.admin`）

| コマンド | 説明 |
|---|---|
| `/yukigassen join <player>` | 対象（プレイヤー名／`@a` 等のセレクタ）を参加させる |
| `/yukigassen leave <player>` | 対象（プレイヤー名／`@a` 等のセレクタ）を離脱させる |
| `/yukigassen solo <player> [モード] [難易度]` | 対象をソロに参加させる／メニューを開かせる（コンソール・コマブロ可） |
| `/yukigassen ranking reset <player\|all> [モード\|all] [難易度\|all]` | ソロの記録を消す |
| `/yukigassen stop` | 試合を強制停止する（在籍者はロビーへ。在籍は維持） |
| `/yukigassen wand` | 管理ツール（ブレイズロッド）を受け取る（右クリックで管理メニュー） |
| `/yukigassen menu` | 管理メニューを開く（在籍中も可） |
| `/yukigassen reload` | config.yml を再読み込みする（試合中は不可） |
| `/yukigassen forceunlock <player> [confirm]` | 参加中フラグの強制解除・退避データの復元 |
| `/yukigassen setzanki <数>` | 初期残機数を設定する（config に保存） |
| `/yukigassen setmax <数>` | 最大参加人数を設定する（config に保存） |
| `/yukigassen setmin <数>` | 最小開始人数を設定する（config に保存） |
| `/yukigassen setcpu <on\|off>` | CPUチーム戦（人数不足を CPU で補充）を切り替える（次の試合から） |
| `/yukigassen setstartspawn` | 初期リスポーン地点を設定する |
| `/yukigassen setlobby` | ロビー地点を設定する |
| `/yukigassen setspawn <red\|blue>` | チームのスポーン地点を設定する |
| `/yukigassen setfield <1\|2>` | ゲームエリアの角を設定する |
| `/yukigassen setsign <join\|leave\|start\|solo>` | 視線先の看板を参加/退出/開始/ソロ看板として登録する |
| `/yukigassen setsign delete` | 視線先の登録看板を解除する（テキストも消去） |

!!! tip "管理ツール（wand）と管理メニュー"
    `/yukigassen wand` で受け取るブレイズロッドを右クリックすると管理メニュー（`/yukigassen menu` と同じ GUI）が開き、地点・看板の設定や各種調整をコマンドを打たずに行えます。

## 運営の流れ

1. プレイヤーがコマンドまたはロビー看板から参加し、人数が集まるのを待つ。
2. 必要に応じて `/yukigassen setzanki <数>`・`/yukigassen setmax <数>`・`/yukigassen setmin <数>` で残機・人数を調整する。
3. `/yukigassen start` でゲームを開始する（赤・青スポーン設定が前提）。
4. 相手チーム全滅で自動的に勝敗が決まり、全員がロビーへ戻る。
5. 異常時は `/yukigassen stop` で強制停止できる。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `yukigassen.admin` | OP | stop・wand・menu・reload・forceunlock・setzanki・setmax・setmin・setcpu・setstartspawn・setlobby・setspawn・setfield・setsign（delete含む）・ranking reset、および join/leave/solo での他プレイヤー指定。`join`/`leave`/`start`/`solo`（自分対象）/`ranking solo`/`status`/`stats`（自分対象）は権限不要 |

## トラブルシューティング

??? failure "`/yukigassen start` でゲームが開始できない"
    赤・青チームのスポーン地点が未設定の可能性があります。`/yukigassen setspawn red` と `/yukigassen setspawn blue` を実行してください。また、ロビー参加者が最小開始人数（`min-players`・既定1）未満の場合も開始できません。`/yukigassen setmin <数>` で調整できます。

??? failure "プレイヤーがエリア外に弾かれない"
    `locations.area.pos1` / `pos2` が未設定だと、エリア判定が常に有効（マップ全体OK）になります。`/yukigassen setfield 1` と `/yukigassen setfield 2` でエリアの2点を設定してください。

??? failure "参加看板が機能しない"
    看板は `/yukigassen setsign <join|leave|start|solo>` で登録した **保存座標** で判定されます（手書きテキストでは機能しません）。看板が登録済みか、`/yukigassen status` の参加/離脱/開始/ソロ看板の登録数で確認してください。看板の登録には `yukigassen.admin` 権限が必要です。

??? failure "ゲーム終了後にプレイヤーがロビーに戻らない"
    `locations.lobby` が未設定の可能性があります。`/yukigassen setlobby` でロビー地点を設定してください。

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
| `messages.vault-save-failed` | 持ち物の退避に失敗して参加を中止したときの文言 |

!!! note "config は自動で追記されるようになりました"
    起動時（`reload` 対応プラグインは reload 時も）に、`config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は `config.yml.bak` を保存）。座標・看板・会場・ステージなどのデータ領域は補完対象外です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Yukigassen 概要へ](index.md){ .md-button }
