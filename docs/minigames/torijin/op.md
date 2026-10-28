<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# 鳥人間コンテスト ― OP・運営ガイド { .page-op #torijin-op }

鳥人間コンテスト（ToriJin）の導入・アリーナ設定・看板・config・権限・管理コマンドをまとめます。地点・看板・アリーナは `arenas.yml`、記録は `records.yml` に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | ToriJin |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/tori`（別名 `/torijin`） |
| api-version | 26.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/ToriJin/config.yml` |
| データ | `plugins/ToriJin/arenas.yml`（アリーナ・看板・地点）・`records.yml`（記録） |
| 権限ノード | `torijin.admin`（既定OP） |

## 導入手順

1. ビルドした `ToriJin` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される（挑戦回数・打ち上げ方式などは初期値で動作）。
3. `/tori setstartspawn`・`/tori setlobby` で初期スポーン（離脱時の戻り先）とロビーを設定する。
4. `/tori create <アリーナ>` でアリーナを作り、**コース方向を向いて** `/tori setorigin` を実行する。
5. `/tori setbatter`（打ち担当の立ち位置）・`/tori setreceiver`（受け担当の開始位置）・`/tori setzone`（ストライクゾーン）を設定する。必要に応じてコースの長さ・幅・下限を調整する。
6. 参加・離脱・役職・開始・ランキングの看板を設置する。
7. `/tori status` で設定状況（開始可能かどうか）を確認する。

!!! warning "会場ワールドの PvP を有効に"
    打ち上げは打撃のダメージイベントで起きるため、**会場ワールドの PvP が無効だと打撃が通りません**。PvP を有効にしてください（開始前にもチェックが入り、無効だと警告が出ます）。`hit-damage` は 0 にしないでください（0 だとノックバックが起きません）。

!!! note "アリーナに必須の設定"
    1つのアリーナを開始可能にするには、**原点・打ち担当の立ち位置・受け担当の開始位置・ストライクゾーン** の4つが必要です。未設定の項目は `/tori status` や開始時に表示されます。

## セットアップ手順（コマンド）

地点・原点・立ち位置・ゾーンは **実行した位置** を使い、看板系は看板を **見ながら**（5ブロック以内）実行します。すべて `torijin.admin` 権限が必要です。アリーナ名を省略すると、参加中ならそのアリーナ、登録が1つならそれが対象になります。

```text title="初期スポーン（離脱時の戻り先／その場に立って実行）"
/tori setstartspawn
```

```text title="受付ロビー（その場に立って実行）"
/tori setlobby
```

```text title="アリーナを作成（名前は半角英数字・_・- のみ）"
/tori create <アリーナ>
```

```text title="原点を設定（コースの開始点。向いている方向がコース方向になる）"
/tori setorigin [アリーナ]
```

```text title="打ち担当の立ち位置（その場に立って実行）"
/tori setbatter [アリーナ]
```

```text title="受け担当の開始位置（その場に立って実行）"
/tori setreceiver [アリーナ]
```

```text title="ストライクゾーンの中心を設定（半径を省略すると config の既定値）"
/tori setzone [アリーナ] [半径]
```

```text title="コースの長さを設定（m。100〜30000。既定5000）"
/tori setlength [アリーナ] <マス>
```

```text title="コースの幅を設定（マス。10〜1000。既定50）"
/tori setwidth [アリーナ] <マス>
```

```text title="コースの下限 Y を設定（この高さより下に落ちたら着地判定／その場に立って実行）"
/tori setminy [アリーナ]
```

```text title="アリーナを削除（看板の登録も解除・記録は残る）"
/tori remove <アリーナ>
```

```text title="参加看板を登録（アリーナ指定・看板を見て実行）"
/tori setsign join <アリーナ>
```

```text title="離脱看板を登録（看板を見て実行）"
/tori setsign leave
```

```text title="開始看板を登録（アリーナ指定・看板を見て実行）"
/tori setsign start <アリーナ>
```

```text title="役職看板を登録（アリーナと役職 uchi|uke を指定・看板を見て実行）"
/tori setsign role <アリーナ> <uchi|uke>
```

```text title="ランキング看板を登録（アリーナと順位を指定・看板を見て実行）"
/tori setsign rank <アリーナ> <順位>
```

```text title="視線先の看板の登録を解除"
/tori setsign delete
```

!!! note "ゾーンとコースについて"
    ストライクゾーンは中心からの **半径**（既定2.5・アリーナごとに上書き可）と **高さ**（中心から上へ `zone-height` マス）で決まります。受け担当はこのゾーンで滑空して打たれます。コースの長さ（`setlength`）まで届くと「場外」、横ずれがファウルラインを超えるとファウルです。挑戦中のアリーナは地点を変更できません（止めてから設定）。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドを打たずに、**管理ツール** と **管理メニュー（GUI）** でも設定できます。どちらも `torijin.admin` 権限が必要です。

