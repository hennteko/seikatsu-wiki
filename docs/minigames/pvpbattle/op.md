<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# PvPバトル ― OP・運営ガイド { .page-op #pvpbattle-op }

PvPバトルの導入・地点/看板設定・モード別の設定差分・config・権限・管理コマンドをまとめます。地点・看板はコマンドや設定GUIで登録すると即 config に自動保存され、再起動後も復元されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | PvpBattle |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/pvpbattle`（別名 `/pvpb`） |
| api-version | 26.1.2 |
| 設定ファイル | `plugins/PvpBattle/config.yml` |
| 権限ノード | `pvpbattle.admin`（既定OP） |

## セットアップ手順

導入後、おおまかに **設定ツール取得 → ロビー・初期スポーン → 使うモードの地点 → 看板 → 各種調整** の順に設定します。地点系は **実行した位置** を登録し、看板系は看板を **見ながら**（5ブロック以内）実行します。地点・数値はすべて設定GUI（`/pvpbattle wand`）からも操作できます。

1. ビルドした `PvpBattle` の jar を `plugins/` に入れてサーバーを起動すると、`config.yml` が自動生成されます。
2. 設定ツール（ネザースター）を受け取り、右クリックで設定GUIを開けます。

```text title="設定ツール（ネザースター）を受け取る（旧名 tool も使用可）"
/pvpbattle wand
```

```text title="受付ロビー（その場に立って実行）"
/pvpbattle setlobby
```

```text title="初期スポーン（途中抜け・離脱時の戻り先／その場に立って実行）"
/pvpbattle setstartspawn
```

```text title="FFAフィールド範囲 角1（その場に立って実行）"
/pvpbattle setfield 1
```

```text title="FFAフィールド範囲 角2（その場に立って実行）"
/pvpbattle setfield 2
```

```text title="チーム戦スポーン 赤（その場に立って実行）"
/pvpbattle setspawn teamA
```

```text title="チーム戦スポーン 青（その場に立って実行）"
/pvpbattle setspawn teamB
```

```text title="大将戦スポーン 赤（その場に立って実行）"
/pvpbattle setspawn taishoA
```

```text title="大将戦スポーン 青（その場に立って実行）"
/pvpbattle setspawn taishoB
```

```text title="参加看板を登録（看板を見て実行）"
/pvpbattle setsign join
```

```text title="退出看板を登録（看板を見て実行）"
/pvpbattle setsign leave
```

```text title="開始看板（FFA）を登録（看板を見て実行）"
/pvpbattle setsign start ffa
```

```text title="開始看板（チーム戦）を登録（看板を見て実行）"
/pvpbattle setsign start team
```

```text title="開始看板（大将戦）を登録（看板を見て実行）"
/pvpbattle setsign start taisho
```

```text title="ソロ看板を登録（看板を見て実行）"
/pvpbattle setsign solo
```

```text title="視線先のPvPバトル看板の登録を解除"
/pvpbattle setsign delete
```

3. 必要に応じて人数・制限時間・ラウンド数・チーム分け方式などを調整し、`/pvpbattle status` で設定状況を確認します。

!!! note "必要な地点はモードごとに違います"
    すべての地点を一度に用意する必要はありません。**使うモードに応じた地点** だけ設定すれば、そのモードを開始できます（下の「モード別の設定差分」を参照）。ロビー・初期スポーンは全モード共通で必須です。

## モード別の設定差分

開始時に必要な地点が揃っていないと、そのモードはエラーで開始できません。

| モード | 必須の地点 | 関連する調整コマンド |
|---|---|---|
| FFA | `setfield 1` / `setfield 2`（ランダム湧き範囲） | `settime`（制限時間）・`setffaprotection`（リスポーン無敵秒） |
| チーム戦 | `setspawn teamA` / `setspawn teamB` | `setrounds`（先取ラウンド数）・`setteammode`（チーム分け方式） |
| 大将戦 | `setspawn taishoA` / `setspawn taishoB` | （専用の数値設定なし。人数・装備は共通設定） |

```text title="FFA制限時間（秒）"
/pvpbattle settime <秒>
```

```text title="FFAリスポーン無敵（秒）"
/pvpbattle setffaprotection <秒>
```

```text title="チーム戦の先取ラウンド数"
/pvpbattle setrounds <数>
```

```text title="チーム分け方式（random / manual / draft）"
/pvpbattle setteammode <random|manual|draft>
```

!!! note "チーム分け方式（setteammode）"
    - **random** … 自動で均等に赤・青へ振り分けます。
    - **manual** … 開始した人（OP）がGUIで一人ずつ赤／青を割り当てて確定します。
    - **draft** … ランダムに大将2人を選び、大将が交互にメンバーを指名して分けます。
    コンソール／コマンドブロックから開始した場合は、manual/draft でも自動で random 相当の割り当てになります。

## 管理コマンド

すべて `pvpbattle.admin` 権限が必要です（他プレイヤーの join/leave も同様）。

| コマンド | 説明 |
|---|---|
| `/pvpbattle wand` | 設定ツール（右クリックで設定GUI）を受け取る（旧名 `tool`） |
| `/pvpbattle setlobby` | ロビー地点を設定（現在地） |
| `/pvpbattle setstartspawn` | 初期スポーン（途中抜け・離脱時の戻り先）を設定（現在地） |
| `/pvpbattle setfield <1\|2>` | FFAフィールド範囲の角を設定（2点・現在地） |
| `/pvpbattle setspawn <teamA\|teamB\|taishoA\|taishoB>` | 各モードのスポーンを設定（現在地） |
| `/pvpbattle setsign <join\|leave\|solo\|start <mode>\|delete>` | 看板を登録／解除（看板を見て実行） |
| `/pvpbattle settime <秒>` | FFA制限時間（最低10秒） |
| `/pvpbattle setffaprotection <秒>` | FFAリスポーン無敵（0以上） |
| `/pvpbattle setrounds <数>` | チーム戦の先取ラウンド数（最低1） |
| `/pvpbattle setmax <数>` | 最大参加人数（最低2） |
| `/pvpbattle setmin <数>` | 最低開始人数（最低1） |
| `/pvpbattle setteammode <random\|manual\|draft>` | チーム分け方式 |
| `/pvpbattle join <名前>` | 指定プレイヤーを参加させる |
| `/pvpbattle leave <名前>` | 指定プレイヤーを退出させる |
| `/pvpbattle solo <名前> [モード] [難易度]` | 指定プレイヤーをソロに参加させる（コンソール・コマンドブロック可） |
| `/pvpbattle ranking reset <名前\|all> [モード\|all] [難易度\|all]` | ソロの記録を削除（コマンドブロック不可） |
| `/pvpbattle stop` | 進行中のゲームを強制終了する（ソロの挑戦者にはソロメニューを開く） |
| `/pvpbattle lobbyfix <名前> [force\|legacy]` | 持ち物の手動復元。`force` は退避データを破棄してロックのみ解除、`legacy` は以前の版で戻らなかった持ち物を返す |
| `/pvpbattle reload` | 設定・看板・成績を再読み込みする |

!!! tip "設定GUI（/pvpbattle wand）"
    人数・FFA制限時間・FFA無敵・先取ラウンド・チーム分け方式の増減と、ロビー・初期スポーン・FFAフィールド2点・チーム戦/大将戦スポーンの「現在地に設定」を1画面でまとめて操作できます。地点ボタンを押すと、その場にいる座標が登録されます。

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/pvpbattle join` | ロビーに参加（参加看板と同等・全員可） |
| `/pvpbattle leave` | ロビーから退出（退出看板と同等・全員可） |
| `/pvpbattle start <ffa\|team\|taisho>` | ゲームを開始（開始看板と同等・全員可／コンソール・コマンドブロックも可） |
| `/pvpbattle status` | 設定・状況を確認（全員可・読み取り専用） |
| `/pvpbattle stats [名前]` | 成績を確認（全員可・読み取り専用） |
| `/pvpbattle solo [モード] [難易度]` | ソロ（CPU対戦）に参加（モード指定ですぐ開始・全員可） |
| `/pvpbattle ranking solo <モード> [難易度]` | ソロのランキング（全員可・読み取り専用） |

