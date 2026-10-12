<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# スナイパーバトロワ ― OP・運営ガイド { .page-op #sniper-op }

スナイパーバトロワの導入・初期設定・config・権限・管理コマンドをまとめます。地点やステージは **設定ツール（ブレイズロッド）** かコマンドで登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Sniper |
| メインコマンド | `/sniper`（エイリアス `/sn`） |
| api-version | 26.1.2 |
| 作者 | henry |
| 依存プラグイン | なし（統合版対応に Geyser / Floodgate を任意利用） |
| 設定ファイル | `plugins/Sniper/config.yml` |
| 権限ノード | `sniper.admin`（既定OP） |

## 導入手順

1. ビルドした `Sniper` の jar ファイルをサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Sniper/config.yml` が自動生成される。
3. `/sniper wand` で **設定ツール（ブレイズロッド）** を受け取り、ロビー・初期スポーン・ステージ・看板を設定する。
4. ステージのフィールド範囲・スポーン・チェストを登録し、アイテムプールを調整する。
5. `/sniper status` で設定状況を確認する。

!!! tip "設定は「設定ツール」が最速です"
    `/sniper wand` で受け取るブレイズロッドを使うと、見ているブロックへ直接セットアップできます。ツールを持って `/sniper menu`（またはツールで開くGUI）から、ステージ選択・スポーン追加・看板モード・開始/停止までGUIで操作できます。コマンド派の方は後述の個別コマンドでも同じ設定が可能です。

## セットアップ手順（設定ツール）

`/sniper wand` で受け取ったブレイズロッドを持って操作します。GUIメニューは `/sniper menu` で開きます。

- **ステージ選択** … メニュー中央の「ステージ選択」を左クリックで次、右クリックで前へ。フィールド角を設定すると新ステージが自動作成されます。
- **初期スポーン** … 途中抜け・切断時の戻り先。現在地を登録。
- **ロビー地点** … 受付エリア。現在地を登録。
- **スポーン地点** … 選択中ステージのゲーム開始位置。現在地を複数登録できます。
- **フィールド範囲** … ツールで角1（左クリック）・角2（右クリック）を指定。ワールドボーダーの初期サイズはこの範囲から自動算出されます。
- **チェスト登録** … ツールでチェストを右クリックして登録／解除。
- **看板モード** … 参加／離脱／開始（ステージ指定）／登録解除の各モードをONにして、看板をツールで右クリックすると設置・解除できます。

## セットアップ手順（コマンド）

コマンドで設定する場合は以下を使います。地点系は **実行した位置** が保存され、看板・チェスト系は対象ブロックを **見ながら** 実行します。

```text title="設定ツール（ブレイズロッド）を受け取る"
/sniper wand
```

```text title="設定GUIメニューを開く"
/sniper menu
```

```text title="初期スポーン地点（その場に立って実行）"
/sniper setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/sniper setlobby
```

```text title="ステージのフィールド角1（その場に立って実行・ステージ自動作成）"
/sniper setfield stage1 1
```

```text title="ステージのフィールド角2（その場に立って実行）"
/sniper setfield stage1 2
```

```text title="ゲームスポーン地点を追加（その場に立って実行・複数可）"
/sniper setspawn stage1
```

```text title="指定番号のスポーン地点を削除"
/sniper delspawn stage1 1
```

```text title="チェストを登録／解除（チェストを見ながら実行・複数可）"
/sniper setchest stage1
```

```text title="ステージの試合モードを設定（br=バトロワ / ffa / tdm）"
/sniper setmode stage1 br
```

!!! note "ステージごとに試合モードを選べます"
    各ステージは **バトロワ（`br`・既定）／FFA（個人戦）／TDM（チームデスマッチ）** のいずれかで動かせます。`/sniper setmode <ステージ> <br|ffa|tdm>` で切り替え、`/sniper stagelist` で現在のモードを確認できます。FFA・TDM の挙動は後述の「ゲームモード（FFA / TDM）」を参照してください。TDM ではスポーンを **チームごと** に登録します（`/sniper setspawn <ステージ> red` / `blue`）。

```text title="参加看板（看板を見ながら実行）"
/sniper setsign join
```

```text title="離脱看板（看板を見ながら実行）"
/sniper setsign leave
```

```text title="開始看板（看板を見ながら実行・ステージ指定）"
/sniper setsign start stage1
```

```text title="視線先の看板の登録を解除（看板を見ながら実行・表示もクリア）"
/sniper setsign delete
```

```text title="ステージ一覧（フィールド・スポーン・チェストの登録状況）"
/sniper stagelist
```

```text title="指定ステージを削除"
/sniper delstage stage1
```

!!! note "ステージ名は任意"
    上記の `stage1` は例です。`/sniper setfield <好きな名前> 1` で任意名のステージが自動作成されます。複数ステージを作り、開始看板や `/sniper start <ステージ>` で切り替えられます。

## 人数・時間・ボーダーの設定

```text title="制限時間を設定（秒・0=無制限）"
/sniper settime 600
```

```text title="最低人数を設定（1以上）"
/sniper setmin 2
```

```text title="最大参加人数を設定"
/sniper setmax 16
```

```text title="ボーダー縮小間隔を設定（秒）"
/sniper setborder interval 60
```

```text title="ボーダー1回の縮小量を設定（ブロック）"
/sniper setborder amount 50
```

```text title="ボーダーの最小サイズを設定（ブロック）"
/sniper setborder min 20
```

```text title="ボーダー縮小演出の秒数を設定"
/sniper setborder speed 5
```

```text title="ボーダー外での1秒あたりダメージを設定"
/sniper setborder damage 0.5
```

!!! note "ボーダーの初期サイズは自動算出です"
    ワールドボーダーの **初期サイズはフィールド範囲（`setfield 1`/`2`）から自動で決まります**。`config.yml` の `border.initial-size` を `0` にしておくと自動算出、値を入れると固定サイズになります。`border.interval` 秒ごとに `border.amount` ブロックずつ（`border.speed` 秒かけて）縮小し、`border.min-size` で停止します。

## チェストのアイテムプール

チェストに入るアイテムは重み付きの抽選プールで管理します。手に持ったアイテムをそのまま登録できます（ポーション効果なども保持）。

```text title="手持ちアイテムを抽選プールに追加（重み・最小/最大個数を任意指定）"
/sniper setitem 10 1 2
```

```text title="抽選プールの一覧と出現割合(%)を表示"
/sniper itemlist
```

```text title="指定番号のアイテムの重みを変更"
/sniper setweight 1 40
```

```text title="指定番号のアイテムをプールから削除"
/sniper removeitem 1
```

!!! note "アイテムプールの動作"
    各チェストには、開始時に `chest.items-min`〜`chest.items-max`（既定3〜6）種のアイテムが補充されます。アイテムは **重み（`weight`）に応じて抽選** され、それぞれ `min-amount`〜`max-amount` の個数で配置されます。既定では弾丸（鉄塊・判定は `tag: bullet`）・回復／スピード／ジャンプの各ポーション・煙幕・グラップリングフック（釣り竿・`tag: hook`）・ジャンプの羽（羽根・`tag: leap`）が初期投入されています。

## config.yml 設定項目

地点・スポーン・チェスト・看板・ステージはコマンドまたは設定ツールで自動保存されます（手動編集は基本不要）。主な調整項目は次のとおりです。

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 2 | 最低人数（0人は必ず拒否。1にすると1人でも開始可） |
| `max-players` | 16 | 最大参加人数 |
| `time-limit` | 600 | 制限時間（秒）。0=無制限。時間切れ時はキル数最多が勝利 |
| `countdown-seconds` | 5 | 開始カウントダウン（秒） |
| `result-seconds` | 10 | 結果発表の表示秒数 |

### 武器（スナイパーライフル）

| キー | 既定値 | 説明 |
|---|---|---|
| `weapon.range` | 150.0 | 射程（ブロック） |
| `weapon.ray-size` | 0.3 | 命中判定の太さ |
| `weapon.body-damage` | 2.0 | 胴体1発のダメージ（最大体力6.0→3発でキル） |
| `weapon.headshot-instant` | true | ヘッドショット即死 |
| `weapon.fire-on-left-click` | true | 左クリック（腕を振る動作）で発砲する。基本の操作 |
| `weapon.fire-on-sneak` | true | しゃがみでも発砲する。**ズーム中の発砲手段はこちら**（ズーム中は左クリックがサーバーに届かないため）。※ `fire-on-left-click` と両方 false にすると撃てなくなる（起動時警告） |
| `weapon.require-scope` | false | trueで「望遠鏡を覗いている間だけ発砲できる」従来操作。false（既定）なら覗かなくても手に持っていれば撃てる。統合版は false 推奨。trueにする場合は `fire-on-sneak` も true に |
| `weapon.reload-ms` | 2000 | リロード時間（ミリ秒） |
| `weapon.use-item-cooldown` | false | バニラのクールダウン表示を出す（trueだとリロード中スコープ不可） |
| `weapon.shot-volume` | 4.0 | 銃声の音量（大きいほど遠くまで聞こえる。4.0≒64ブロック） |
| `weapon.trail` | true | 弾道パーティクル |
| `weapon.max-arrows` | 32 | 弾（鉄塊）の最大所持数 ※キー名は互換のため `max-arrows` のまま |

### 体力・環境

| キー | 既定値 | 説明 |
|---|---|---|
| `health.max` | 6.0 | 最大体力（6.0=ハート3個） |
| `health.kill-heal-full` | true | キル時に体力全回復 |
| `health.natural-regen` | false | 自然回復（false推奨。回復ポーションは別途有効） |
| `fall-damage` | true | 落下ダメージ |
| `jump-potion-no-fall` | true | ジャンプポーション中は落下ダメージ無効 |
| `jump-potion-fall-limit` | 0 | 落下ダメージを無効にする落下距離の上限（ブロック）。0=距離に関係なく無効。高所からの飛び降りだけ罰したい場合に使用（目安20前後。跳躍Xはバニラが元々10ブロック程度を無効化するため10以下は効果なし・起動時警告） |
| `melee-damage` | false | 近接攻撃を許可（false＝純狙撃戦） |
| `protect-blocks` | true | 試合中の参加者がステージのブロックを壊す・置くのを禁止する。左クリック発砲では押しっぱなしが採掘になってしまうため、基本はtrue推奨 |
| `hide-nametags` | true | 試合中の参加者のネームタグ（頭上の名前）を消す。trueで壁越しに名前で位置がバレなくなる（ロビー・通常ワールドには影響なし） |
| `keep-food` | true | 満腹度を減らさない |

### 安全地帯（ワールドボーダー）

| キー | 既定値 | 説明 |
|---|---|---|
| `border.initial-size` | 0 | 0=`setfield` の範囲から自動算出。値を入れると固定 |
| `border.interval` | 60 | 縮小間隔（秒） |
| `border.amount` | 50 | 1回の縮小量（ブロック） |
| `border.min-size` | 20 | これ以下には縮まない停止サイズ |
| `border.speed` | 5 | 縮小にかける秒数（壁が動く演出） |
| `border.outside-damage` | 0.5 | 圏外での1秒あたりダメージ |
| `border.actionbar-warning` | true | 圏外にいる間、アクションバーに「安全地帯の外！ 北へ 32m」と警告を出す。統合版はボーダーの壁が見えにくいため true 推奨 |
| `border.warn-before` | `[30, 10]` | 縮小の予告タイミング（秒前） |

### チェスト・煙幕・観戦

| キー | 既定値 | 説明 |
|---|---|---|
| `chest.items-min` | 3 | 1チェストに入る種類数の最小 |
| `chest.items-max` | 6 | 1チェストに入る種類数の最大 |
| `chest.refill-interval` | 0 | 再補充間隔（秒）。0=無効 |
| `smoke.radius` | 4.0 | 煙幕の半径（ブロック・弾道を遮る範囲） |
| `smoke.duration` | 10 | 煙幕の持続時間（秒） |
| `smoke.blindness` | false | trueで煙の中のプレイヤーに盲目を付与 |
| `smoke.block-line-of-fire` | true | 煙越しの狙撃を遮断する |
| `smoke.render-interval` | 4 | 何tickごとに描画するか（1〜10）。小さいほど濃いが負荷増 |
| `smoke.view-range` | 64 | この距離内のプレイヤーにだけ送る（配り過ぎ防止） |
| `smoke.participants-only` | true | 参加者・観戦者にだけ送る（falseでロビーの人にも見える） |
| `smoke.max-active` | 6 | 同時に存在できる煙の上限（超えると古いものから消える） |
| `smoke.smoke-density` | 60 | 外側の自然な煙の量（1回の描画あたり） |
| `smoke.core-enabled` | true | 中心部を大きめパーティクルで埋めるか |
| `smoke.core-density` | 150 | 中心部の量（濃さの主役） |
| `smoke.rim-ratio` | 1.0 | 縁を埋める量（横から覗かれるときは上げる） |
| `smoke.rim-points` | 16 | 縁を作る球面上の点の数（4〜20） |
| `smoke.core-size` | 3.5 | 大きめパーティクルのサイズ（1.0〜4.0・上げるほど遮蔽が強い） |
| `smoke.core-color` | `"9E9E9E"` | 大きめパーティクルの色（16進） |
| `spectator.chat-isolate` | true | 観戦者のチャットを生存者に見せない（実況＝ゴースティング防止）。ロビーやサーバーの他プレイヤーには届く |
| `pending-expire-days` | 30 | 復元待ちデータ（players.yml）の保持日数 |
| `messages.prefix` | `[スナイパー]` | メッセージの接頭辞 |

### グラップリングフック（`hook`）

チェストから拾える釣り竿型の移動アイテムです。狙ったブロックへ右クリックで自分を引き寄せます（人や物には引っかからない・耐久は減らない）。抽選プールに `material: FISHING_ROD` ＋ `tag: hook` で登録されています。

| キー | 既定値 | 説明 |
|---|---|---|
| `hook.power` | 1.2 | 引き寄せの強さの倍率（大きいほど速く飛ぶ） |
| `hook.vertical-boost` | 0.35 | 上向きの補正（0だと屋上の縁に引っかかって登れない） |
| `hook.max-speed` | 2.2 | 速度の上限（飛びすぎ・マップ外への吹っ飛び防止） |
| `hook.max-distance` | 32.0 | これより遠い針は引っ張らない |
| `hook.cooldown-seconds` | 5 | 引き寄せ後のクールダウン（秒・0でなし。バニラのクールダウン表示を使う） |
| `hook.require-ground` | false | trueだと地面に立っているときだけ引き寄せられる（falseは空中でも使え落下脱出になる） |
| `hook.max-fall-distance` | 3.0 | `require-ground` が true のとき、何ブロック以上落下していたら「落下中」とみなすか |
| `hook.no-fall` | true | 引き寄せ後の落下ダメージを無効にする |
| `hook.no-fall-seconds` | 5 | 落下ダメージ無効が有効な秒数（＝引き寄せ後にタダで落下できる猶予） |
| `hook.no-fall-distance` | 64.0 | 何ブロックまでの落下を無効にするか（これを超える落下は普通にダメージ） |

!!! note "針が刺さるのは足場の面"
    グラップリングフックは1回目の右クリックで釣り針を飛ばし、地面に届いた状態でもう一度右クリック（巻き取り）でその地点へ引っ張られます。**刺さるのは地面・床・屋上などの面**で、垂直な壁には刺さりません（バニラの釣り針の仕様）。人や物には引っかけられません。

### ジャンプの羽（`leap`）

チェストから拾える羽根型の移動アイテムです。右クリックで **真上へ大ジャンプ** します（回数は無制限・クールダウンで制限）。抽選プールに `material: FEATHER` ＋ `tag: leap` で登録されています。

| キー | 既定値 | 説明 |
|---|---|---|
| `leap.height` | 15.0 | 跳ぶ高さ（ブロック・0.5〜61。到達高度から初速を自動計算） |
| `leap.cooldown-seconds` | 5 | 次に跳べるまでの秒数（0でなし） |
| `leap.require-ground` | true | trueで地面に立っているときだけ跳べる（落下ダメージ帳消し防止のため推奨） |
| `leap.max-fall-distance` | 3.0 | 何ブロック以上落下していたら「落下中」とみなすか（3なら1〜2ブロックの段差を降りた直後でも跳べる） |
| `leap.no-fall` | true | 跳んだあとの落下ダメージを無効にする |
| `leap.no-fall-seconds` | 5 | 落下ダメージ無効が有効な秒数（＝跳んだあとタダで落下できる猶予） |
| `leap.no-fall-distance` | 26.5 | 何ブロックまでの落下を無効にするか（目安は「跳んだ高さ ＋ 11.5」） |

### ロビー共通インベントリ（`lobby-inventory`）

ロビー入場時に入る前の所持品を退避してインベントリを空にし、ロビー退室時に復元する共通システムです。退避データは `plugins/Sniper/lobby-inventory.yml` に自動保存され、サーバー再起動やクラッシュをまたいでも復元されます（試合の開始・終了では復元されず、ロビー在籍中はずっと空のまま）。

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | falseでこの機能を丸ごと無効化（従来挙動に戻る） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（`ADVENTURE`/`SURVIVAL`/`CREATIVE`/`KEEP`） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に最大体力を既定へ戻し体力・満腹度を全回復 |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値とレベルを0にする |
| `lobby-inventory.expire-days` | 30 | 復元されずに残った退避データの保持日数 |

### 敗者復活モード（ゾンビスナイパー・`revival`）

**バトロワ系ステージでのみ有効**な追加モードです。ONにすると、通常プレイヤーに倒された人は一定秒数後に「ゾンビスナイパー」として復活し、通常プレイヤーを倒すと完全復活します。ゾンビに倒された通常プレイヤーは完全脱落（復活不可）。試合中は `/sniper revival <on|off>` で切り替えられます。

| キー | 既定値 | 説明 |
|---|---|---|
| `revival.enabled` | false | 敗者復活モードを使うか |
| `revival.respawn-seconds` | 20 | 倒されてからゾンビとして復活するまでの秒数 |
| `revival.wipe-at` | 2 | 通常プレイヤーがこの人数まで減った時点で、残るゾンビを全員脱落させる（以降は復活なしの決着戦） |
| `revival.ammo-minimum` | 15 | 完全復活時に保証する最低弾数（最初の死亡時の所持数がこれ以上ならその数を引き継ぐ） |
| `revival.zombie-health` | 1.0 | ゾンビの体力（1.0＝ハート0.5） |
| `revival.zombie-glow` | false | ゾンビを緑に光らせる（発光は壁越しに見え位置がバレるため既定false。ネームタグの `[Z]` とサイドバーで見分け可） |
| `revival.zombie-body-damage` | 1.0 | ゾンビの胴体ダメージ |
| `revival.zombie-headshot-damage` | 1.5 | ゾンビのヘッドショットダメージ（ゾンビは即死させない） |

### ゲームモード（FFA / TDM）

ステージは `/sniper setmode <ステージ> <br|ffa|tdm>` で試合モードを切り替えられます（既定は `br`＝バトロワ）。**FFA・TDM はバトロワと挙動が大きく異なります**。

- **FFA（個人戦）** … 全員が敵の個人戦。**弾薬は無限**（チェスト・ワールドボーダーは使いません）。死亡すると数秒後に **リスポーン** し、制限時間終了時に **最多キル** が勝利（同数は引き分け）。開始・リスポーン時に煙幕・グラップリングフック・ジャンプの羽を配ります。リスポーン地点は `/sniper setspawn <ステージ>` の登録地点から、できるだけ敵の近くを避けて選ばれます。
- **TDM（チームデスマッチ）** … レッド／ブルーに分かれて戦うチーム戦。**弾薬は無限**。死亡すると数秒後にリスポーンし、先に **規定キル数** に達したチームが勝利（時間切れ時はキル数の多いチーム）。リスポーン直後は一定秒数の **無敵（発光）** が付き、時間経過か自分が撃った瞬間に解除されます。スポーンは `/sniper setspawn <ステージ> red` / `blue` でチームごとに登録します（マップの対角へ。近すぎると `status` で警告）。

| キー | 既定値 | 説明 |
|---|---|---|
| `ffa.time-limit` | 300 | FFA の制限時間（秒）。終了時に最多キルが勝利 |
| `ffa.respawn-seconds` | 3 | FFA で死亡してからリスポーンするまでの秒数 |
| `tdm.kill-limit` | 20 | TDM の勝利に必要なチームキル数 |
| `tdm.time-limit` | 600 | TDM の制限時間（秒）。0=無制限。時間切れ時はキル数の多いチームが勝利 |
| `tdm.respawn-seconds` | 3 | TDM で死亡してからリスポーンするまでの秒数 |
| `tdm.spawn-protect-seconds` | 3 | リスポーン後の無敵時間（秒）。時間経過か、自分が撃った瞬間に解除 |
| `tdm.auto-balance` | true | 開始時に自動で半分ずつチーム分けする（奇数のときはレッドが1人多い） |
| `tdm.friendly-fire` | false | 味方への誤射を通すか |
| `tdm.red-name` | `レッド` | レッドチーム名 |
| `tdm.blue-name` | `ブルー` | ブルーチーム名 |

FFA / TDM 共通の配布・通知は `mode-common` で設定します。

| キー | 既定値 | 説明 |
|---|---|---|
| `mode-common.smoke-count` | 2 | 開始時とリスポーン時に配る煙玉の数 |
| `mode-common.give-hook` | true | 開始時とリスポーン時にグラップリングフックを配る |
| `mode-common.give-leap` | true | 開始時とリスポーン時にジャンプの羽を配る |
| `mode-common.avoid-radius` | 12.0 | リスポーン地点を選ぶとき、敵からこの距離以内はできるだけ避ける |
| `mode-common.broadcast-kills` | true | キル通知をチャットに出す |

### 統合版（Bedrock）・名前表示

| キー | 既定値 | 説明 |
|---|---|---|
| `bedrock.wax-signs` | true | 登録看板を蝋引きする（統合版でタップしても編集画面が開かない） |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも特殊アイテム（ジャンプの羽など）を使えるようにする |
| `bedrock.spectator-style` | AUTO | 観戦の方式。`AUTO`＝統合版だけゴースト観戦 / `GHOST`＝全員ゴースト / `VANILLA`＝全員観戦モード |
| `bedrock.name-prefix` | `"."` | Floodgate が入っていないときに統合版と見なす名前の接頭辞 |
| `name-tag.enabled` | true | 参加中の名前を「【ゲーム名】名前」表示にする（頭上・Tab・チャット） |
| `name-tag.display-name` | スナイパーバトロワ | 【】に入れる正式名 |
| `name-tag.color` | GOLD | 【ゲーム名】の色（`GOLD`/`AQUA`/`GREEN`/`RED`/`YELLOW` など） |
| `name-tag.announce-join` | true | falseでサーバー全体への参加通知を出さない |

!!! note "統合版の観戦方式"
    統合版（Bedrock）は観戦モードにすると HUD ごと消え、チャット欄もサイドバーも見えなくなります。`AUTO` では統合版だけ「アドベンチャー＋透明＋飛行＋他人からは見えない」ゴースト観戦になり、表示が壊れない代わりに壁抜けはできません。

!!! success "看板は複数設置できます"
    参加・離脱・開始の各看板を **複数拠点に設置** できます（`signs` は座標リスト形式）。`/sniper setsign <join|leave|start <ステージ>>` は上書きではなく **追記** され、`/sniper setsign delete` は視線先の1枚だけ解除します。

!!! note "自動生成される領域（手動編集不要）"
    `stages`（フィールド・スポーン・チェスト）、`signs`（看板）、`default-spawn` / `lobby-spawn` はコマンドまたは設定ツールで自動生成・自動保存されます。手動編集は不要です。`config-version` はプラグインが管理するバージョン番号です。

## 射撃訓練場（ソロモード）の設定

1人用のタイムアタック「射撃訓練場」を使うための設定です。通常戦のステージとは **別の場所** に作ってください（同時に挑戦できるのは1人です）。

!!! info "的はワールドを書き換えません"
    止まった的は **挑戦者の画面にだけ** ターゲットブロックとして表示し、動く的は挑戦者にだけ見えるアーマースタンドを動かします。ワールドのブロックは変わらず、ほかのプレイヤー・ほかのゲームにも見えません。挑戦が終わる・止まると片付きます。

### 手順

1. 射撃位置（挑戦者が立つ場所）に立ち、撃たせたい方向を向いて実行します。開始時はこの向きになります。

```text title="射撃位置を設定"
/sniper setbooth
```

2. 止まった的にしたいブロックを見て実行します（何個でも。射程 `weapon.range` 以内）。登録したマスは2秒だけターゲットブロックに見えます。ブロックがあるマスでも、空中のマスでも構いません。

```text title="止まった的を登録"
/sniper addtarget
```

3. 動く的の経路を登録します。始点に立って1、終点に立って2を実行します（往復する経路が1本できます）。何本でも登録できます。

```text title="動く的の始点"
/sniper addmover 1
```

```text title="動く的の終点"
/sniper addmover 2
```

4. 登録した的の位置を10秒間だけ確認します（止まった的＝ターゲットブロック、動く的の経路＝緑の粒、射撃位置＝炎）。

```text title="的の位置を確認"
/sniper showtargets
```

5. ソロ看板を設置します（看板を見ながら）。

```text title="ソロ看板を設置"
/sniper setsign solo
```

6. 設定状況を確認します（`[ソロモード（射撃訓練場）]` の欄）。

```text title="設定状況"
/sniper status
```

!!! tip "的の数の目安"
    中級は同時に2個、上級は同時に3個出ます。**止まった的は5個以上、動く的の経路は4本以上** あると、同じ場所に続けて出にくくなります。枠が同時数より少ないときは、出る数が枠の数に減ります。

!!! warning "射撃位置と同じワールドに置いてください"
    別のワールドに登録した的は使われません。射撃位置から射程より遠い的は、登録時に警告が出ます（当たりません）。

### 的の削除

```text title="見ている止まった的を解除"
/sniper deltarget
```

```text title="動く的の経路を番号で解除（番号は /sniper status で確認）"
/sniper delmover 1
```

```text title="止まった的を全部解除"
/sniper cleartargets block
```

```text title="動く的を全部解除"
/sniper cleartargets mover
```

```text title="的を全部解除"
/sniper cleartargets all
```

### 記録の管理

記録は `plugins/Sniper/solo-records.yml` に保存されます（プレイヤー×難易度ごとに最速タイムを1件）。

```text title="あるプレイヤーの記録を全部消す"
/sniper ranking reset <プレイヤー>
```

```text title="ある難易度の記録を全員分消す"
/sniper ranking reset all hard
```

```text title="全記録を消す"
/sniper ranking reset all all
```

### 停止

```text title="射撃訓練場の挑戦だけ止める"
/sniper stop solo
```

挑戦者はロビーへ戻り、ソロメニューが開きます（記録は残りません。在籍は続きます）。`/sniper stop` を引数なしで実行すると、通常戦と射撃訓練場の両方を止めます。

### config.yml（`solo`）

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | true | false でソロモードを無効にする |
| `solo.menu-delay` | 3 | 挑戦が終わってロビーへ戻ってから、ソロメニューを開くまでの秒数 |
| `solo.countdown` | 3 | 開始前のカウントダウン(秒) |
| `solo.leash-radius` | 6.0 | 射撃位置からこの距離より離れると戻す。0 で無制限 |
| `solo.block-during-match` | false | true にすると通常戦の試合中はソロを始められない（逆も同じ）。射撃訓練場を試合のステージの中に作った場合だけ true |
| `solo.broadcast-result` | true | クリア時にサーバー全体へ結果を1行流す |
| `solo.ranking-size` | 10 | `/sniper ranking solo <難易度>` の表示人数 |

難易度は `solo.modes.<識別子>` に書きます。ソロメニューのボタンはここから自動で作られ、書いた順に並びます（メニューに出るのは7個まで）。

| キー | 初級 easy | 中級 normal | 上級 hard | 説明 |
|---|---|---|---|---|
| `display` | 初級 | 中級 | 上級 | 表示名 |
| `icon` | TARGET | ARMOR_STAND | SPECTRAL_ARROW | ボタンのアイテム |
| `description` | | | | ボタンの説明文（リスト） |
| `targets` | 8 | 10 | 12 | 撃つ的の総数（すべて当てたらゴール） |
| `simultaneous` | 1 | 2 | 3 | 同時に出ている的の数 |
| `types` | [block] | [block, mover] | [mover] | 使う的（block＝止まった的 / mover＝動く的） |
| `mover-speed` | 0.08 | 0.1 | 0.18 | 動く的の速さ（ブロック/tick。0.1＝毎秒2ブロック） |
| `time-limit` | 120 | 150 | 180 | 制限時間(秒)。0 で無制限。時間切れは記録なし |
| `reload-ms` | 1000 | 1000 | 1000 | ソロ中のリロード(ミリ秒)。通常戦の `weapon.reload-ms` とは別 |
| `miss-penalty` | 0.0 | 1.0 | 2.0 | 外したときにタイムへ足す秒数 |
| `enabled` | （省略） | （省略） | （省略） | false にするとメニューに出さない |

!!! warning "難易度の識別子と削除"
    識別子（`easy` など）は記録の保存先です。名前を変えると記録が別扱いになります。既定の難易度は消しても起動時に書き戻されるので、使わないものは `enabled: false` にしてください。新しい難易度は `solo.modes` に識別子を足すだけで追加できます（識別子に `.` は使えません）。

!!! note "射撃訓練場の設置データ（`solo-range`）"
    `solo-range`（射撃位置・止まった的・動く的の経路）は上のコマンドで自動保存されます。手で書く必要はありません。

### 通常戦との関係

- ロビー（在籍・持ち物の退避・名前表示）は通常戦と共通です。ソロ在籍者は通常戦の **人数に数えず**、`/sniper start` でも試合に入りません。
- `/sniper join` でソロ→通常戦、`/sniper solo` で通常戦の待機→ソロに切り替わります（在籍は続く）。
- 試合中の `/sniper join` は、ロビーで待機して次の試合から参加する扱いになりました（共通ルール12章）。
- ソロ在籍者がログアウトするとロビーから外れ、次にログインしたときに持ち物が戻って初期スポーンへ送られます。

!!! warning "統合版（Bedrock）"
    ソロメニュー（チェスト画面）はタップで操作できます。ボタンの説明文（lore）の見え方は実機で確認してください。ネザースターは空中タップ・持ってしゃがむ でも開きます。挑戦者にだけ見せるターゲットブロック・アーマースタンドは Geyser 経由でも表示されますが、初回は実機で見え方を確認してください。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/sniper wand` | 設定ツール（ブレイズロッド）を受け取る |
