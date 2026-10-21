<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# RedstoneMover ― OP・運営ガイド { .page-op #redstonemover-op }

RedstoneMover の導入・可動体の設定・`config.yml`・権限・管理コマンド・トラブルシュートをまとめます。地点・経路・センサー・乗り場などはコマンド／管理メニューで登録すると `movers.yml` に自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | RedstoneMover |
| バージョン | `${project.version}`（plugin.yml はプロパティ参照。ビルド成果物に依存） |
| メインコマンド | `/rmover`（別名 `/rmv`） |
| api-version | 26.2 |
| 依存プラグイン | なし（plugin.yml に `depend`/`softdepend` の記載なし） |
| 設定ファイル | `plugins/RedstoneMover/config.yml` |
| メッセージ | `plugins/RedstoneMover/messages.yml` |
| 可動体データ | `plugins/RedstoneMover/movers.yml`（自動生成・自動更新） |
| 権限ノード | `redstonemover.admin`（既定OP） / `redstonemover.ride`（既定 true） |

!!! note "コマンドはすべて OP 専用"
    `/rmover` は `plugin.yml` 側では権限を付けず、**コード側で全サブコマンドに `redstonemover.admin` を要求** します。プレイヤーが使うコマンドはありません。プレイヤーは乗り場の看板・ボタンから乗車します（`redstonemover.ride`・既定 true）。

## 仕組み（可動体のしくみ）

1台の乗り物を **可動体（mover）** と呼び、次の要素で構成されます。

- **経路（waypoint）** … 乗り物が通る道。作成した位置が始点になり、点を追加して線でつなぎます。
- **センサー** … 乗り物を動かす動力源。以下の2方式から選びます。
    - **CLOCK** … 指定した1ブロック（レッドストーンランプ・リピーター・レバーなど）の **信号の立ち上がり（OFF→ON）** を1パルスとして検知。平均間隔から速度を割り出し、1パルスあたり `step` ブロックだけ進みます。クロックが速いほど高速になります。`ONEWAY` / `LOOP` / `PINGPONG` の動き方を選べます。
    - **NOTE** … 経路沿いの **音ブロック** を登録し、回路から鳴らされるたびにその音の経路上の位置へ乗り物を進めます。曲の進行に乗り物が同期します（動き方は常に `ONEWAY`）。`offset` で鳴っている位置より手前／先を走らせられます。
- **見た目（capture）** … 乗り物の外見。範囲のブロックを取り込んで BlockDisplay として表示します（元のブロックは残ります）。`render` を `NONE` にすると座席だけになります。
- **座席（seat）** … 乗客が座る位置。複数登録すると定員が増えます（未登録なら基準位置に1席）。
- **開始ブロック（startblock）** … 出発時に一瞬だけレッドストーンブロックを置いて回路を起動する地点（任意。未設定なら回路は手動で動かす）。
- **乗り場（station）** … 乗客がクリックして乗車する看板・ボタン。
- **降車位置（exit）** … 到着時・降車時にプレイヤーを移す地点。

!!! note "乗車を受け付ける条件（準備完了）"
    可動体が乗車を受け付けるには、**経路（2点以上）・センサー・乗り場** の3つがそろっている必要があります。未設定のものがあると看板に「準備中」と表示されます。`/rmover status`・`/rmover info <id>` で不足項目を確認できます。

## 導入手順

1. ビルドした `RedstoneMover-*.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml`・`messages.yml` と `movers.yml` が自動生成される。
3. 必要に応じて `config.yml` を調整する（更新間隔・速度・出発待ち時間など）。
4. 可動体を作成・設定する（下記「可動体のセットアップ」）。
5. `/rmover status` で各可動体が準備完了かを確認する。
6. 設定を変更したら `/rmover reload` で再読み込みする。

!!! note "動作要件"
    `plugin.yml` の `api-version` は `26.2` で、Paper 1.26 系サーバーを前提としています。座席・見た目には防具立て／ItemDisplay／BlockDisplay、乗り場の看板には蝋引き（waxed）など 1.26 系の API を使用します。

## 可動体のセットアップ（コマンド）

地点・経路・開始ブロックなどは **実行した位置** や **見ているブロック**（5ブロック以内）を使います。すべて `redstonemover.admin` 権限が必要です。まず `create` で作成し、以降は id を指定して設定します。

```text title="可動体を作成（今の立ち位置が経路の始点になる）"
/rmover create <id>
```

```text title="経路に点を追加（今の立ち位置を追加）"
/rmover waypoint <id> add
```