## ソロ（CPU対戦）

ソロ（`/pvpbattle solo`）は **ボット相手に1人で** 遊ぶモードです。アリーナは通常戦と共用で、通常戦とソロを合わせて同時に1試合だけ動きます。地点も通常戦のものを使います（FFA＝`setfield` のフィールド範囲、大将抜き＝`taishoA`・`taishoB` のスポーン）。専用の地点設定は不要で、ソロ看板（`/pvpbattle setsign solo`）を置くとワンクリックで参加できます。

- **モード** … `ffa`（FFAサバイバル／複数ボットのデスマッチ・ボット同士も戦う）と `taisho`（大将抜き／1対1の勝ち抜き。最後の相手は1段上の難易度の大将）。それぞれ config の `solo.modes` で ON/OFF・ボット数・制限時間などを調整できます。
- **難易度** … `easy`（よわい）・`normal`（ふつう）・`hard`（つよい）。反応速度・移動速度・狙いのブレ・跳び斬り/横移動/盾の割合・回復の閾値を `solo.difficulty` で定義します。
- 記録は `plugins/PvpBattle/solo-records.yml` に保存され、`/pvpbattle ranking solo` で参照、`/pvpbattle ranking reset` で削除できます。

## config.yml 設定項目

地点・看板のデータ（`arena.*`・`signs.*`）はコマンド／GUIで登録すると自動保存されます。手書き不要です。以下はおもな設定項目です。

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 24 | 最大参加人数（`setmax`／GUIで変更可） |
| `settings.min-players` | 2 | 最低開始人数（`setmin`／GUIで変更可） |
| `display-name` | `&c【PvPバトル】` | 名前の前に付く表示名（チャット・Tab・頭上／`&` カラーコード可） |
| `messages.join-broadcast` | `{game}&f{player} &aが参加しました &7({count}人)` | 参加時の全体告知（`{game}`/`{player}`/`{count}` 置換） |
| `messages.leave-broadcast` | `{game}&f{player} &7が離脱しました &7({count}人)` | 離脱時の全体告知 |
| `messages.vault-save-failed` | `&c持ち物の退避に失敗したため…` | 退避失敗で参加中止になったときの通知 |
| `game.countdown-seconds` | 5 | 開始前カウントダウン（秒） |
| `game.result-seconds` | 10 | 結果発表からロビー復帰までの秒数 |
| `modes.ffa.time-limit-seconds` | 300 | FFA制限時間（秒／`settime`） |
| `modes.ffa.respawn-invincible-seconds` | 3 | FFAリスポーン直後の無敵（秒／`setffaprotection`） |
| `modes.team.win-rounds` | 3 | チーム戦の先取ラウンド数（`setrounds`） |
| `modes.team.team-mode` | random | チーム分け方式 random/manual/draft（`setteammode`） |
| `loadout.ffa.*` | 鉄装備一式 | FFAの配布装備（armor/items/offhand） |
| `loadout.team.*` | ダイヤ装備一式＋金リンゴ5 | チーム戦の配布装備 |
| `loadout.taisho.*` | ダイヤ装備一式＋金リンゴ5 | 大将戦の配布装備（チーム戦と同一） |
| `solo.enabled` | true | ソロ（CPU対戦）の有効化 |
| `solo.default-difficulty` | normal | モード指定時に難易度を省略したときの難易度 |
| `solo.countdown-seconds` | 3 | 開始前カウントダウン（挑戦者・ボットとも動けない・秒） |
| `solo.result-seconds` | 3 | 結果表示からロビー復帰までの秒数 |
| `solo.menu-delay` | 3 | ロビー復帰からソロメニューを開くまでの秒数 |
| `solo.ranking-size` | 10 | `/pvpbattle ranking solo` の表示人数 |
| `solo.broadcast-top` | true | ランキング1位更新をサーバー全体に告知 |
| `solo.max-bots` | 8 | 同時に出すボットの上限 |
| `solo.bot-speed-scale` | 1.0 | ボットの移動速度の微調整 |
| `solo.modes.ffa.*` | enabled/bots 5/time-limit 300/bot-infight | FFAサバイバルのボット数・制限時間・ボット同士の戦闘 |
| `solo.modes.taisho.*` | enabled/opponents 5 | 大将抜きの勝ち抜く相手の人数 |
| `solo.difficulty.<id>` | easy/normal/hard | ボット難易度（reaction-ticks・speed・aim-error・crit/strafe/shield-chance・heal-below） |
| `protect.enabled` | true | アリーナ保護（在籍者＝ロビー/試合中/ソロの人とボットのブロック破壊・設置・各種操作・爆発破壊などを止める） |
| `protect.allow-interact` / `allow-break` / `allow-place` | `[]` | 保護の例外として操作・破壊・設置を許すブロック一覧 |