| `/sniper menu` | 設定GUIメニューを開く |
| `/sniper setstartspawn` | 初期スポーン地点を設定 |
| `/sniper setlobby` | 受付ロビー地点を設定 |
| `/sniper setspawn <ステージ> [red\|blue]` | ゲームスポーン地点を追加（TDM はチーム指定） |
| `/sniper delspawn <ステージ> [red\|blue] <番号>` | 指定スポーン地点を削除 |
| `/sniper setfield <ステージ> <1\|2>` | フィールドの角1／角2を設定（ステージ自動作成） |
| `/sniper setmode <ステージ> <br\|ffa\|tdm>` | ステージの試合モードを切り替え |
| `/sniper setchest <ステージ>` | 見ているチェストを登録／解除 |
| `/sniper setsign <join\|leave\|start <ステージ>\|solo\|delete>` | 参加／離脱／開始／ソロ／解除看板を設定 |
| `/sniper setbooth` | 射撃訓練場の射撃位置（今いる場所・向き） |
| `/sniper addtarget` / `deltarget` | 見ているブロックを止まった的に登録／解除 |
| `/sniper addmover <1\|2>` | 動く的の経路の始点／終点（今いる場所） |
| `/sniper delmover <番号>` | 動く的の経路を解除 |
| `/sniper cleartargets [block\|mover\|all]` | 的の登録をまとめて解除 |
| `/sniper showtargets` | 的の位置を10秒間表示 |
| `/sniper ranking reset <プレイヤー\|all> [難易度\|all]` | 射撃訓練場の記録を消す（コンソール可） |
| `/sniper settime <秒>` | 制限時間を設定（0=無制限） |
| `/sniper setmin <人数>` | 最低人数を設定 |
| `/sniper setmax <人数>` | 最大参加人数を設定 |
| `/sniper setborder <interval\|amount\|min\|speed\|damage> <値>` | ボーダー各値を設定 |
| `/sniper setitem [重み] [最小] [最大]` | 手持ちアイテムを抽選プールに追加 |
| `/sniper itemlist` | 抽選プールの一覧と出現割合を表示 |
| `/sniper setweight <番号> <重み>` | 指定アイテムの重みを変更 |
| `/sniper removeitem <番号>` | 指定アイテムを削除 |
| `/sniper stagelist` | ステージ一覧を表示 |
| `/sniper delstage <ステージ>` | 指定ステージを削除 |
| `/sniper lobbyfix <プレイヤー> [force]` | ロビーに入る前の持ち物が戻らないときの復旧。`force` で退避データを破棄してロックだけ解除 |
| `/sniper revival <on\|off>` | 敗者復活（ゾンビスナイパー）モードの切替（バトロワ系ステージ限定） |
| `/sniper give <leap\|hook\|smoke\|ammo\|speed\|jump\|heal>` | 指定アイテムを自分に渡す（動作確認用） |
| `/sniper leapcheck` | ジャンプの羽が使えない原因を項目ごとに診断（`itemcheck` でも可） |
| `/sniper stop [solo\|all]` | ゲームを停止する。`solo` で射撃訓練場だけ、省略／`all` で両方 |
| `/sniper reset` | 在籍・試合データを全消去（緊急リセット） |
| `/sniper reload` | config を再読み込み |
| `/sniper status` | 設定状況・現在の状況を確認（全員可） |

