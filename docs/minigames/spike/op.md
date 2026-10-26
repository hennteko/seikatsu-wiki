<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# SPIKE ― OP・運営ガイド { .page-op #spike-op }

SPIKE の導入・マップ設定・看板・config・権限・管理コマンドをまとめます。地点・看板・マップはコマンドで登録すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Spike |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/spike` |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/Spike/config.yml` |
| 権限ノード | `spike.admin`（既定OP） |

## 導入手順

1. ビルドした `Spike` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される（ショップ価格・ラウンド数などは初期値で動作）。
3. `/spike setstartspawn`・`/spike setlobby` で初期スポーン（途中抜けの戻り先）とロビーを設定する。
4. `/spike map <マップ>` でマップを作り、スポーン・購入境界・スパイク初期地点・設置サイトを設定する。
5. 参加・離脱・編成・開始の看板を設置する。
6. `/spike status` で設定状況（開始可能かどうか）を確認する。

!!! note "試合に必須のマップ設定"
    1つのマップを開始可能にするには、**攻撃側/防衛側スポーン・攻撃側/防衛側の購入境界・スパイク初期地点・設置サイト（1つ以上）** がすべて必要です。試合範囲（`setfield`）は任意です。未設定の項目は `/spike status` に表示されます。

## セットアップ手順（コマンド）