```text title="経路の最後の点を取り消す"
/rmover waypoint <id> undo
```

```text title="経路を始点だけに戻す"
/rmover waypoint <id> clear
```

```text title="経路の点の一覧を表示"
/rmover waypoint <id> list
```

```text title="センサーを CLOCK にする（見ているブロックをクロックとして登録）"
/rmover sensor <id> clock
```

```text title="センサーを NOTE にする（音ブロック方式）"
/rmover sensor <id> note
```

```text title="センサーを外す（運行しなくなる）"
/rmover sensor <id> none
```

```text title="経路沿いの音ブロックを登録（NOTE 方式）"
/rmover scannotes <id>
```

```text title="CLOCK：1パルスで進む距離を設定（0.01〜64）"
/rmover step <id> <距離>
```

```text title="NOTE：先行オフセットを設定（-64〜64／負で手前を走る）"
/rmover offset <id> <距離>
```

```text title="動き方を設定（NOTE では常に ONEWAY 扱い）"
/rmover mode <id> <ONEWAY|LOOP|PINGPONG>
```

```text title="見た目の取り込み範囲：角1（見ているブロック）"
/rmover pos1
```

```text title="見た目の取り込み範囲：角2（見ているブロック）"
/rmover pos2
```

```text title="見た目を取り込む（立ち位置が基準。元のブロックは残る）"
/rmover capture <id>
```

```text title="描画モードを設定（DISPLAY＝ブロック表示／NONE＝座席のみ）"
/rmover render <id> <DISPLAY|NONE>
```

```text title="座席を今の立ち位置に追加"
/rmover seat <id> add
```

```text title="座席を全消去（基準位置に1席だけになる）"
/rmover seat <id> clear
```

```text title="開始ブロックを設定（見ているブロック。出発時に一瞬レッドストーンブロックを置く）"
/rmover startblock <id>
```

```text title="開始ブロックを解除（回路は手動で動かす）"
/rmover startblock <id> clear
```

```text title="降車位置を今の立ち位置に設定"
/rmover exit <id>
```

```text title="乗り場を登録（看板・ボタンを見て実行）"
/rmover setsign station <id>
```

```text title="見ている看板・ボタンの乗り場登録を解除"
/rmover setsign delete
```

```text title="経路・音ブロックなどをパーティクルで表示／非表示"
/rmover show [id]
```

!!! tip "経路表示の色"
    `/rmover show` で表示されるパーティクルは、水色＝経路／白＝点／黄＝音ブロック／赤＝CLOCK ブロック／緑＝開始ブロック／橙＝乗り場／紫＝降車位置、で色分けされます。

!!! note "音ブロック（NOTE）の登録について"
    `scannotes` は経路から `note-scan-radius`（既定3ブロック）以内の音ブロックを探して登録します（経路の点が2つ以上必要）。運行中は **回路から通電して鳴った音だけ** を検知し、プレイヤーが手で叩いた音は無視します。登録範囲が広すぎると `max-scan-blocks`（既定200万）の上限に達するため、経路を分けるか config を調整してください。

## 管理ツール・管理メニュー（コマンドを打たない設定）

コマンドの代わりに、**管理ツール（`/rmover wand` で受け取るアイテム）** と **管理メニュー（GUI）** でも一通りの設定ができます。どちらも `redstonemover.admin` 権限が必要です。

```text title="管理ツールを受け取る"
/rmover wand
```

```text title="管理メニュー（GUI）を開く"
/rmover menu
```

- **管理メニュー** … 可動体の一覧・新規作成・経路編集・センサー切替・音ブロック登録・見た目取り込み・座席・開始ブロック・乗り場登録／解除・降車位置・step/offset・動き方・描画モード・経路表示・有効/無効・強制停止・削除・リロードを GUI からまとめて操作できます。管理ツールを **右クリック**（統合版はタップ／長押し）でも開けます。
- 「見ているブロック」を使う項目（CLOCK・開始ブロック・乗り場・取り込み角）は、**メニューを開いたまま対象を見てボタンを押します**。
- 削除・強制停止などの重要操作は、**5秒以内（削除は10秒以内）に2回** 実行すると確定します。

## config.yml 設定項目

先頭の `config-version` は設定ファイルの版（移行用）で、**手で変えないでください**。値は読み込み時に下表の範囲に丸められます。

### 移動

