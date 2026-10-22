<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 攻城戦（SiegeWar） ― OP・運営ガイド { .page-op #siegewar-op }

生活鯖ワールドにある既存の城をそのまま使う、攻撃側 vs 防御側のチーム対戦です。試合中に変化したブロックはすべて記録し、試合後に試合前の状態へ戻します（ワールドのコピーは使いません）。地点・範囲・コア・看板はコマンドで登録すると即 config に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | SiegeWar |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/siege` |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/SiegeWar/config.yml` |
| 権限ノード | `siegewar.admin`（既定OP） |

## 導入・更新

1. `SiegeWar-1.0-SNAPSHOT.jar` を `plugins/` フォルダに入れて再起動します（更新時は古い jar を消してください）。
2. 初回起動で `plugins/SiegeWar/config.yml` が自動生成されます。更新時は、足りない項目だけが自動で追加されます（コンソールに「config.yml に N 項目を追加しました」と出ます）。更新前のファイルは `config.yml.bak-日時` に保存されます。
3. Geyser / Floodgate があれば統合版の判定に使います（無くても動きます）。

!!! note "ステージは1つ"
    攻城戦はステージが1つだけです。開始看板・`start`・`stop` に識別子はありません。生活鯖の既存の城を1つ選び、その城とその周辺を囲むようにステージ範囲を設定してください。

## セットアップ手順（コマンド）

地点・範囲・コアは **実行した位置（立ち位置）** を使い、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `siegewar.admin` 権限が必要です。

```text title="初期スポーン（離脱時の戻り先／その場に立って実行）"
/siege setstartspawn
```

```text title="ロビー地点（その場に立って実行）"
/siege setlobby
```

```text title="ステージ範囲 角1（その場に立って実行）"
/siege setfield 1
```

```text title="ステージ範囲 角2（その場に立って実行。城とその周辺を囲む）"
/siege setfield 2
```

```text title="攻撃側スポーン（範囲の中・その場に立って実行）"
/siege setspawn attack
```

```text title="防御側スポーン（範囲の中・その場に立って実行）"
/siege setspawn defense
```

```text title="コアを登録（見ているブロック、無ければ足元・範囲の中）"
/siege setcore
```

```text title="参加看板を登録（看板を見て実行）"
/siege setsign join
```

```text title="離脱看板を登録（看板を見て実行）"
/siege setsign leave
```

```text title="開始看板を登録（看板を見て実行）"
/siege setsign start
```

```text title="視線先の看板の登録を解除"
/siege setsign delete
```

```text title="設定状況の確認（「開始できません」が出ないか確認）"
/siege status
```

!!! note "ステージ範囲・コアについて"
    - コアの位置のブロックは、試合の開始時に `core.block`（初期値ビーコン）に置き換わり、試合後に元のブロックへ戻ります。
    - 範囲の高さの上端が、建築・移動の高さの上限になります（`stage.max-build-height` で別に指定できます）。
    - 範囲が広いほど復元に時間がかかります。約 150×100×100 の城で、壊されたブロックが2万個なら約2秒です（`restore.blocks-per-tick`）。
    - 試合中・ステージ復元中は、範囲・スポーン・コアを変更できません。

## 管理ツール・管理メニュー（コマンドを打たない設定）

`/siege wand` で管理ツール（ブレイズロッド）を受け取り、右クリック（統合版はタップ/長押し）で管理メニューを開きます。`/siege menu` でも開けます。どちらも `siegewar.admin` 権限が必要です。

```text title="管理ツール（ブレイズロッド）を受け取る"
/siege wand
```

```text title="管理メニュー（GUI）を開く"
/siege menu
```

- **数値** … ＋/−ボタンで 最低開始人数・最大参加人数・準備フェーズ・試合時間・コアHP・リスポーン秒・攻撃側の比率・延長戦の最大秒数・一斉出撃の間隔 を変更（即保存）。
- **切り替え** … 延長戦・攻撃側の一斉出撃・防衛ボーナス・非参加者を範囲外へ移動 の ON/OFF（即保存）。
- **地点** … ボタンを押すと立ち位置に設定（ロビー・初期スポーン・範囲の角1/2・攻撃側スポーン・防御側スポーン・コア（足元のブロック））。
- **看板** … 看板を見ながら（5ブロック以内）押すと参加／離脱／開始看板を登録、ハサミで登録解除。
- **運用** … 開始・設定状況・リロード・強制停止・手動復元（強制停止と手動復元は5秒以内に2回押し）。

!!! note "管理ツールの注意"
    攻城戦に参加中は受け取れません（先に `/siege leave` で離脱してから実行）。捨てると消えるので、再度 `/siege wand` で取得してください。統合版はチェストGUIで操作でき、数値は左右クリックに頼らず＋/−の別ボタンで操作します。

## ステージ復元

- 試合中、範囲内でブロックが変化すると、**最初に変化した時点の元の状態** を座標ごとに記録します。
  - プレイヤーの設置・破壊（壊したブロックのまわりと、上に積まれた砂・砂利の列も記録）
  - ドア・トラップドア・フェンスゲート・レバーなどの操作、斧で樹皮を剥ぐなどの右クリックによる変化
  - 落下ブロック（砂・砂利）、支えを失って壊れたブロック（たいまつ・はしご等。アイテムは落ちない）
  - レバー・ボタンでつながったレッドストーン・鉄のドア・ランプなどの変化
- 記録は `plugins/SiegeWar/restore-log.txt` に1秒ごとに追記します。試合の途中でサーバーが落ちても、次の起動時に自動で戻します。
  - このときは **ブロックの種類と向きだけ** が戻ります（看板の文字やチェストの中身などは、通常の試合終了時にしか戻りません）。城のコンテナは試合中に開けない・壊せないようにしてあります。
  - ワールドが読み込まれていなくて戻せなかった分はファイルに残ります。ワールドを読み込んでから `/siege restore` を実行してください。

```text title="残っている記録から手動で復元（試合中は使えない）"
/siege restore
```

- 復元中は次の試合を開始できません（status に「ステージ復元中」と出ます）。
- 試合中と復元中は、範囲のチャンクを読み込んだままにします（誰もいない場所の変化を取りこぼさないため）。
- 試合をしていない間の城は保護しません。試合の外で城が壊されたり建築されたりすると、それが次の試合の「元の状態」になります。

## 試合中の範囲の保護

試合中・ステージ復元中は、範囲内で次のことを止めます（範囲の外と、試合をしていない間は何もしません）。

- **非参加者** … ブロックの設置・破壊、ドアなどの操作、バケツ。止めたときは本人にだけ「攻城戦の試合中です」と出ます。入場は止めません（`stage.kick-outsiders: true` で、範囲内にいる非参加者を初期スポーンへ移動させます。OP と在籍者は除外）。
- **参加者と非参加者の間のダメージ** は無効です。
- **環境** … 火の延焼・燃焼、葉の消滅、液体の流れ、ピストン、ディスペンサー、爆発によるブロック破壊、植物の成長、雪や氷の変化、エンダーマン等によるブロックの変化、モブのスポーン（コマンド・プラグインによるものは除く）。
- **額縁・絵画・防具立て** … 誰も壊せず、操作もできません。防具立て・ボート・トロッコなどは範囲内に置けません。
- **非参加者** は範囲内に落ちているアイテムを拾えません。参加者と非参加者の間では、スプラッシュ・残留ポーションの効果も届きません。
- **参加者** はクラフトできません。時刻・天気・ゲームルールは変更しません。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/siege wand` | 管理ツール（ブレイズロッド）を受け取る |
| `/siege menu` | 管理メニュー（GUI）を開く |
| `/siege setstartspawn` | 初期スポーン（離脱時の戻り先）を設定 |
| `/siege setlobby` | ロビー地点を設定 |
| `/siege setspawn <attack\|defense>` | 攻撃側 / 防御側スポーンを設定 |
| `/siege setfield <1\|2>` | ステージ範囲の角を設定（2点） |
| `/siege setcore` | 見ているブロック（無ければ足元）をコアに登録 |
| `/siege setsign <join\|leave\|start>` | 看板を登録 |
| `/siege setsign delete` | 視線先の看板の登録を解除 |
| `/siege stop` | 強制停止（勝敗なし → 参加者をロビーへ → ステージを復元）。在籍は解除しない |
| `/siege restore` | `restore-log.txt` に残っている記録からステージを戻す（試合中は使えない） |
| `/siege reload` | config.yml の再読み込み |
| `/siege lobbyfix <プレイヤー> [force]` | 持ち物の手動復元（`force` は退避データを破棄・ロック解除。最終手段） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/siege join` | 参加（ロビーへ。参加看板と同等） |
| `/siege leave` | 離脱（持ち物が戻り、元の場所へ。離脱看板と同等） |
| `/siege start` | 開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/siege status` | 設定・状況を確認（読み取り専用） |

## config.yml 設定項目

地点・看板・範囲・コアのデータ（`arena`・`sign`・`leave-sign`・`start-sign`・`lobby-spawn`・`default-spawn`）はコマンド登録時に自動保存されます。手書き不要で、自動更新でも消えません。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。以下は数値などの設定項目です。

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` / `max-players` | 4 / 40 | 開始に必要な人数（最低2）/ 定員 |
| `countdown-seconds` | 10 | 開始のカウントダウン（秒） |
| `team-ratio.attack` / `.defense` | 6 / 4 | 陣営の人数比（どちらも最低1人） |
| `prepare-seconds` / `battle-seconds` | 120 / 900 | 準備フェーズ / 攻城フェーズ（秒） |
| `result-seconds` | 8 | 決着後、ロビーへ戻るまで（秒） |
| `respawn-seconds` / `respawn-invincible-seconds` | 5 / 3 | リスポーン待ち / 出撃後の無敵（秒） |
| `wave-respawn.enabled` / `.interval-seconds` | true / 15 | 攻撃側の一斉出撃 / その間隔（秒） |
| `overtime.enabled` / `.max-seconds` / `.grace-seconds` | true / 30 / 2 | 延長戦 / 最大秒数 / 殴る人が0人で確定するまでの猶予 |

### コア（`core`）

| キー | 既定値 | 説明 |
|---|---|---|
| `core.block` | BEACON | コアのブロック（開始時に置換、終了後に復元） |
| `core.hp` | 300 | コアの HP |
| `core.damage-interval-ticks` | 10 | 殴っている間、この間隔（tick）ごとに1ダメージ（20tick=1秒） |
| `core.reach` | 5 | コアを殴れる距離（ブロック） |
| `core.require-swing` / `.swing-timeout-ticks` | true / 10 | 腕を振り続けている間だけダメージ / 最後の腕振りから無効とみなす tick |
| `core.defender-bonus` / `.defender-radius` / `.defender-multiplier` | true / 5 / 0.5 | 防衛ボーナス / 範囲 / ダメージ倍率 |
| `core.no-build-radius` | 3 | コアのまわりの建築禁止（ブロック） |

### ステージ（`stage` ほか）

| キー | 既定値 | 説明 |
|---|---|---|
| `stage.max-build-height` | -1 | 高さの上限（-1 = 範囲の上端Y） |
| `stage.spawn-protect-radius` | 5 | スポーンのまわりの建築禁止。準備中の攻撃側はこの範囲から出られない |
| `stage.kick-outsiders` | false | 範囲内の非参加者（OP・在籍者を除く）を初期スポーンへ移動 |
| `breakable-blocks` | 木の柵・ゲート・ドア・木材など | 城にもとからあるブロックのうち壊してよい種類 |
| `deny-place-blocks` | 水・溶岩・TNT・砂など | 置けないブロック（ドア・ベッドは常に不可） |
| `deny-items` | エンダーパール・エリトラなど | 使えないアイテム |
| `restore.blocks-per-tick` | 500 | 1tick に戻すブロック数（2万ブロックで約2秒） |

### 役職（`kits`）

| キー | 既定値 | 説明 |
|---|---|---|
| `kits.<id>.side` | attack / defense | その役職の陣営 |
| `kits.<id>.items` | 役職ごと | 持ち物（`"素材:個数"`、個数省略で1。ホットバー左から順） |
| `kits.<id>.armor` | 役職ごと | 防具 `[頭, 胴, 脚, 足]`（不要なら AIR） |
| `kits.<id>.effects` | 役職ごと | 試合中ずっと付く効果（`"効果:レベル"`） |
| `kits.<id>.max-health` | 20（剣士は24） | 最大体力（HP） |
| `kits.<id>.repair-hammer` / `.charges` / `.recharge-seconds` | 修理兵 true / 5 / 30 | 修理ハンマー / 回数 / 1回回復する秒数 |

初期の6役職は 攻撃＝剣士・弓兵・工兵、防御＝守備兵・弓兵・修理兵 です。

!!! note "config の運用メモ"
    - `kits` の役職を消しても、自動更新のときに初期の役職が戻ります。使わない役職は、消すのではなく内容を書き換えてください。
    - コアHP・陣営比率は人数に合わせて調整してください（テストプレイで決める前提の初期値です）。

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `siegewar.admin` | OP | `wand`・`menu`・`setstartspawn`・`setlobby`・`setspawn`・`setfield`・`setcore`・`setsign`・`stop`・`restore`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/siege join`・`/siege leave`・`/siege start`・`/siege status` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/siege status` を確認してください。ステージ範囲・攻撃側/防御側スポーン・コアがすべて設定済みで、ロビーに `min-players`（既定4）以上がいる必要があります。未設定の項目は status に表示されます。

??? failure "範囲・スポーン・コアを変更できない"
    試合中・ステージ復元中は変更できません。試合の終了を待つか `/siege stop` で停止し、復元の完了を待ってください。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/siege lobbyfix <プレイヤー>` で復旧できます。退避データは `plugins/SiegeWar/lobby-inventory.yml` に保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

??? failure "統合版でコアにダメージが入らない／途中で止まる"
    統合版は掘る操作の送られ方が Java 版と違うため、**必ず実機で確認してください**。止まる場合は `core.swing-timeout-ticks` を 20 くらいに上げるか、`core.require-swing: false` にしてください（false にすると、一度掘り始めてコアを見続けている間はダメージが入り続けます）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【攻城戦】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。試合結果も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます（「他のミニゲーム ○○ に参加中です」）。サーバー共通のスコアボードタグ（`minigame_lobby:<プラグイン名>`）で所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品を `plugins/SiegeWar/lobby-inventory.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します（試合終了時は復元せず、ロビー在籍中はずっと空のまま）。異常で持ち物が戻らない・ロックが残った場合はOPが `/siege lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は旧ファイルを `config.yml.bak-日時` に退避）。地点・看板・範囲・コアなどのデータ領域は補完対象外です。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも管理ツール・役職選択を使え、登録看板は蝋引きして編集画面が開かないようにします（Floodgate 未導入時は `bedrock.name-prefix` の接頭辞で統合版と見なします）。

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
| `name-tag.display-name` | 攻城戦 | 「【】」に入れる正式名 |
| `name-tag.color` | GOLD | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも管理ツール・役職選択を使える |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 攻城戦（SiegeWar） 概要へ](index.md){ .md-button }
