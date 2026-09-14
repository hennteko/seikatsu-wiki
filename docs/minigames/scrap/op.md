<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# SCRAP -回収屋- ― OP・運営ガイド { .page-op #scrap-op }

SCRAP の導入・エリア設定・看板・config・権限・管理コマンドをまとめます。地点・エリア・看板はコマンドで登録すると自動保存されます（手動編集は基本不要）。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Scrap |
| メインコマンド | `/scrap`（エイリアス `/sc`） |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| 作者 | henry |
| 依存プラグイン | なし（チーム資金はプラグイン内部で完結・外部経済に非依存） |
| 設定ファイル | `plugins/Scrap/config.yml` |
| 権限ノード | `scrap.admin`（既定OP） |

## 導入手順

1. ビルドした `Scrap-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Scrap/config.yml` が自動生成される。
3. `/scrap setstartspawn`・`/scrap setlobby` で初期スポーンとロビーを設定する。
4. エリアの **フィールド範囲・カート範囲** を設定し、**回収物地点・怪異出現地点・ランタン地点・潜入開始地点** を登録する。
5. 参加・離脱・開始・ショップの看板を設置する。
6. `/scrap status` で設定状況を確認する。

!!! warning "エリアには回収物地点が必須です"
    エリアに **フィールド範囲・カート範囲・回収物地点（1つ以上）** が無いと開始できません。潜入開始地点（`setspawn`）が未設定の場合はカート範囲にフォールバックします（警告表示）。

## セットアップ手順（コマンド）

地点系は **実行した位置**、`setloot`・`setlantern`・`delpoint` は **視線先ブロックの上面**、看板系は看板を **見ながら（6ブロック以内）** 実行します。すべて `scrap.admin` 権限が必要です。

```text title="初期スポーン（離脱・切断時の戻り先／その場に立って実行）"
/scrap setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/scrap setlobby
```

```text title="エリアのフィールド角1（その場に立って実行・エリア自動作成）"
/scrap setfield area1 1
```

```text title="エリアのフィールド角2（その場に立って実行）"
/scrap setfield area1 2
```

```text title="搬出カートの範囲・角1（その場に立って実行）"
/scrap setcart area1 1
```

```text title="搬出カートの範囲・角2（その場に立って実行）"
/scrap setcart area1 2
```

```text title="潜入開始地点を追加（その場に立って実行・複数可）"
/scrap setspawn area1
```

```text title="潜入開始地点を全消去"
/scrap clearspawn area1
```

```text title="回収物の候補地点を追加（視線先に・サイズ指定）"
/scrap setloot area1 <small|medium|large|random>
```

```text title="怪異の出現地点を追加（その場に立って実行・複数可）"
/scrap setmobspawn area1
```

```text title="ランタンの拾得地点を追加（視線先に・複数可）"
/scrap setlantern area1
```

```text title="視線先3ブロック以内の登録地点を削除（種別を自動判別）"
/scrap delpoint
```

```text title="エリア一覧・ルート一覧を表示"
/scrap arealist
```

```text title="指定エリアを削除（開始看板の登録も解除）"
/scrap delarea area1
```

!!! note "エリア名は任意・回収物は候補から抽選"
    `area1` は例です。`/scrap setfield <好きな名前> 1` で任意名のエリアが自動作成されます。回収物は登録した候補地点から、ラウンド開始時に `loot.count-per-round`（既定12）個だけ抽選配置されます（候補が少なければ全部）。サイズを `random` で登録すると小・中・大からランダムになります。

## ルート（ラウンドごとのエリア切替）

複数のエリアを順番に巡るルートを作れます。`/scrap start <ルート名>` で開始すると、ラウンドごとに指定エリアへ切り替わります。単一エリアで `/scrap start <エリア>` した場合は、同じエリアをラウンド数ぶん繰り返します。

```text title="ルートを作成（ルート名＋エリアを順に指定）"
/scrap setroute route1 area1 area2 area3
```

```text title="ルートを削除"
/scrap delroute route1
```

## 看板の設置

参加・離脱・開始・ショップの看板を、看板を見ながら登録します。テキストはプラグインが自動で書き込み・更新します。

```text title="参加看板を登録"
/scrap setsign join
```

```text title="離脱看板を登録"
/scrap setsign leave
```

```text title="開始看板を登録（エリアまたはルートを指定）"
/scrap setsign start area1
```