地点・境界・サイト・スパイク地点は **実行した位置** を使い、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `spike.admin` 権限が必要です。マップ系の設定は、先に `/spike map <マップ>` で編集対象を選んでから行います。

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/spike setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/spike setlobby
```

```text title="編集するマップを選ぶ（未登録なら作成）"
/spike map <マップ>
```

```text title="攻撃側スポーン（その場に立って実行）"
/spike setspawn attack
```

```text title="防衛側スポーン（その場に立って実行）"
/spike setspawn defense
```

```text title="攻撃側 購入境界 角1（その場に立って実行）"
/spike setbarrier attack 1
```

```text title="攻撃側 購入境界 角2（その場に立って実行）"
/spike setbarrier attack 2
```

```text title="防衛側 購入境界 角1（その場に立って実行）"
/spike setbarrier defense 1
```

```text title="防衛側 購入境界 角2（その場に立って実行）"
/spike setbarrier defense 2
```

```text title="スパイクの初期配置地点（その場に立って実行）"
/spike setspike
```

```text title="設置サイト A 角1（その場に立って実行）"
/spike setsite A 1
```

```text title="設置サイト A 角2（その場に立って実行）"
/spike setsite A 2
```

```text title="設置サイトを削除"
/spike delsite A
```

```text title="試合範囲 角1（任意・その場に立って実行）"
/spike setfield 1
```

```text title="試合範囲 角2（任意・その場に立って実行）"
/spike setfield 2
```

```text title="参加看板を登録（看板を見て実行）"
/spike setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/spike setsign leave
```

```text title="編成看板（役職選択）を登録（看板を見て実行）"
/spike setsign weapon
```

```text title="開始看板を登録（マップ指定・看板を見て実行）"
/spike setsign start <マップ>
```

```text title="ソロ看板を登録（看板を見て実行）"
/spike setsign solo
```

```text title="視線先の看板の登録を解除"
/spike setsign delete
```

!!! note "設置サイト・試合範囲について"
    設置サイトは `A`・`B` のように名前を付けて2点（対角）で範囲指定します。複数サイトを登録できます。試合範囲（`setfield`）を設定すると、範囲外に出た生存者はスポーンへ戻されます（任意設定）。購入境界は購入フェーズ中に各チームが出られない水平範囲です。マップが試合で使用中のときは変更・削除できません。

## 設定ツール・設定メニュー（コマンドを打たない設定）

コマンドを打たずに、**設定ツール（ブレイズロッド）** と **設定メニュー（GUI）** でも地点・範囲・看板を設定できます。どちらも `spike.admin` 権限が必要です。

```text title="設定ツール（ブレイズロッド）を受け取る"
/spike wand
```

```text title="設定メニュー（GUI）を開く"
/spike menu
```

- **設定メニュー** … 編集するマップの選択、初期スポーン／ロビー／攻撃側・防衛側スポーン／スパイク初期地点の「現在地を設定」、各範囲モード・看板モードの切り替え、設置サイトの追加・削除、開始（ランダム／自由選択）・強制停止・設定状況の表示・範囲表示のON/OFF・設定ツールの回収を、GUIからまとめて操作できます。メニューは `/spike menu` のほか、設定ツールを **しゃがみ＋右クリック（統合版はしゃがみ＋タップ）** でも開けます。
- **範囲モード**（攻撃側／防衛側の購入境界・設置サイト・試合範囲）… メニューでモードを選び、設定ツールでブロックを **左クリック＝角1／右クリック＝角2**（統合版は長押し＝角1／タップ＝角2）で指定します。
- **看板モード**（参加／離脱／編成／開始／解除）… メニューでモードを選び、設定ツールで看板を **右クリック／タップ** して登録・解除します。
- 設定ツールを持っている間は、編集中マップの範囲・地点が **本人にだけパーティクル表示** されます（橙＝攻撃側／水色＝防衛側／赤＝サイト・スパイク／白＝試合範囲。メニューでON/OFF 切替）。設定ツールで空中を右クリック／タップすると設定状況の要約が出ます。

!!! note "設定ツールの注意"
    設定ツールはOP専用で、受け取り時・使用時の両方で `spike.admin` を確認します（非OPが入手しても使えません）。SPIKE に参加中は受け取れないので、先に `/spike leave` で離脱してから実行してください。不要になったら設定メニューの「設定ツールを回収」で片付けられます。

## ソロ解除チャレンジの設定

ソロ解除チャレンジ（`/spike solo`）は、選んだ **マップ** 上にスパイクと警備Mobを配置して遊びます。先に `/spike map <マップ>` で編集対象を選んでから、以下を **現在地** で登録します（すべて `spike.admin` 権限）。地点はマップごとに保存されます。

```text title="スパイクの初期配置地点を追加（現在地。複数登録でき、挑戦ごとにいずれかが選ばれる）"
/spike setsolospike
```

```text title="見張り（その場で首を振る警備）の配置地点を追加（現在地と向き）"
/spike addpost
```

```text title="巡回兵（ルートを歩く警備）のルートに地点を追加（現在地）"
/spike addroute <ルート>
```

```text title="ソロの地点を削除（spike=スパイク / post=見張り / route <ルート> / all=全部）"
/spike clearsolo spike|post|route <ルート>|all
```

!!! note "ソロの配置について"
    スパイク地点は1つ以上必要です（複数登録すると挑戦ごとに1つ選ばれます）。見張り（post）と巡回兵（route）は任意で、難易度の `guard-ratio` に応じて配置数が決まります。番兵（sentinel）はスパイクの周囲に `solo.sentinel-radius` の範囲で自動配置されるため、地点登録は不要です。巡回ルートは隣り合う地点が `solo.route-warn-distance`（既定24ブロック）より離れていると警告が出ます。`/spike clearsolo all` はプレイヤーが実行した場合、3秒以内に2回実行すると確定します。

## config.yml 設定項目

コマンドで登録する地点・看板・マップのデータ（`default-spawn`・`lobby-spawn`・各 `*-sign`・`maps`）は自動保存されます。手書き不要です。先頭の `config-version` は設定ファイルの版（項目の名前変更・削除時の移行用）で、**手で変えないでください**。以下は数値・経済などの設定項目です。

### 試合（ラウンド・人数・時間）

| キー | 既定値 | 説明 |
|---|---|---|
| `max-players` | 0 | 定員（0 = 無制限） |
| `team-mode-default` | random | 方式省略時・開始看板からのチーム分け（random / select） |
| `countdown-seconds` | 5 | random：チーム分け前のカウントダウン（秒） |
| `team-select-seconds` | 20 | select：チーム選択フェーズの長さ（秒）。時間切れ時の未選択者は少人数チームへ |
| `win-rounds` | 5 | 何ラウンド先取で勝利か（延長なし） |
| `buy-phase-seconds` | 15 | 購入フェーズ（秒） |
| `round-seconds` | 100 | 戦闘フェーズ（秒）。時間切れは防衛側の勝利 |
| `spike-timer-seconds` | 45 | スパイク設置から爆発まで（秒） |
| `interval-seconds` | 5 | ラウンド決着から次の購入フェーズまで（秒） |
| `result-seconds` | 5 | 試合終了後、結果を見せてからロビーへ戻すまで（秒） |

!!! note "開始に必要な人数"
    開始には最低2人の在籍が必要で、両チームに最低1人ずつ割り当てられます。全員が役職を選ぶまで試合は始まりません。

### スパイク（`spike`）

| キー | 既定値 | 説明 |
|---|---|---|
| `spike.plant-seconds` | 4.0 | 設置にかかる時間（秒）。スニーク解除・移動・持ち替え・サイト外で0に戻る |
| `spike.defuse-seconds` | 7.0 | 解除にかかる時間（秒） |
| `spike.defuse-half-seconds` | 3.5 | ハーフ解除の地点（秒）。ここまで到達していれば中断しても次回継続 |
| `spike.defuse-range` | 1.2 | 解除できる距離（スパイクのブロック中心から・ブロック） |
| `spike.explosion-radius` | 8.0 | 爆発範囲（ブロック）。範囲内の生存者は敵味方問わず即死（ブロックは壊さない） |

### 戦闘（`combat`）

| キー | 既定値 | 説明 |
|---|---|---|
| `combat.base-health` | 20.0 | 素の最大体力（HP。20 = 10ハート） |
| `combat.magazine-size` | 25 | 銃の装弾数 |
| `combat.reload-seconds` | 2.0 | リロード時間（秒） |
| `combat.sword-damage` | 4.0 | 木の剣の1撃のダメージ（HP。4 = 2ハート） |
| `combat.sword-speed-bonus` | 0.10 | 木の剣を持っている間の移動速度アップ（0.10 = +10%） |
| `combat.kill-assist-seconds` | 10 | 死亡時、この秒数以内に最後にダメージを与えた敵をキルにする |
| `combat.hide-name-tags` | true | 試合中は全員のネームタグを消す（false にすると味方のネームタグだけ見える） |

### 銃（`gun`）

戦闘の主武器はヒットスキャンの銃（見た目は鉄のクワ）。弾は重力で落ちず、撃った瞬間にまっすぐ届きます。

| キー | 既定値 | 説明 |
|---|---|---|
| `gun.fire-interval-ticks` | 3 | 連射の間隔（tick。20=1秒。3=毎秒約6.7発）。右クリック長押し中この間隔で撃つ |
| `gun.range` | 64 | 射程（ブロック） |
| `gun.body-damage` | 4.0 | 体に当たったときのダメージ（HP。4=2ハート） |
| `gun.headshot-multiplier` | 3.0 | 頭に当たったときの倍率（体のダメージ × この値） |
| `gun.head-size` | 0.4 | 頭の当たり判定の一辺（ブロック。目の位置が中心。バニラの頭は約0.5） |
| `gun.spread.standing` / `moving` / `airborne` | 0.3 / 5.0 / 9.0 | ブレ（度）。止まって撃つ／歩き（走ると最大）／空中 |
| `gun.spread.sneak-multiplier` | 0.5 | スニーク中にブレへ掛ける倍率 |
| `gun.move-speed-while-firing` | 1.0 | 撃っている間の移動速度（1.0=ふだん通り）。Java版のみ |
| `gun.can-sprint` | true | 撃っている間もダッシュできるか。Java版のみ |
| `gun.bedrock-burst` | 3 | 統合版：タップ1回で撃つ弾数（統合版は長押しの連射が届かないため） |
| `gun.tracer` | true | 弾道のパーティクルを出す |
| `gun.item-model` | `minecraft:iron_hoe` | 銃の見た目（item_model）。リソースパックで専用モデルを指定可。統合版は鉄のクワの見た目 |

### クレジット（`credits`）

| キー | 既定値 | 説明 |
|---|---|---|
| `credits.start` | 80 | 試合開始時のクレジット |
| `credits.max` | 900 | 所持上限 |
| `credits.round-win` | 100 | ラウンド勝利の報酬 |
| `credits.loss-streak` | `[60, 80, 100]` | 連敗ボーナス [1連敗, 2連敗, 3連敗以降] |
| `credits.kill` | 10 | キル報酬 |
| `credits.plant` | 10 | スパイク設置で攻撃側全員（死亡中を含む）に |

### ショップ（アーマー）（`armor`）

| キー | 既定値 | 説明 |
|---|---|---|
| `armor.light.price` | 40 | ライトアーマーの価格 |
| `armor.light.hearts` | 5 | ライトアーマーの追加ハート |
| `armor.regen.price` | 65 | 回復アーマーの価格 |
| `armor.regen.hearts` | 5 | 回復アーマーの追加ハート |
| `armor.regen.regen-hearts-per-round` | 5 | 1ラウンドに自己回復できる合計（ハート） |
| `armor.regen.regen-delay-seconds` | 5 | 最後に被弾してから回復が始まるまで（秒） |
| `armor.heavy.price` | 100 | ヘヴィアーマーの価格 |
| `armor.heavy.hearts` | 10 | ヘヴィアーマーの追加ハート |

### ULT（`ult`）

| キー | 既定値 | 説明 |
|---|---|---|
| `ult.required-points` | 5 | ULT の使用に必要なポイント（キル・設置・解除・被撃破で貯まる） |

### ソロ解除チャレンジ（`solo`）

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | true | ソロ解除チャレンジの有効化（false でメニュー・看板・コマンドすべて受け付けない） |
| `solo.countdown-seconds` | 3 | 開始前に動けない時間（秒） |
| `solo.result-seconds` | 3 | 結果を見せてからロビーへ戻すまで（秒） |
| `solo.menu-delay` | 2 | ロビーへ戻ってからソロメニューを開くまで（秒） |
| `solo.default-difficulty` | normal | `/spike solo defuse` で難易度を省略したときの難易度 |
| `solo.announce-top` | true | 1位を更新したらサーバー全体に知らせる |
| `solo.route-warn-distance` | 24 | 巡回ルートの隣り合う地点がこれより離れていたら警告（ブロック） |
| `solo.sentinel-radius` | 4 | 番兵を出す範囲（スパイクからの半径・ブロック） |
| `solo.guard.patrol` | hp 20 / damage 3 | 巡回兵（ピリジャー・クロスボウ）の基本値 |
| `solo.guard.lookout` | hp 20 / damage 3 / sweep-degrees 60 / sweep-seconds 4 | 見張り（スケルトン・弓）。首振り角度・1往復の秒数 |
| `solo.guard.sentinel` | hp 24 / damage 6 | 番兵（ヴィンディケーター・斧）の基本値 |
| `solo.guard.sneak-range-multiplier` | 0.6 | スニーク中の視界距離の倍率 |
| `solo.guard.noise-radius` | 6 | 物音（ダッシュ・外れた弾）で振り向く距離（ブロック） |
| `solo.guard.call-radius` | 12 | 見つけた警備が仲間を呼ぶ距離（ブロック） |
| `solo.guard.lose-seconds` | 5 | 見失ってから捜索に入るまで（秒） |
| `solo.guard.search-seconds` | 5 | 捜索をあきらめるまで（秒） |
| `solo.difficulties.<id>` | easy/normal/hard | 難易度の定義（下表）。キーを足すとメニューのボタンが増える。キーは記録にも使う |

各難易度は `display-name`・`icon`・`time-limit`（制限時間・秒）・`guard-ratio`（巡回・見張りを配置する割合）・`sentinels`（番兵の数）・`vision-range`／`vision-angle`（警備の視界）・`detect-seconds`（見つかるまでの秒数）・`damage-multiplier`（警備の攻撃力倍率）・`armor`（挑戦者の装備 none/light/regen/heavy）・`respawn-seconds`（倒されてから復帰まで）・`show-vision`（警備の視界を表示するか）を持ちます。既定は easy（かんたん）・normal（ふつう）・hard（むずかしい）の3段階です。

ソロの記録は `plugins/Spike/solo-records.yml` にプレイヤー × マップ × 難易度ごとに保存され、`/spike ranking solo` で参照できます。

## ショップ品目

購入フェーズ中、生存中のプレイヤーだけが利用できます。購入の取り消し・返金はありません。価格・追加ハートは上記 `armor` の config に連動します。

| 品目 | アイコン | 価格 | 追加ハート | 備考 |
|---|---|---|---|---|
| ライトアーマー | 鉄のチェストプレート | 40 | +5 | 今のアーマーと入れ替え |
| 回復アーマー | 金のチェストプレート | 65 | +5 | しばらく被弾しないと回復（1ラウンド合計5ハートまで） |
| ヘヴィアーマー | ダイヤのチェストプレート | 100 | +10 | 今のアーマーと入れ替え |

## ロール・サイド

- **ロール（役職）** … デュエリスト（ジェット型）・イニシエーター（ソーヴァ型）・コントローラー（ブリムストーン型）・センチネル（セージ型）の4種。同じ役職を何人でも選べます。各役職に無料スキル・購入スキル・ULT が設定されています。役職は在籍中は試合をまたいで保持され、全員が選ぶまで試合は開始できません。
- **サイド** … 攻撃側（attack）・防衛側（defense）。ラウンドごとに赤/青チームへ割り当てが入れ替わります。コマンド引数・config のキーには `attack` / `defense` を使います。
- **チーム** … 赤チーム・青チームの2チーム。チーム色の革防具は見た目のみで防御力はありません（アーマーの黄色いハートで防御を表現）。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/spike setstartspawn` | 初期スポーン（途中抜けの戻り先）を設定 |
| `/spike setlobby` | ロビー地点を設定 |
| `/spike map <マップ>` | 編集するマップを選ぶ（未登録なら作成） |
| `/spike delmap <マップ>` | マップを削除（開始看板の登録も解除） |
| `/spike setspawn <attack\|defense>` | 攻撃側 / 防衛側スポーンを設定 |
| `/spike setbarrier <attack\|defense> <1\|2>` | 購入境界の角を設定（2点） |
| `/spike setspike` | スパイクの初期配置地点を設定 |
| `/spike setsite <サイト> <1\|2>` | 設置サイトの角を設定（2点） |
| `/spike delsite <サイト>` | 設置サイトを削除 |
| `/spike setfield <1\|2>` | 試合範囲の角を設定（任意） |
| `/spike setsign <join\|leave\|start <マップ>\|weapon\|solo\|delete>` | 看板を登録／解除 |
| `/spike setsolospike` | ソロ用スパイク地点を追加（現在地） |
| `/spike addpost` | ソロ用の見張りの配置地点を追加（現在地と向き） |
| `/spike addroute <ルート>` | ソロ用の巡回ルートに地点を追加（現在地） |
| `/spike clearsolo <spike\|post\|route <ルート>\|all>` | ソロの地点を削除 |
| `/spike solo <プレイヤー>` | 指定した人をソロ解除チャレンジに参加させる（コンソール・コマンドブロック可） |
| `/spike ranking reset <プレイヤー\|all> [難易度\|all] [マップ\|all]` | ソロの記録を削除 |
| `/spike wand` | 設定ツール（ブレイズロッド）を受け取る |
| `/spike menu` | 設定メニュー（GUI）を開く |
| `/spike stop` | 試合を強制停止 |
| `/spike lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧（`force` で退避データ破棄・ロック解除） |
| `/spike status` | 設定状況・現在の状況を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/spike join` | ロビーに参加（参加看板と同等） |
| `/spike leave` | ロビーから離脱（離脱看板と同等。試合中は脱落扱い） |
| `/spike start [マップ] [random\|select]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/spike status` | 設定・状況を確認（読み取り専用） |
| `/spike solo` | ソロ解除チャレンジに参加（ソロ看板と同等） |
| `/spike solo defuse [難易度] [マップ]` | ソロ解除チャレンジをすぐ始める |
| `/spike ranking solo [難易度] [マップ]` | ソロ解除チャレンジのランキング |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `spike.admin` | OP | 看板設置・マップ/地点の各 set 系・`delmap`・`delsite`・ソロ地点（`setsolospike`・`addpost`・`addroute`・`clearsolo`）・`solo <プレイヤー>`・`ranking reset`・`wand`・`menu`・`stop`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/spike join`・`/spike leave`・`/spike start`・`/spike status`・`/spike solo`（自分）・`/spike solo defuse`・`/spike ranking solo` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。`/spike solo <プレイヤー>` で他人を指定できるのは OP・コンソール・コマンドブロックだけです。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/spike status` を確認してください。マップが開始可能（スポーン・購入境界・スパイク初期地点・設置サイトがすべて設定済み）で、ロビーに2人以上おり、**全員が役職を選んでいる** 必要があります。役職未選択の人がいると開始できません。

