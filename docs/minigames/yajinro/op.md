<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 弓人狼（Yajinro） ― OP・運営ガイド { .page-op #yajinro-op }

弓人狼の導入・ステージ設定・看板・config・Discord連携・権限・管理コマンドをまとめます。地点・スポーン・看板はコマンドで登録すると自動保存されます（手動編集は基本不要）。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Yajinro |
| メインコマンド | `/yajinro`（エイリアス `/yj`） |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| 作者 | henry |
| 依存プラグイン | なし（会話は外部のDiscord Bot／運営が担当） |
| 設定ファイル | `plugins/Yajinro/config.yml` |
| 権限ノード | `yajinro.admin`（既定OP） |

## 導入手順

1. ビルドした `Yajinro-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Yajinro/config.yml` が自動生成される。
3. `/yj setstartspawn`・`/yj setlobby` で初期スポーンとロビーを設定する。
4. ステージのフィールド範囲・スポーンを登録し、フィールド内に **ボタン** を設置する。
5. 参加・離脱・開始の看板を設置する。
6. `/yj status` で設定状況を確認する。

!!! tip "ボタンは個別登録不要"
    特殊アイテムの起点となるボタンは **個別登録しません**。`setfield` で設定したフィールド範囲内に置かれた **すべてのボタンブロック** が対象になり、プレイヤーが押した「別々の座標」を数えて判定します。マップにボタンを置くだけで機能します。

## セットアップ手順（コマンド）

地点系は **実行した位置** が保存され、看板系は対象の看板を **見ながら（6ブロック以内）** 実行します。すべて `yajinro.admin` 権限が必要です。

```text title="初期スポーン（離脱・切断時の戻り先／その場に立って実行）"
/yj setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/yj setlobby
```

```text title="ステージのフィールド角1（その場に立って実行・ステージ自動作成）"
/yj setfield stage1 1
```

```text title="ステージのフィールド角2（その場に立って実行）"
/yj setfield stage1 2
```

```text title="ゲームスポーン地点を追加（その場に立って実行・複数可）"
/yj setspawn stage1
```

```text title="ゲームスポーン地点を全消去"
/yj clearspawn stage1
```

```text title="ステージ一覧を表示"
/yj stagelist
```

```text title="指定ステージを削除（開始看板の登録も解除）"
/yj delstage stage1
```

!!! note "ステージ名は任意・スポーンは使い回し可"
    `stage1` は例です。`/yj setfield <好きな名前> 1` で任意名のステージが自動作成されます。ゲームスポーンが人数分に足りない場合は使い回し（±2ブロックのランダム）で配置されます。スポーン未設定のときはフィールド範囲内のランダム地点にフォールバックします（警告表示）。

## 看板の設置

参加・離脱・開始の看板を、看板を見ながら登録します。テキストはプラグインが自動で書き込み・更新します。

```text title="参加看板を登録"
/yj setsign join
```

```text title="離脱看板を登録"
/yj setsign leave
```

```text title="開始看板を登録（ステージ指定・クリックでそのステージを開始）"
/yj setsign start stage1
```

```text title="視線先の看板の登録を解除（種別を自動判別・テキストもクリア）"
/yj setsign delete
```

!!! success "看板は複数設置できます"
    参加・離脱・開始の各看板を **複数拠点に設置** できます（開始看板はステージ識別子ごと）。登録は上書きではなく **追記** され、`/yj setsign delete` は視線先の1枚だけ解除します。人数・状態表示は全枚数が同時に更新されます。保存形式は `signs.join` / `signs.leave`（`"ワールド名,x,y,z"` のリスト）・`signs.start.<ステージ>`（リスト）です。

## config.yml 設定項目

地点・スポーン・ステージ・看板はコマンドで自動保存されます。主な調整項目は次のとおりです。

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 4 | 最低人数（0人は必ず拒否。1にすると1人でも開始可） |
| `max-players` | 15 | 最大参加人数 |
| `countdown-seconds` | 10 | 開始カウントダウン（移動固定） |
| `grace-seconds` | 10 | 開始直後の撃破無効時間（散開時間）。0=なし |
| `time-limit` | 0 | 制限時間（秒）。0=無制限 |
| `timeout-winner` | DRAW | 時間切れの勝者（`DRAW` / `VILLAGE` / `WOLF`） |
| `result-seconds` | 15 | 結果発表の表示秒数 |

### 矢・クォーツ・クラフト

| キー | 既定値 | 説明 |
|---|---|---|
| `ammo.initial` | 3 | 開始時の矢の本数 |
| `ammo.refill-count` | 3 | 補充本数 |
| `ammo.refill-ticks` | 40 | 撃ち切りから補充までのtick（20tick=1秒） |
| `quartz.initial` | 1 | 開始時のクォーツ所持数 |
| `craft.required` | 4 | クラフトに必要なクォーツ数（2×2に並べる） |
| `craft.accomplice-can-craft-axe` | false | trueにすると共犯者も人狼の斧を作れる |

### 役職の能力

| キー | 既定値 | 説明 |
|---|---|---|
| `ability.initial-charges` | 1 | 占い師・霊媒師・騎士の初期能力回数 |
| `knight.can-guard-self` | false | 騎士が自分自身を守れるか |
| `knight.notify-target` | false | 守られた側に通知するか |
| `knight.notify-attacker` | true | 守りで防がれたとき攻撃側に通知するか |
| `death.show-killer` | false | trueで「AはBに倒された」、falseで「Bが倒れた」 |
| `death.reveal-role` | false | 死亡時に役職を公開するか（終了時は必ず全公開） |

### ボタン・特殊アイテム

| キー | 既定値 | 説明 |
|---|---|---|
| `buttons.required` | 5 | 別々のボタンを何個押すと報酬か |
| `buttons.reset-pressed-after-reward` | false | trueで報酬後に同じボタンを再利用可 |
| `special-items.enabled` | 7種すべて | 抽選対象（早い者勝ち・被りなし）。外したいものは行削除 |
| `special-items.tp-trident.uses` | 5 | 瞬間移動トライデントの使用回数 |
| `special-items.hero-sword.speed-multiplier` | 1.5 | かけだし勇者の剣の移動速度倍率 |
| `special-items.hero-sword.jump-bonus` | 0.5 | 同・跳躍力の加算 |
| `special-items.rod.power` | 1.6 | 伝説のつりざおの上昇の強さ |
| `special-items.rod.cooldown-ticks` | 40 | 同・連続使用の間隔 |
| `special-items.cobweb.radius` | 1 | 蜘蛛の巣トラップの半径（1=3×3） |
| `special-items.cobweb.duration` | 6 | 同・消えるまでの秒数 |
| `special-items.swap.notify-target` | true | 入れ替えられた側に通知するか |

`special-items.enabled` の初期値は `[SPREAD_CROSSBOW, TP_TRIDENT, HERO_SWORD, LEGEND_ROD, LIGHTNING_TRAP, COBWEB_TRAP, SWAP]` の7種です。

### チャット・Discord・環境

| キー | 既定値 | 説明 |
|---|---|---|
| `chat.alive-mode` | DISTANCE | 生存者チャット（`DISTANCE`＝近くの生存者のみ／`ALL`／`OFF`） |
| `chat.distance` | 15 | `DISTANCE` のときの到達範囲（ブロック） |
| `chat.dead-isolate` | true | 死亡者チャットを死亡者・観戦者間のみにする |
| `chat.wolf-chat` | true | `/yj w` 人狼・共犯者専用チャットの有効化 |
| `discord.log-events` | true | 開始/死亡/終了をコンソールへ出力（Botのミュート連携用） |
| `keep-food` | true | 試合中は満腹度を減らさない |
| `messages.prefix` | `[弓人狼]` | メッセージの接頭辞 |

### ロビー共通インベントリ（`lobby-inventory`）

ロビー入場時に所持品を退避し、退室・試合終了で復元する共通システムです。

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | 機能の有効化 |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（`ADVENTURE`/`SURVIVAL`/`CREATIVE`/`KEEP`） |
| `lobby-inventory.clear-effects` | true | ロビーでポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビーで体力をリセット |
| `lobby-inventory.reset-exp` | true | ロビーで経験値をリセット |
| `lobby-inventory.expire-days` | 30 | 退避データの保持日数 |

### 配役（`role-distribution`）

人数ごとの役職配分です。役職キーは `villager` / `seer`（占い師）/ `medium`（霊媒師）/ `knight`（騎士）/ `werewolf`（人狼）/ `accomplice`（共犯者）。config で人数ごとに自由に上書きできます。人数が表にない場合は最も近い下の行を使い、余りは村人になります。

| 人数 | 村人 | 占い師 | 霊媒師 | 騎士 | 人狼 | 共犯者 |
|---|---|---|---|---|---|---|
| 4（練習） | 2 | 1 | 0 | 0 | 1 | 0 |
| 5 | 3 | 1 | 0 | 0 | 1 | 0 |
| 6 | 2 | 1 | 1 | 0 | 1 | 1 |
| 7 | 2 | 1 | 1 | 0 | 2 | 1 |
| 8 | 2 | 1 | 1 | 1 | 2 | 1 |
| 9 | 3 | 1 | 1 | 1 | 2 | 1 |
| 10 | 4 | 1 | 1 | 1 | 2 | 1 |
| 11 | 5 | 1 | 1 | 1 | 2 | 1 |
| 12 | 6 | 1 | 1 | 1 | 2 | 1 |
| 13 | 6 | 1 | 1 | 1 | 3 | 1 |
| 14 | 7 | 1 | 1 | 1 | 3 | 1 |
| 15 | 8 | 1 | 1 | 1 | 3 | 1 |

!!! note "自動生成される領域（手動編集不要）"
    `default-spawn` / `lobby-spawn` / `stages`（フィールド・スポーン）/ `signs`（看板）はコマンドで自動生成・自動保存されます。手動編集は不要です。

## Discord 連携（会話・ミュート）

会話は **Discord VC（全員1チャンネル）** で行い、死亡者はサーバーミュートする運用です。**ミュート操作は外部のDiscord Bot または運営が手動で行い、プラグインは音声・ミュートを一切制御しません**。

プラグインは連携用に、`discord.log-events: true` のとき次の定型ログをコンソールへ出力します。外部Botがログ監視やRCONで「死亡者をミュート／終了時に全解除」を自動化できます。

```text title="コンソール出力される定型ログ"
[Yajinro] START <ステージ> <人数>
[Yajinro] DEAD <名前>
[Yajinro] END <勝者陣営>
```

!!! note "プラグインが持たないもの"
    MinecraftアカウントとDiscordアカウントの紐付けや、VC・ミュートの制御はプラグインの範囲外です。Bot側の実装が必要です。手動運用の場合も、死亡時の全体通知（「<名前> が倒れた」）は必ず表示されるので、運営が見てミュートできます。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/yj setstartspawn` | 初期スポーン地点を設定 |
| `/yj setlobby` | 受付ロビー地点を設定 |
| `/yj setspawn <ステージ>` | ゲームスポーン地点を追加（複数可） |
| `/yj clearspawn <ステージ>` | ゲームスポーン地点を全消去 |
| `/yj setfield <ステージ> <1\|2>` | フィールドの角1／角2を設定（ステージ自動作成） |
| `/yj setsign <join\|leave\|start <ステージ>\|delete>` | 参加／離脱／開始／解除看板を設定 |
| `/yj stagelist` | ステージ一覧を表示 |
| `/yj delstage <ステージ>` | 指定ステージを削除（開始看板の登録も解除） |
| `/yj leave <プレイヤー>` | 指定プレイヤーを強制離脱させる |
| `/yj lobbyfix <プレイヤー> [force]` | ロビー退避データを手動で復旧する |
| `/yj stop` | ゲームを強制終了（全員復元してロビーへ） |
| `/yj reset` | 緊急リセット（トラップ・蜘蛛の巣・矢・スコアボードを完全消去） |
| `/yj reload` | config を再読み込み |
| `/yj status` | 設定状況・現在の状況を確認（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `yajinro.admin` | OP | セットアップ・看板設置・強制停止・強制離脱・lobbyfix など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/yj join`・`/yj leave`（自分）・`/yj start <ステージ>`・`/yj status`・`/yj role`・`/yj w`・役職コマンド（`/yj divine`・`/yj seance`・`/yj guard`）は権限チェックがなく、全プレイヤーが使えます（役職コマンドは該当役職の生存者のみ有効）。`/yj start` は看板クリックと同じく誰でも開始できます。`/yj leave <プレイヤー>` の他人指定だけは `yajinro.admin` が必要です。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/yj status` と `/yj stagelist` を確認してください。対象ステージにフィールド範囲（`setfield 1`/`2`）が必要です。また、ロビーに最低人数（既定4人）以上いる必要があります。

??? failure "特殊アイテムが手に入らない"
    ボタンは `setfield` の範囲内にあるものだけが対象です。フィールド内にボタンブロックが設置されているか確認してください。プレイヤーは **別々のボタンを5つ** 押す必要があります。プールが空（全部取られた）の場合も出ません。

??? failure "試合が正常に終わらない・残留物がある"
    `/yj reset` を実行してください。トラップ実体・蜘蛛の巣・飛翔中の矢・スコアボードを完全に消去し、状態をリセットします。

??? failure "看板をクリックしても反応しない"
    `/yj status` で看板が登録済みか確認してください。看板の位置を変更・破壊した場合は再登録が必要です。

??? failure "config を編集したのに反映されない"
    `/yj reload` で再読み込みしてください（人数・時間・矢・役職・特殊アイテムなどの数値に反映されます）。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 弓人狼 概要へ](index.md){ .md-button }