```text title="管理ツールを受け取る（右クリック／タップで管理メニューを開く）"
/tori wand
```

```text title="管理メニュー（GUI）を開く"
/tori menu
```

管理ツールは参加中・テスト中は受け取れません。先に離脱してから実行してください。

## 運用コマンド

```text title="1人テスト（受け担当として1人で試す／記録なし・自動で打ち出す）"
/tori test [アリーナ]
```

```text title="進行を強制停止（挑戦中のペアは列の先頭に戻る・在籍は維持）"
/tori stop [アリーナ]
```

```text title="そのアリーナのランキングを消す（個人ベストは残る）"
/tori resetrank <アリーナ>
```

```text title="config / messages を再読み込み"
/tori reload
```

```text title="ロビー前の持ち物を手動復旧（force で退避データ破棄・ロック解除）"
/tori lobbyfix <プレイヤー> [force]
```

## config.yml 設定項目

アリーナ・看板・地点・記録はデータファイル（`arenas.yml` / `records.yml`）に自動保存されます。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。以下は挙動の設定項目です。

### 進行

| キー | 既定値 | 説明 |
|---|---|---|
| `attempts` | 3 | 1ペアの挑戦回数 |
| `swap-roles` | false | 回ごとに役割を交代するか（偶数回で打ち・受けが入れ替わる） |
| `countdown-seconds` | 3 | 各回の開始カウント（秒） |
| `swing-timeout` | 30 | 有効な打撃がないまま回を終えるまでの秒数（0で無制限） |
| `result-seconds` | 3 | 結果を表示してから次の回に進むまでの秒数 |

### 打ち上げ

| キー | 既定値 | 説明 |
|---|---|---|
| `launch-mode` | VANILLA | VANILLA（バニラのノックバック・ランキング対象）／CONTROLLED（プラグインが速度を決める予備方式・別枠） |
| `knockback-level` | 255 | バットのノックバックのレベル（1〜255） |
| `launch-max-speed` | 3.9 | 打ち出し速度の上限（1軸あたりブロック/tick。0で上限なし） |
| `hit-damage` | 0.01 | 有効な打撃のダメージ（0 にするとノックバックが起きないため 0 にしない） |
| `launch-power` | 3.0 | CONTROLLED の打ち上げの強さ（ブロック/tick） |
| `launch-angle` | 35 | CONTROLLED の打ち上げの仰角（度） |

!!! note "launch-max-speed について"
    1.21.9 以降はノックバックの速度に上限が無く、レベル255だと速すぎてサーバーに「moved too quickly」で引き戻されます。昔のバージョン相当の上限（約3.9）をかけることで安定します。

### 有効な打撃の条件（`hit-check`）

| キー | 既定値 | 説明 |
|---|---|---|
| `hit-check.mode` | TIME | TIME＝受付時間中なら条件なしで打てる（既定）／STRICT＝下の条件をすべて満たしたときだけ打てる |
| `hit-check.min-glide-ticks` | 4 | （STRICT）滑空開始から打てるまでの tick |
| `hit-check.min-pitch` | 20 | （STRICT）受け担当に必要な上向きの角度（度） |
| `hit-check.min-height` | 1.0 | （STRICT）足元から地面までに必要な高さ（マス） |
| `hit-check.min-charge` | 0.9 | （STRICT）打ち担当の攻撃のための最低値（0〜1・Java版） |
| `hit-check.min-charge-bedrock` | 0.5 | （STRICT）統合版の打ち担当のための最低値 |

### ストライクゾーン・立ち位置

| キー | 既定値 | 説明 |
|---|---|---|
| `zone-radius` | 2.5 | ゾーンの半径（既定値。アリーナごとに上書き可） |
| `zone-height` | 6 | ゾーンの高さ（中心から上へ何マスまで） |
| `batter-leash` | 2 | 打ち担当が立ち位置から離れられる距離（超えると戻す） |
| `receiver-leash` | 30 | 打撃待ちの受け担当がゾーンから離れられる距離（超えると開始位置へ戻す） |

### 判定・表示

| キー | 既定値 | 説明 |
|---|---|---|
| `max-lateral` | 20 | これ以上横にずれて着地したらファウル（コース幅の半分が小さければそちら） |
| `flight-timeout` | 120 | 飛行の上限（秒）。引っかかって着地しないとき、この時間で現在位置を判定（0で無制限） |
| `lateral-warn` | 15 | 警告を出す横ずれ（表示のみ） |
| `aim-warn` | 5 | 照準のずれを黄色で表示する角度（度） |
| `landing-marker-seconds` | 5 | 着地点にパーティクルの柱を出す秒数（0で出さない） |
| `default-course-length` | 5000 | 新しいアリーナのコースの長さ |
| `default-course-width` | 50 | 新しいアリーナのコースの幅 |