```text title="ショップ看板を登録"
/scrap setsign shop
```

```text title="視線先の看板の登録を解除（種別を自動判別・テキストもクリア）"
/scrap setsign delete
```

!!! success "看板は複数設置できます"
    参加・離脱・開始・ショップの各看板を **複数拠点に設置** できます（開始看板はエリア／ルート識別子ごと）。登録は上書きではなく **追記** され、`/scrap setsign delete` は視線先の1枚だけ解除します。保存形式は `signs.join` / `signs.leave` / `signs.shop`（`"ワールド名,x,y,z"` のリスト）・`signs.start.<エリア|ルート>`（リスト）です。

## config.yml 設定項目

地点・エリア・看板はコマンドで自動保存されます。主な調整項目は次のとおりです。

### 人数・時間・ノルマ

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 1 | 最低人数（テスト用に1人開始を許可。0は常に拒否） |
| `max-players` | 8 | 最大参加人数 |
| `countdown-seconds` | 10 | 開始カウントダウン |
| `round-seconds` | 240 | 1ラウンドの制限時間（秒） |
| `shop-seconds` | 60 | ラウンド間のショップ時間（秒） |
| `result-seconds` | 15 | 結果発表の表示秒数 |
| `depart-countdown` | 5 | 全員乗車後に出発するまでの秒数 |
| `revive-hp` | 10 | 復活時の体力 |
| `keep-food` | true | 満腹度を減らさない |
| `rounds` | 10 | ラウンド数（0＝エンドレス。全滅かノルマ未達まで続く） |
| `quotas` | `[1000, 2500, 4000, 6000, 8000]` | ラウンドごとのノルマ金額。未達で即ゲームオーバー |
| `quota-growth` | 1.3 | `quotas` の範囲外は「最後の値 × 1.3^超過数」（100単位に丸め） |
| `surplus-to-funds` | true | ノルマ超過分のみをチーム資金にする |
| `allow-join-in-shop` | true | ショップ時間中の途中参加を許可（次ラウンドから潜入） |
| `late-join-lantern` | false | 途中参加者にランタンを1個渡す |

### 回収物（`loot`）

| キー | 既定値 | 説明 |
|---|---|---|
| `loot.count-per-round` | `[30, 35, 40]` | ラウンドごとの出現数（整数1つでも可） |
| `loot.count-growth` | 3 | リストの範囲外は最後の値に毎ラウンド+3 |
| `loot.auto-fill` | true | 候補地点が足りないとき、登録地点の周囲に自動で散らして補う |
| `loot.scatter-radius` | 5 | 自動配置の散らばり半径（ブロック） |
| `loot.value-variance` | 0.3 | 価値のバラツキ（±30%） |
| `loot.base-value` | small:150 / medium:500 / large:1500 | サイズ別の基準価値 |
| `loot.speed-penalty` | small:0.15 / medium:0.40 / large:0.40 | サイズ別の移動速度低下 |
| `loot.large-max-distance` | 4.0 | 大型品の2人運搬で許される距離 |
| `loot.damage.fall-per-block` | 0.08 | 落下1ブロックあたりの減額割合 |
| `loot.damage.monster-hit` | 0.25 | 運搬者が被弾したときの減額割合 |
| `loot.damage.large-separation` | 0.20 | 2人運搬の距離超過での減額割合 |
| `loot.display-items` | サイズ別のリスト | 回収物の見た目に使うアイテム候補 |
| `loot.noisy-names` | `[古い鐘, オルゴール, 蓄音機]` | 担いでいる間、定期的に鳴って怪異を呼ぶ回収物の名前（価値ボーナスあり） |
| `loot.noisy-bonus` | 1.3 | 音の出る回収物の価値倍率 |
| `loot.noisy-interval-ticks` | 60 | 音の出る回収物が鳴る間隔（tick） |

### 音（`noise`）

怪異には「半径 ×（hearing/16）」の距離まで届きます。値は半径（ブロック）。

| キー | 既定値 | 発生源 |
|---|---|---|
| `noise.walk` | 4 | 歩行（非スニーク） |
| `noise.sprint` | 12 | スプリント |
| `noise.jump` | 10 | ジャンプ着地 |
| `noise.door` | 10 | 扉・フェンスゲート開閉 |
| `noise.loot-damage` | 20 | 回収物の減額 |
| `noise.lantern` | 6 | ランタン点灯中 |
| `noise.shout` | 30 | チャットで叫ぶ（`!`始まり） |
| `noise.decoy` | 40 | おとり |
| `noise.noisy-loot` | 14 | 音の出る回収物が鳴ったとき |