!!! note "アリーナ保護（protect）"
    在籍中（ロビー待機・試合中・ソロ）のプレイヤーとボットによるブロック破壊・設置・バケツ・着火・爆発破壊・ドア/ボタン/チェスト等の操作・感圧板・矢による的操作・額縁/絵画/防具立てを止めます。**OP も在籍中は同じ制限を受けます**（アリーナを直すときは一度 `/pvpbattle leave` してください）。在籍していない人には作用しません。

!!! note "ローダウト（配布装備）の書き方"
    `loadout.<モード>` に `armor`（helmet/chestplate/leggings/boots）・`items`（スロット番号 0〜8 → アイテム）・`offhand` を定義します。各アイテムは `material` / `amount` / `name` / `enchantments{名:Lv}` / `potion-effects[{type,amplifier,duration}]` を指定できます。不明な material・エンチャント・効果はスキップされ、起動ログに警告が出ます。

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `pvpbattle.admin` | OP | `wand`（旧 `tool`）・各 `set` 系・`setsign`・`stop`・`reload`・`lobbyfix`・`ranking reset`、他プレイヤーの `join`/`leave`/`solo`、PvPバトル看板の破壊 など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/pvpbattle join`・`leave`（自分）・`start`・`status`・`stats`・`solo`（自分）・`ranking solo` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます（この場合 manual/draft は自動割り当てになります）。`/pvpbattle solo <名前>` で他人を指定できるのは OP・コンソール・コマンドブロックだけです。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/pvpbattle status` を確認してください。開始には **最低人数（既定2人）** 以上の在籍が必要です。また、FFAは `setfield` の2点、チーム戦は `setspawn teamA/teamB`、大将戦は `setspawn taishoA/taishoB` が未設定だとそのモードを開始できません。