### ランキング・その他

| キー | 既定値 | 説明 |
|---|---|---|
| `ranking-size` | 10 | ランキングの表示件数 |
| `bedrock-mark` | ` [統]` | 受け担当が統合版の記録に付ける印 |
| `scoreboard.max-names` | 5 | 各役職にスコアボードへ表示する人数（超えた分は「ほか N人」） |
| `bat-item` | STICK | 打ち担当の道具 |
| `debug` | false | 打撃の条件チェック結果・初速・仰角をログに出す |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `torijin.admin` | OP | `wand`・`menu`・`create`・`remove`・`setstartspawn`・`setlobby`・`setorigin`・`setbatter`・`setreceiver`・`setzone`・`setlength`・`setwidth`・`setminy`・`setsign`・`test`・`stop`・`resetrank`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/tori join`・`/tori leave`・`/tori role`・`/tori start`・`/tori status`・`/tori top` は権限チェックがなく、全プレイヤーが使えます。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/tori setstartspawn` | 初期スポーン（離脱時の戻り先）を設定 |
| `/tori setlobby` | ロビー地点を設定 |
| `/tori create <アリーナ>` | アリーナを作成 |
| `/tori remove <アリーナ>` | アリーナを削除（看板の登録も解除・記録は残る） |
| `/tori setorigin [アリーナ]` | 原点とコース方向を設定 |
| `/tori setbatter [アリーナ]` | 打ち担当の立ち位置を設定 |
| `/tori setreceiver [アリーナ]` | 受け担当の開始位置を設定 |
| `/tori setzone [アリーナ] [半径]` | ストライクゾーンを設定 |
| `/tori setlength [アリーナ] <マス>` | コースの長さを設定 |
| `/tori setwidth [アリーナ] <マス>` | コースの幅を設定 |
| `/tori setminy [アリーナ]` | コースの下限 Y を設定 |
| `/tori setsign <join <ア>\|leave\|start <ア>\|role <ア> <uchi\|uke>\|rank <ア> <順位>\|delete>` | 看板を登録／解除 |
| `/tori wand` | 管理ツールを受け取る |
| `/tori menu` | 管理メニュー（GUI）を開く |
| `/tori test [アリーナ]` | 1人テスト（記録を残さない） |
| `/tori stop [アリーナ]` | 進行を強制停止 |
| `/tori resetrank <アリーナ>` | ランキングを消す（個人ベストは残る） |
| `/tori reload` | 設定を再読み込み |
| `/tori lobbyfix <プレイヤー> [force]` | ロビー前の持ち物の手動復旧 |

## トラブルシューティング

??? failure "開始できない／打撃が通らない"
    `/tori status` でアリーナの4項目（原点・打ち立ち位置・受け開始位置・ゾーン）がそろっているか確認してください。打撃が通らない場合は、会場ワールドの **PvP が有効** か、`hit-damage` が 0 になっていないかを確認します。

??? failure "受け担当が打ち出されても引き戻される"
    打ち出し速度が速すぎると「moved too quickly」で戻されます。`launch-max-speed`（既定3.9）が 0 や極端に大きい値になっていないか確認してください。

??? failure "アリーナの地点を変更できない"
    そのアリーナで挑戦中（ペア成立中）は地点を変更できません。`/tori stop` で止めてから設定してください。

??? failure "ロビーに入る前の持ち物が戻らない"
    `/tori lobbyfix <プレイヤー>` で復旧できます。退避データはファイルに保存され、サーバー再起動・クラッシュをまたいでも復元されます。どうしても戻らない場合のみ `force` を付けると退避データを破棄してロックだけ解除します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【鳥人間コンテスト】名前」になり、離脱で元に戻ります（表示名・色は config の `name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。ランキング入り・自己ベスト更新も全体へ告知されます。
- **同時参加は1ゲームまで** … 他ミニゲームに参加中は参加が拒否されます。サーバー共通のスコアボードタグで所有権を管理しています。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品をファイルに保存し、**サーバークラッシュ後の再ログインでも復元** します。異常で持ち物が戻らない・ロックが残った場合はOPが `/tori lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に `config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません）。アリーナ・看板・記録などのデータは別ファイルで管理します。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも管理ツールを使え、登録看板は蝋引きして編集画面が開かないようにします。統合版の受け担当の記録には印が付きます。

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
| `name-tag.display-name` | 鳥人間コンテスト | 「【】」に入れる正式名 |
| `name-tag.color` | GOLD | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも管理ツールを使える |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← 鳥人間コンテスト 概要へ](index.md){ .md-button }