### 怪異（`monsters`）

| キー | 既定値 | 説明 |
|---|---|---|
| `monsters.damage` | 8 | 1撃のダメージ（HP20→3発で死亡） |
| `monsters.attack-range` | 1.7 | 攻撃の届く距離 |
| `monsters.stun-seconds` | 8 | スタンバットの気絶秒数 |
| `monsters.scale-by-players` | 6 | この人数以上で各ウェーブ+1 |
| `monsters.round-bonus-every` | 3 | このラウンド数ごとに各ウェーブ+1（0で無効） |
| `monsters.mimic-count` | `[1, 2, 3]` | ラウンドごとの擬態数 |
| `monsters.mimic-hint` | true | 擬態の価値表示の末尾を7にする（見抜くヒント） |
| `monsters.brightness` | 6 | 怪異の見た目の明るさ（0〜15） |
| `monsters.collector-seconds` | 45 | 残りこの秒数で督促者が出現（0で無効） |
| `monsters.footsteps` | true | 怪異が歩いている間、位置で足音を鳴らす（聞き耳は無音） |
| `monsters.footstep-volume` | 1.5 | 足音の音量 |
| `monsters.excite.speed` | 1.6 | 近くで大きな音がしたときの加速倍率（1.0で無効） |
| `monsters.excite.range` | 16 | この距離以内の音に反応 |
| `monsters.excite.threshold` | 10 | この半径以上の音のみ対象（歩行4は対象外、走る/跳ぶ/扉は対象） |
| `monsters.excite.seconds` | 3 | 加速が続く秒数（音が続けば延長） |
| `monsters.default-waves` | 0秒WANDERER / 30秒DOPPEL / 60秒LISTENER / 90秒WRAITH / 120秒WATCHER / 150秒TALL / 180秒WANDERER | エリア作成時に複製される既定ウェーブ（種類: WANDERER/LISTENER/WATCHER/COLLECTOR/TALL/DOPPEL/WRAITH） |
| `monsters.types.*` | 種類ごとの速度・聴覚・視認など | LISTENER / WATCHER / MIMIC / WANDERER / COLLECTOR / TALL（巨人・一撃死・ほぼ止められない）/ DOPPEL（ドッペルゲンガー・近づくと正体を現す）/ WRAITH（亡霊・壁の中に潜んで壁抜けで一直線に追い、一撃離脱）のパラメータ |
| `monsters.looks.*` | 種類ごとの見た目 | 頭テクスチャ（Base64）・頭/胴ブロック・スケール等 |

!!! note "怪異の見た目はリソースパック不要"
    怪異は透明化したMOBの上にプレイヤーヘッドやブロックを乗せて表現しています。`monsters.looks.<種類>.head-texture` にプレイヤーヘッドのBase64を入れると見た目を差し替えられます。

### ホラー演出（`horror`）・ランタン・アイテム