| キー | 既定値 | 説明（範囲） |
|---|---|---|
| `update-interval-ticks` | 1 | 乗り物の位置を更新する間隔（tick）。統合版でガタつくときは 2 に（1〜20） |
| `sample-count` | 4 | 速度の平均を取るパルス回数（1〜32） |
| `stop-timeout-multiplier` | 2.0 | 一時停止までの待ち時間（平均間隔 × この倍率）（1.0〜20.0） |
| `end-timeout-seconds` | 8 | パルスが来ないまま経過したら運行を終了する秒数（1〜600） |
| `smooth-stop` | true | 一時停止時に減速するか（false でその場停止） |
| `rest-threshold` | 1.8 | CLOCK：平均のこの倍率より長い間隔は休みとみなし平均から除く（1.0〜20.0） |
| `max-speed` | 1.5 | 速度の上限（ブロック/tick）（0.05〜10.0） |
| `correction-rate` | 0.2 | NOTE：位置のずれを1tickで詰める割合（0.0〜1.0） |

### 音ブロック

| キー | 既定値 | 説明（範囲） |
|---|---|---|
| `note-scan-radius` | 3 | `scannotes` で音ブロックを登録する、経路からの距離（ブロック）（0〜16） |

### 運行

| キー | 既定値 | 説明（範囲） |
|---|---|---|
| `busy-hold-seconds` | 3 | 待機中に回路の動きを検知したとき、乗車を受け付けない秒数（0〜60） |
| `start-pulse-ticks` | 2 | 出発時に開始ブロックへレッドストーンブロックを置く長さ（tick）（1〜40） |
| `depart-delay-seconds` | 5 | 乗車してから自動出発までの秒数（0〜120） |
| `allow-dismount` | false | 運行中にスニークで降りられるか |
| `show-progress-bar` | true | 乗客に進み具合のボスバーを表示するか |

### 座席

| キー | 既定値 | 説明（範囲） |
|---|---|---|
| `seat-type` | ARMOR_STAND | 座席の方式（ARMOR_STAND＝透明な防具立て／DISPLAY＝ItemDisplay） |
| `seat-y-offset` | 0.0 | 座席の高さ補正（ブロック）（-3.0〜3.0） |

### チャンク

| キー | 既定値 | 説明（範囲） |
|---|---|---|
| `chunk-margin` | 16 | 運行中に経路の周りで読み込み続ける幅（ブロック）（0〜128） |
| `max-ticket-chunks` | 256 | 運行中に読み込み続けるチャンク数の上限（1〜4096） |

### 上限・その他

| キー | 既定値 | 説明（範囲） |
|---|---|---|
| `max-movers` | 50 | 可動体の登録上限（1〜10000） |
| `max-blocks-per-mover` | 256 | 1台の見た目ブロック数の上限（1〜4096） |
| `max-waypoints` | 512 | 経路の点の上限（2〜10000） |
| `max-scan-blocks` | 2000000 | `scannotes` で調べるブロック数の上限（最小1000） |
| `default-render-mode` | DISPLAY | 新規作成時の描画モード（DISPLAY／NONE） |
| `display-name` | RedstoneMover | 管理ツール名などに使う正式名称 |
| `debug` | false | パルスや速度をコンソールに出力（調整用） |

!!! note "設定の自動追記（ConfigUpdater）"
    起動時、`config.yml`・`messages.yml` に不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません）。更新が入った場合は旧ファイルを `config.yml.bak-日時` などに退避します。可動体データ（`movers.yml`）は別ファイルで管理されます。

!!! note "設定変更後は `/rmover reload`"
    `/rmover reload` を実行すると、運行中の可動体を停止してから `config.yml`・`messages.yml`・`movers.yml` を読み直します（`redstonemover.admin` が必要）。

## 管理コマンド

すべて `redstonemover.admin`（既定OP）が必要です。`[id]` を省略できるコマンドは、登録が1台だけならその1台を対象にします。

