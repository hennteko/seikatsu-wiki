<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Yukicraft ― OP・運営ガイド { .page-op #yukicraft-op }

Yukicraft の導入・地点セットアップ・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Yukicraft |
| api-version | 26.1.2 |
| メインコマンド | `/yukicraft <join\|leave\|start\|status\|solo\|ranking\|stop\|setstartspawn\|setlobby\|setspawn\|setfield\|setsign\|reload\|lobbyfix>` |
| 依存プラグイン | なし（統合版対応のため Floodgate / Geyser-Spigot があれば自動連携する softdepend） |
| 設定ファイル | `plugins/Yukicraft/config.yml`／ソロの記録 `plugins/Yukicraft/solo-records.yml`／ロビー退避 `plugins/Yukicraft/lobby-inventory.yml` |

## 導入手順

1. ビルドした `Yukicraft` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Yukicraft/config.yml` が自動生成される。
3. 後述の手順で地点・エリア座標を設定する。
4. 設定を変更したら `/yukicraft reload` で再読み込みする。

## config.yml 設定項目

`config.yml` の各項目は次のとおりです。地点・エリアは基本的にセットアップコマンド（`setstartspawn` / `setlobby` / `setspawn` / `setfield`）で設定しますが、直接編集も可能です。

### 地点設定

| キー | 説明 |
|---|---|
| `spawn` | 初期リスポーン地点。離脱時の移動先（world / x / y / z / yaw / pitch） |
| `lobby` | ロビー地点。参加時・ゲーム終了時の移動先 |
| `game-spawn` | ゲーム開始時のスポーン地点（雪フィールド上空に設定する） |

### エリア設定（`arena`）

| キー | 既定値 | 説明 |
|---|---|---|
| `arena.world` | `world` | ゲームエリアのワールド名 |
| `arena.min-x` / `max-x` | 1075 / 1099 | エリアのX範囲（`setfield 1`/`setfield 2` で設定） |
| `arena.min-z` / `max-z` | 110 / 134 | エリアのZ範囲（`setfield 1`/`setfield 2` で設定） |
| `arena.snow-layers` | 5,10,15,20,25 | 雪を生成するY座標のリスト（各層の高さ） |
| `arena.clear-min-y` | 5 | マップリセット時にクリアする高さの下限 |
| `arena.clear-max-y` | 25 | マップリセット時にクリアする高さの上限 |

!!! note "マップリセットの挙動"
    ゲーム開始時、エリア内の `clear-min-y`〜`clear-max-y` の範囲がリセットされます。`snow-layers` に含まれるYは雪ブロックに、それ以外は空気になります（バリアブロックはそのまま保持されます）。

### ゲーム設定（`game`）

| キー | 既定値 | 説明 |
|---|---|---|
| `game.max-players` | 16 | 最大参加人数 |
| `game.result-seconds` | 10 | ゲーム終了後、結果発表してからロビーへ戻るまでの秒数 |

!!! note "旧キー `game.end-delay` からの自動移行"
    旧バージョンの `game.end-delay` は、プラグイン起動時に自動で `game.result-seconds` へ移行され、旧キーは削除されます。手動での書き換えは不要です。

### クリーパー設定（`creeper`）

| キー | 既定値 | 説明 |
|---|---|---|
| `creeper.enabled` | true | クリーパー出現の有効化 |
| `creeper.interval` | 30 | 出現間隔（秒） |
| `creeper.count` | 5 | 1回の出現数 |
| `creeper.block-damage` | true | 爆発による地形破壊を許可（true でも雪ブロックのみ破壊。false で破壊なし） |

### ソロモード設定（`solo`）

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | true | false でソロを無効にする（`/yukicraft solo`・ソロ看板から入れなくなる） |
| `solo.menu-delay` | 3 | ソロ終了後、ソロメニューを開くまでの秒数 |
| `solo.countdown` | 3 | 開始前のカウントダウン（秒）。この間は動けない |
| `solo.block-when-lobby-waiting` | false | true にすると、通常戦の参加者がロビーで待っている間はソロを始められない |
| `solo.broadcast-result` | true | ソロの結果をサーバー全体に1行で流す |
| `solo.ranking-size` | 10 | `/yukicraft ranking solo` で表示する人数 |
| `solo.modes.dig.display` | 掘削スコア | メニュー・看板に出るモード名 |
| `solo.modes.dig.icon` | DIAMOND_SHOVEL | メニューのボタンのアイテム |
| `solo.modes.dig.time-limit` | 120 | 制限時間（秒） |
| `solo.modes.dig.creeper.first-delay` | 10 | 開始から最初の乱入まで（秒） |
| `solo.modes.dig.creeper.interval` | 15 | 乱入の間隔（秒） |
| `solo.modes.dig.creeper.start-count` | 1 | 1回に出る数（最初） |
| `solo.modes.dig.creeper.add-every` | 40 | 何秒ごとに1体増えるか（0 で増えない） |
| `solo.modes.dig.creeper.max-count` | 3 | 1回に出る数の上限 |
| `solo.modes.dig.creeper.min-distance` / `max-distance` | 5 / 10 | 挑戦者からの距離（ブロック） |
| `solo.modes.dig.creeper.block-damage` | true | 爆発で雪を壊す（壊れた分は点にならない） |

- `solo.modes` の下にモードを追加すると、ソロメニューの上段に並びます（ランキングはモードごと）。
- 通常戦の `creeper:` とは別の設定です（ソロは `solo.modes.<モード>.creeper` を使います）。
- プラグイン更新後の初回起動で、`solo:` ブロックは既存の config.yml に **自動で追記** されます（設定済みの値は上書きしません。旧ファイルは `config.yml.bak-日時` に退避）。

### メッセージ設定（`messages`）

`messages` 以下に各種メッセージ文字列を定義します。`prefix` のほか、`join` / `leave` / `game-start` / `game-stop` / `eliminated` / `winner` / `creeper-spawn` / `game-ending` などがあり、`{player}` `{count}` `{seconds}` のプレースホルダーが使えます。`§` で色コードを指定できます。

!!! note "メッセージの自動補正"
    プラグイン起動時、統合版で文字化けしやすい一部の記号（`⚠`→`※`、`▶`→`→` など）は既存 config のメッセージからも安全な文字へ自動置換されます。なお `messages.join` は現在使用しておらず、参加通知は後述の `name-tag.announce-join` が送ります。

### ロビーの持ち物退避（`lobby-inventory`）

ロビーに入ると、入る前の状態（インベントリ・防具・ゲームモード・体力・経験値など）を退避してインベントリを空にし、ロビーから出ると完全に元へ戻します。退避データは `lobby-inventory.yml` に保存され、サーバー再起動やクラッシュをまたいでも次回ログイン時に復元されます。

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | false でこの機能を丸ごと無効化（従来どおりの挙動に戻る） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE / SURVIVAL / CREATIVE / KEEP。KEEP は変更しない） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し、体力・満腹度を全回復する |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値とレベルを0にする |
| `lobby-inventory.expire-days` | 30 | 復元されないまま残った退避データの保持日数 |

!!! note "持ち物が戻らないとき"
    持ち物が戻らないプレイヤーがいる場合、OP が `/yukicraft lobbyfix <プレイヤー>` で復旧できます。`force` を付けると退避データを破棄してロックだけ解除します。`/yukicraft status` の「ロビー退避(未復元)」で未復元の件数を確認できます。

### 参加中の名前表示・参加通知（`name-tag`）

参加すると、頭上ネームタグ・Tabリスト・チャットの名前が「【雪クラフト】名前」になり、離脱・ログアウト・プラグイン停止で元に戻ります。参加時にはサーバー全体へ参加通知を出します。

| キー | 既定値 | 説明 |
|---|---|---|
| `name-tag.enabled` | true | false にすると名前表示を付けない |
| `name-tag.display-name` | 雪クラフト | 【】に入れる正式名 |
| `name-tag.color` | AQUA | 【ゲーム名】の色（GOLD / AQUA / GREEN / RED / YELLOW など） |
| `name-tag.announce-join` | true | false にするとサーバー全体への参加通知を出さない |

### 統合版対応（`bedrock`）

Geyser / Floodgate 経由の統合版プレイヤー向けの調整です。

| キー | 既定値 | 説明 |
|---|---|---|
| `bedrock.wax-signs` | true | 登録看板を蝋引きし、統合版でタップしても編集画面が開かないようにする |
| `bedrock.spectator-style` | AUTO | 観戦方式（AUTO＝統合版だけゴースト観戦／GHOST＝全員ゴースト／VANILLA＝全員観戦モード） |
| `bedrock.name-prefix` | `.` | Floodgate が入っていないときに統合版と見なす名前の接頭辞 |

## セットアップ手順

専用ワールドを用意し、OP権限で以下のコマンドを **その場に立って** 実行します（実行位置が座標として保存されます）。

```text title="初期リスポーン地点（離脱時の戻り先）"
/yukicraft setstartspawn
```

```text title="ロビー地点（参加時・終了時の戻り先）"
/yukicraft setlobby
```

```text title="ゲーム開始スポーン地点（雪フィールド上空）"
/yukicraft setspawn
```

```text title="ゲームエリアの角1"
/yukicraft setfield 1
```

```text title="ゲームエリアの角2"
/yukicraft setfield 2
```

!!! tip "セットアップのポイント"
    `setfield 1`/`setfield 2` はエリアのX・Z範囲のみを記録します（高さ範囲は config の `clear-min-y` / `clear-max-y` と `snow-layers` で別途指定）。`setspawn` は雪フィールドより十分上空に設定すると、開始時に安全に着地できます。設定後は `/yukicraft reload` を実行してください。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/yukicraft join` / `leave` / `start` | 全員 | 参加 / 退出 / 開始（看板と同等。引数省略で自分） |
| `/yukicraft status` | 全員 | 設定状況・ゲーム状態を確認する |
| `/yukicraft join <対象>` / `leave <対象>` | `yukicraft.admin` | 対象を指定して参加／離脱させる |
| `/yukicraft stop` | `yukicraft.admin` | ゲームを強制終了する |
| `/yukicraft setstartspawn` | `yukicraft.admin` | 初期リスポーン地点を設定する |
| `/yukicraft setlobby` | `yukicraft.admin` | ロビー地点を設定する |
| `/yukicraft setspawn` | `yukicraft.admin` | ゲーム開始スポーン地点を設定する |
| `/yukicraft setfield <1\|2>` | `yukicraft.admin` | ゲームエリアの角を設定する |
| `/yukicraft setsign <join\|leave\|start\|solo\|delete>` | `yukicraft.admin` | 視線先の看板を参加／離脱／開始／ソロ看板として登録、または登録解除する |
| `/yukicraft reload` | `yukicraft.admin` | config.yml を再読み込みする（看板位置も再読込） |
| `/yukicraft lobbyfix <プレイヤー> [force]` | `yukicraft.admin` | ロビーの持ち物が戻らないときの復旧（`force` で退避データを破棄してロック解除） |
| `/yukicraft solo` | 全員 | ソロで在籍してソロメニューを開く（在籍中なら開き直す） |
| `/yukicraft solo <モード>` | 全員 | メニューを通さずソロを開始する（例：`/yukicraft solo dig`） |
| `/yukicraft ranking solo [モード]` | 全員 | ソロのランキング（上位 `ranking-size` 人と自分） |
| `/yukicraft solo <プレイヤー> [モード]` | `yukicraft.admin` | 指定した人をソロへ（モード指定で即開始。コンソール・コマンドブロックからも可） |
| `/yukicraft ranking reset <プレイヤー\|all> [モード\|all]` | `yukicraft.admin` | ソロの記録を消す（フィールドの大きさや層を変えたとき用。コンソールからも可） |
| `/yukicraft stop` | `yukicraft.admin` | ソロ挑戦中に実行すると、ソロを中断する（記録なし。挑戦者はロビーへ戻り、ソロメニューが開く） |

!!! note "看板による参加導線"
    ロビーに看板を設置し、**その看板を見た状態**で以下のコマンドを実行します（`yukicraft.admin` 権限が必要）。

    ```text title="参加看板として登録（1行目 [Yukicraft]・2行目 lobby・4行目に参加人数表示）"
    /yukicraft setsign join
    ```

    ```text title="離脱看板として登録（2行目 leave）"
    /yukicraft setsign leave
    ```

    ```text title="開始看板として登録（クリックでゲーム開始）"
    /yukicraft setsign start
    ```

    ```text title="ソロ看板として登録（クリックでソロメニュー。4行目に空き／挑戦中を表示）"
    /yukicraft setsign solo
    ```

    ```text title="看板の登録を解除（種別を自動判別・テキストもクリア）"
    /yukicraft setsign delete
    ```

    コマンドを実行すると、プラグインがテキストを自動書き込みし、位置を config（`signs.join` / `signs.leave` / `signs.start` / `signs.solo`）に保存します。手書きでのテキスト入力は不要です。参加・離脱・開始はすべて `setsign <join\|leave\|start>` で登録し、`setsign delete` で登録解除できます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `yukicraft.admin` | OP | stop / setstartspawn / setlobby / setspawn / setfield / setsign / reload / lobbyfix / join &lt;対象&gt; / leave &lt;対象&gt; / solo &lt;プレイヤー&gt; / ranking reset（管理者コマンドすべて） |

## ゲームの運営

1. プレイヤーが参加看板のクリックまたは管理者による `/yukicraft join` でロビーに集まる。
2. 人数がそろったら `/yukicraft start` で開始する。
3. 異常時は `/yukicraft stop` で強制終了する（`result-seconds` 秒後に全員ロビーへ戻る）。

### ソロモードの運営

- アリーナは通常戦と共用です。ソロ挑戦中は通常戦を開始できず、通常戦の試合中はソロを始められません（理由は本人に表示されます）。
- 在籍者は「通常」と「ソロ」に分かれます。`/yukicraft join`・参加看板で入った人だけが通常戦の参加者になり、ソロで入った人は巻き込まれません。ソロで在籍中に `join` すると通常に、通常で在籍中に `solo` するとソロに切り替わります。
- 試合中に `join` した人はロビー在籍として受け付け、次の試合から参加します。
- 参加看板の人数表示と `max-players` の定員は、通常で在籍している人だけで数えます。
- `/yukicraft status` の「ソロ」欄で、ソロ看板の数・挑戦中の人（残り時間とスコア）・記録件数を確認できます。
- ソロの記録は会場の大きさ・層の数で変わります。フィールドを作り直したときは `/yukicraft ranking reset all` で記録を消してください。

## トラブルシューティング

??? failure "ゲームを開始できない"
    `/yukicraft start` は参加者が1人以上いないと開始できません。プレイヤーがロビーに参加しているか確認してください。すでにゲームが進行中の場合も開始できません。

??? failure "開始しても雪フィールドが生成されない／おかしい"
    `arena` のエリア座標（`area pos1/pos2`）と `snow-layers`・`clear-min-y`・`clear-max-y` の設定を確認してください。マップリセットはこのエリア・高さ範囲に対して行われます。範囲が未設定だと雪が生成されません。設定変更後は `/yukicraft reload` を実行してください。

??? failure "プレイヤーが看板で参加できない"
    `/yukicraft setsign join`（または `leave`）を、登録したい看板を見ながら実行してください（`yukicraft.admin` 権限が必要）。

??? failure "クリーパーが地形を壊しすぎる"
    `creeper.block-damage` を `false` にすると、クリーパー爆発による地形破壊が無効になります（爆発エフェクトとプレイヤーへのダメージは残ります）。`true` の場合でも雪ブロックのみが破壊対象です。

??? failure "ソロを始められない"
    ソロメニューの開始ボタンがバリアになっているときは、表示されている理由を確認してください。「フィールド未設定」なら `setfield 1`・`2` を、「通常戦の参加者が待機中」なら `solo.block-when-lobby-waiting` を確認します。ソロ挑戦が止まらないときは `/yukicraft stop` で中断できます。

??? failure "クリーパーを出したくない"
    `creeper.enabled` を `false` にするとクリーパーは出現しなくなります。設定後は `/yukicraft reload` を実行してください。

??? failure "ロビーの持ち物が戻らない"
    ロビーに入る前の持ち物は `lobby-inventory.yml` に退避され、ロビー退出・ログアウト・プラグイン停止で復元されます。復元されないプレイヤーがいる場合は OP が `/yukicraft lobbyfix <プレイヤー>` を実行してください。それでも直らない場合は `/yukicraft lobbyfix <プレイヤー> force` で退避データを破棄し、ロックだけ解除できます（持ち物は戻りません）。未復元の件数は `/yukicraft status` で確認できます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Yukicraft 概要へ](index.md){ .md-button }