| キー | 既定値 | 説明 |
|---|---|---|
| `horror.darkness` | true | 視界エフェクトを有効化（falseで全部切り、夜固定のみ） |
| `horror.vision-mode` | both | 視界の種類（`darkness`＝暗闇・弱・走れる／`blindness`＝盲目・強・約5ブロック先が真っ黒で走れない／`both`＝両方・最強） |
| `horror.darkness-amplifier` | 0 | Darknessの強さ |
| `horror.blindness-near-monster` | 0 | `vision-mode: darkness` のとき、怪異がこの距離以内に来たら盲目を追加（0で無効・例8） |
| `horror.fix-night` | true | 試合中は夜固定（終了時に復元） |
| `horror.heartbeat-range` | 30 | 心拍BossBarが反応する最寄り怪異との距離 |
| `horror.ambience-interval-seconds` | 20 | 環境演出の間隔 |
| `horror.torch-blackout` | true | 松明を一時的に消灯する演出 |
| `horror.fake-footsteps` | true | 背後の偽の足音（誰もいない） |
| `horror.fake-delivery` | true | 遠くで偽の納品音 |
| `horror.fake-heartbeat-per-round` | 2 | 偽の心拍（1人あたり1ラウンドに最大何回・0で無効） |
| `horror.whisper-direction` | true | ささやきが本当の怪異の方向（前/後ろ/左/右）を告げる |
| `horror.whisper-direction-range` | 24 | 方向ささやきが働く距離 |
| `horror.silence-range` | 6 | 怪異がこの距離以内なら環境音・音楽を止め心拍だけにする（0で無効） |
| `horror.door-creak-range` | 12 | 怪異が扉を開けたとき、この距離内の人にだけ低い「ギィ……」 |
| `horror.watcher-breath-range` | 12 | 見つめ返す者から目を離している間、背後で呼吸音 |
| `horror.scream-volume` | 6.0 | 死亡時の悲鳴の音量（エリア全体に届く・0で無効） |
| `horror.radio-noise` | true | チャットが届かない距離の仲間には「ザザッ」だけ届く |
| `horror.fear.hit` | true | 被弾時の画面演出（真っ赤＋視点が傾く＋目の前に怪異の顔） |
| `horror.fear.spotted` | true | 発見された瞬間の点滅＋「！」 |
| `horror.fear.proximity` | true | 怪異が近いほど画面の縁が赤くなる |
| `horror.fear.proximity-range` | 10 | この距離から赤くなり始める |
| `horror.fear.proximity-full` | 3 | この距離でほぼ全面 |
| `horror.fear.death` | true | 死亡ジャンプスケア（固定→赤→黒→顔→観戦） |
| `horror.fear.death-freeze-ticks` | 40 | 死亡演出の固定時間（tick・0で即観戦） |
| `horror.power-outage.enabled` | true | 停電（ラウンド中1回、全員のランタンが消えて点けられない） |
| `horror.power-outage.chance` | 0.7 | ラウンドごとの停電発生確率 |
| `horror.power-outage.seconds` | 10 | 停電の継続秒数 |
| `horror.cart-malfunction.enabled` | true | 出発時にカートが故障して延長（督促者が来る）。最終ラウンド、または `from-round` 以降で判定 |
| `horror.cart-malfunction.chance` | 0.5 | カート故障の発生確率 |
| `horror.cart-malfunction.extra-seconds` | 10 | 故障による延長秒数 |
| `horror.cart-malfunction.from-round` | 3 | このラウンド以降でも故障判定を行う |
| `horror.whispers` | 文言リスト | チャットに流れる囁きの文言 |
| `chat-range` | 15 | 距離制限チャットの範囲（`!`始まりで2倍） |
| `lantern.initial` | 2 | 開始時にチームへ配るランタン数 |
| `lantern.per-area` | 1 | エリア内に置かれる拾得数 |
| `lantern.light-level` | 13 | 点灯時の明るさ |
| `lantern.vision-while-lit` | darkness | 点灯中の視界（`off`=明るい／`darkness`=薄暗い・既定／`blindness`／`both`） |
| `lantern.sight-multiplier` | 2.0 | 点灯中の怪異の視認範囲倍率 |
| `items.stun-bat-uses` | 3 | スタンバットの使用回数 |
| `items.decoy-seconds` | 10 | おとりの持続秒数 |
| `items.flare-seconds` | 20 | フレアの持続秒数 |

### ショップ（`shop`・価格。0で非表示）

| キー | 既定価格 | 効果 |
|---|---|---|
| `shop.LANTERN` | 600 | ランタン（光源。点灯中は怪異に見つかりやすい） |
| `shop.DECOY` | 800 | おとり（着地点で10秒間、大きな音） |
| `shop.FLARE` | 700 | フレア（着地点に20秒間の強い光） |
| `shop.SILENT_BOOTS` | 1500 | 消音ブーツ（スプリント・ジャンプの音を消す） |
| `shop.HARNESS` | 2500 | 運搬ハーネス（運搬の速度低下を半減・中型のジャンプ解除） |
| `shop.MEDKIT` | 1000 | 医療キット（HP全回復・1回） |
| `shop.STUN_BAT` | 3000 | スタンバット（怪異を気絶。3回で壊れる） |
| `shop.SHOP_TERMINAL` | 0 | 取引端末（全員に配布。0＝販売しない） |
| `shop.SETUP_WAND` | 0 | OP用設定ツール（`/scrap wand` で入手。0＝販売しない） |

### ロビー共通インベントリ（`lobby-inventory`）