| コマンド | 説明 |
|---|---|
| `/rmover wand` | 管理ツール（アイテム）を受け取る |
| `/rmover menu` | 管理メニュー（GUI）を開く |
| `/rmover status` | 設定状況・稼働状況を表示 |
| `/rmover list` | 可動体の一覧 |
| `/rmover info [id]` | 可動体の詳細（経路・センサー・乗り場・運行状況など） |
| `/rmover create <id>` | 可動体を作成（立ち位置が経路の始点） |
| `/rmover remove <id>` | 削除（10秒以内に再実行で確定） |
| `/rmover enable\|disable [id]` | 有効／無効（無効化で運行中なら停止） |
| `/rmover stop [id]` | 運行を強制停止（id 省略で稼働中のものを停止） |
| `/rmover waypoint <id> <add\|undo\|clear\|list>` | 経路の編集 |
| `/rmover sensor <id> <clock\|note\|none>` | センサーの設定（clock は見ているブロック） |
| `/rmover scannotes <id>` | 経路沿いの音ブロックを登録 |
| `/rmover step <id> <距離>` | CLOCK の1パルスの移動量（0.01〜64） |
| `/rmover offset <id> <距離>` | NOTE の先行オフセット（-64〜64） |
| `/rmover mode <id> <ONEWAY\|LOOP\|PINGPONG>` | 動き方（NOTE は常に ONEWAY） |
| `/rmover render <id> <DISPLAY\|NONE>` | 描画モード |
| `/rmover pos1` / `/rmover pos2` | 見た目の取り込み範囲の角（見ているブロック） |
| `/rmover capture <id>` | 見た目を取り込む（立ち位置が基準） |
| `/rmover seat <id> <add\|clear>` | 座席の追加／全消去 |
| `/rmover startblock <id> [clear]` | 開始ブロックの設定／解除（見ているブロック） |
| `/rmover exit <id>` | 降車位置を立ち位置に設定 |
| `/rmover setsign station <id>` | 乗り場（看板・ボタン）を登録（見て実行） |
| `/rmover setsign delete` | 見ている乗り場の登録を解除 |
| `/rmover show [id]` | 経路・音ブロックなどのパーティクル表示を切替 |
| `/rmover reload` | `config.yml`・`messages.yml`・`movers.yml` を再読み込み |

!!! note "設定を変えられないとき"
    運行中・乗車待ちの可動体は設定を変更できません（`{id} は運行中・乗車待ちのため、設定を変えられません` と表示）。`/rmover stop <id>` で止めてから設定してください。

## 権限ノード

`plugin.yml` で定義されている権限は次の2つです。

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `redstonemover.admin` | op | `/rmover` 全サブコマンド・管理ツール・管理メニューの利用。乗り場ブロックの破壊（破壊すると登録解除） |
| `redstonemover.ride` | true（全員） | 乗り場から乗車する |

!!! info "乗車とミニゲームの関係"
    プレイヤーがミニゲームのロビー・試合に参加中（サーバー共通の `minigame_lobby:` スコアボードタグ保持中）は乗車できません（`ミニゲーム参加中は乗れません`）。スペクテイターも乗車できません。乗り場の看板・ボタンは、登録されると一般プレイヤーの破壊・看板編集・ボタン作動が抑止されます。

!!! example "LuckPerms 設定例"
    ```bash
    # 運営スタッフに管理権限
    /lp group staff permission set redstonemover.admin true

    # 特定グループの乗車を禁止する例（既定は全員 true）
    /lp group guest permission set redstonemover.ride false
    ```

## トラブルシューティング

??? failure "乗り物が動かない（出発しても進まない）"
    センサーの動力源が動いているか確認してください。CLOCK は登録ブロックの信号が ON/OFF を繰り返しているか、NOTE は経路沿いの音ブロックが **回路から** 鳴っているか（手で叩いた音は無視されます）。開始ブロックを設定していない場合は、回路を手動で起動する必要があります。出発後 `end-timeout-seconds`（既定8秒）パルスが来ないと運行は中止されます。

??? failure "看板に「準備中」と出る／乗車できない"
    経路（2点以上）・センサー・乗り場の3つがそろっていません。`/rmover status`・`/rmover info <id>` で不足項目を確認してください。

??? failure "乗り物がガタつく・カクつく（特に統合版）"
    `config.yml` の `update-interval-ticks` を 2 にする、`seat-type` を `DISPLAY` に切り替える、`seat-y-offset` を微調整する、などを試してください。

??? failure "運行中にチャンクが読み込まれず止まる／範囲外で消える"
    回路や音ブロックが経路から離れている場合は `chunk-margin` を大きくしてください。読み込み続けるチャンクが `max-ticket-chunks` を超えると一部しか維持されず警告が出ます。

??? failure "開始ブロックのレッドストーンブロックが残ってしまった"
    開始ブロックは出発時に一瞬だけ置かれ、`start-pulse-ticks` 後に元へ戻します。サーバークラッシュ等で戻せなかった分は、次回起動時に自動で元のブロックへ戻されます（`前回戻せなかったブロックを … 個戻しました`）。

??? failure "乗り場を壊してしまった"
    乗り場ブロックを壊すと（管理者のみ破壊可）その乗り場の登録は自動解除されます。再登録は `/rmover setsign station <id>` を看板・ボタンを見て実行してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← RedstoneMover 概要へ](index.md){ .md-button }