## 看板について

`/sniper setsign <join|leave|start>` で登録した看板は、プラグインが自動的に装飾します。

- **参加看板** … `[スナイパー]` / `参加` と、`現在人数 / 最大人数`・`待機中/進行中` を表示。クリックでロビー参加。
- **離脱看板** … `[スナイパー]` / `離脱`。クリックで離脱。
- **開始看板** … `[スナイパー]` / `開始` と対象ステージ名・`待機中/進行中` を表示。クリックでそのステージのゲーム開始（**開始は全員可**）。
- **ソロ看板** … `[スナイパー]` / `ソロ` / `射撃訓練場` と `空き/挑戦中` を表示。クリックでソロ在籍してソロメニューを開く。
- 参加看板の人数は **通常戦の待機者** だけを数えます（ソロ在籍者は含みません）。

登録を解除したいときは、その看板を見ながら `/sniper setsign delete`（または設定ツールの登録解除モード）を実行すると config から削除され、看板の表示もクリアされます。

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `sniper.admin` | OP | セットアップ・看板設置・強制停止など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/sniper join`・`/sniper leave`・`/sniper start <ステージ>`・`/sniper status`・`/sniper solo`・`/sniper ranking solo` は権限チェックがなく、全プレイヤーが実行できます。`/sniper start` は看板クリックと同じく誰でもゲームを開始できる点に注意してください。それ以外のサブコマンド（wand / menu / set系 / stop / reset / reload など）は `sniper.admin`（既定OP）が必要です。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/sniper status` と `/sniper stagelist` で設定を確認してください。対象ステージにフィールド範囲が未設定だと開始できません。また、ロビーに最低人数（既定2人）以上いる必要があります。スポーン地点が未設定の場合はフィールド内のランダム地点で代用されます。