ロビー入場時に所持品を退避し、退室・試合終了で復元する共通システムです。退避データは `plugins/Scrap/lobby-inventory.yml` に保存され、再起動をまたいでも復元されます。

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | 機能の有効化 |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（`ADVENTURE`/`SURVIVAL`/`CREATIVE`/`KEEP`） |
| `lobby-inventory.clear-effects` | true | ロビーでポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビーで体力をリセット |
| `lobby-inventory.reset-exp` | true | ロビーで経験値をリセット |
| `lobby-inventory.expire-days` | 30 | 退避データの保持日数 |

!!! note "自動生成される領域（手動編集不要）"
    `default-spawn` / `lobby-spawn` / `areas`（フィールド・カート・スポーン・回収物地点・怪異出現地点・ランタン地点・ウェーブ）/ `routes`（ルート）/ `signs`（看板）はコマンドで自動生成・自動保存されます。手動編集は不要です。値を変えたら `/scrap reload`（試合中は不可）で反映します。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/scrap setstartspawn` | 初期スポーン地点を設定 |
| `/scrap setlobby` | 受付ロビー地点を設定 |
| `/scrap setfield <エリア> <1\|2>` | フィールドの角1／角2を設定（エリア自動作成） |
| `/scrap setcart <エリア> <1\|2>` | 搬出カートの範囲の角1／角2を設定 |
| `/scrap setspawn <エリア>` | 潜入開始地点を追加（複数可） |
| `/scrap clearspawn <エリア>` | 潜入開始地点を全消去 |
| `/scrap setloot <エリア> <small\|medium\|large\|random>` | 回収物の候補地点を追加 |
| `/scrap setmobspawn <エリア>` | 怪異の出現地点を追加 |
| `/scrap setlantern <エリア>` | ランタンの拾得地点を追加 |
| `/scrap delpoint` | 視線先3ブロック以内の登録地点を削除 |
| `/scrap setroute <ルート名> <エリア...>` | ルートを作成 |
| `/scrap delroute <ルート名>` | ルートを削除 |
| `/scrap setsign <join\|leave\|start <エリア\|ルート>\|shop\|delete>` | 看板を設定／解除 |
| `/scrap arealist` | エリア一覧・ルート一覧を表示 |
| `/scrap delarea <エリア>` | エリアを削除（開始看板の登録も解除） |
| `/scrap leave <プレイヤー>` | 指定プレイヤーを強制離脱させる |
| `/scrap wand` | OP用の設定ツール（SETUP_WAND）を入手する |
| `/scrap lobbyfix <プレイヤー> [force]` | ロビー退避データを手動で復旧する |
| `/scrap stop` | ゲームを強制終了（全員復元してロビーへ） |
| `/scrap reset` | 緊急リセット（回収物・怪異・頭・光源・BossBarを完全消去） |
| `/scrap reload` | config を再読み込み（試合中は不可） |
| `/scrap status` | 設定状況・現在の状況を確認（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `scrap.admin` | OP | セットアップ・看板設置・強制停止・強制離脱・lobbyfix など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/scrap join`・`/scrap leave`（自分）・`/scrap start <エリア>`・`/scrap status`・`/scrap drop`・`/scrap shop`（ショップ時間のみ）は権限チェックがなく、全プレイヤーが使えます。`/scrap start` は看板クリックと同じく誰でも開始できます。`/scrap leave <プレイヤー>` の他人指定だけは `scrap.admin` が必要です。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/scrap status` と `/scrap arealist` を確認してください。対象エリアに **フィールド範囲・カート範囲・回収物地点（1つ以上）** が必要です。ロビーに最低人数以上いる必要もあります。

??? failure "回収物が出現しない"
    `/scrap setloot <エリア> <サイズ>` で候補地点を登録してください。候補が0のエリアは開始できません。

??? failure "怪異が出ない・出過ぎる"
    エリアの `waves` と `monsters.default-waves`、`/scrap setmobspawn` の出現地点を確認してください。人数が `scale-by-players`（既定6）以上だと各ウェーブが+1されます。

??? failure "試合が正常に終わらない・残留物がある"
    `/scrap reset` を実行してください。回収物・怪異・頭・LIGHT光源・BossBar・Darknessを完全に消去し、ワールド時間なども復元します。

??? failure "config を編集したのに反映されない"
    `/scrap reload` で再読み込みしてください（試合中は実行できません）。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← SCRAP 概要へ](index.md){ .md-button }