??? failure "手動／ドラフトのチーム分けが進まない"
    manual はOPがGUIで両チームに最低1人ずつ割り当てて確定するまで、draft は大将が全員を指名し終えるまで準備中のままです。大将がオフラインになった場合などは自動で中止され、待機状態に戻ります。

---

## 全ゲーム共通の改修

本ゲームに実装されている、共通系の機能です。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札に表示名（config の `display-name`・`&` カラーコード可）が前置されます。退出・終了で元に戻ります。チーム戦／大将戦の対戦中は赤／青のチーム色が付きます。
- **参加/離脱の全体告知** … 参加・離脱時にサーバー全体へ通知します（文面は config の `messages.join-broadcast` / `leave-broadcast`）。試合結果もタイトルと全体メッセージで告知されます。
- **退避データのファイル保存（PlayerVault）** … ロビー入場時に所持品・装備・体力・満腹度・経験値・ゲームモードを `plugins/PvpBattle/vault/<UUID>.yml` に退避し、退出・終了で復元します。ファイル保存のため、**サーバー再起動・クラッシュをまたいでも持ち物を失いません**。退避に失敗した場合は参加を中止します（`messages.vault-save-failed`）。プラグイン無効化時にも在籍者を元の状態へ復元します。
- **成績の永続化（StatsManager）** … キル／デス／勝利／試合数を `plugins/PvpBattle/stats.yml` に保存し、`/pvpbattle stats [名前]` で参照できます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← PvPバトル 概要へ](index.md){ .md-button }