??? failure "マップの設定を変更できない"
    そのマップが試合で使用中（進行中・開始準備中）だと変更・削除できません。試合の終了を待つか `/spike stop` で停止してください。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/spike lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/Spike/lobby-inventory.yml` に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【SPIKE】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。試合結果も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます（「他のミニゲーム ○○ に参加中です」）。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品を `plugins/Spike/lobby-inventory.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します（試合終了時は復元せず、ロビー在籍中はずっと空のまま）。異常で持ち物が戻らない・ロックが残った場合はOPが `/spike lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は旧ファイルを `config.yml.bak-日時` に退避）。地点・看板・マップなどのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でもショップを開け、登録看板は蝋引きして編集画面が開かないようにします。
- **観戦（GhostSpectator）** … 死亡したプレイヤーは味方の視点を観戦します（スニークで対象切り替え）。統合版は観戦モードが扱いづらいため、方式を自動で切り替えます（`bedrock.spectator-style`）。

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
| `name-tag.display-name` | SPIKE | 「【】」に入れる正式名 |
| `name-tag.color` | RED | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でもショップを開ける |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.spectator-style` | AUTO | 観戦方式（AUTO＝統合版だけゴースト観戦／GHOST＝全員ゴースト／VANILLA＝全員観戦モード） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← SPIKE 概要へ](index.md){ .md-button }