??? failure "プレイヤーが「撃てない」と言っている"
    操作方法を案内してください。既定ではライフルを手に持って **左クリック**で発砲、**右クリック長押しでズーム**、ズーム中は左クリックが届かないため **しゃがみ（Shift）で発砲** します（`weapon.fire-on-left-click` / `fire-on-sneak` / `require-scope` で切替）。弾（鉄塊）が0だと撃てないので、チェストで補充が必要です。1発ごとに約2秒（`weapon.reload-ms`）のリロードがあります。

??? failure "チェストにアイテムが入らない"
    抽選プールが空、またはチェストが未登録の可能性があります。`/sniper itemlist` でプールを、`/sniper stagelist` でチェスト登録数を確認してください。登録した座標が実際にチェストでないと補充されません。

??? failure "ワールドボーダーが縮小したまま残った／試合が正常に終わらない"
    `/sniper reset` を実行してください。在籍・試合データを全消去し、残留物も含めてリセットします。

??? failure "看板をクリックしても参加・退出できない"
    `/sniper status` で看板が登録済みか確認してください。看板の位置を変更・破壊した場合は再登録が必要です。

??? failure "config を編集したのに反映されない"
    `/sniper reload` で config を再読み込みしてください。人数・時間・武器・ボーダーなどの数値項目に反映されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← スナイパーバトロワ 概要へ](index.md){ .md-button }
