<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# オセロ（Othello） ― OP・運営ガイド { .page-op #othello-op }

オセロ（実物大オセロ）の導入・盤／スポーン／看板設定・盤の敷設・config・権限・管理コマンドをまとめます。地点・盤・看板はコマンドで登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Othello |
| メインコマンド | `/othello`（エイリアス `/oth`） |
| api-version | 26.1.2 |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Othello/config.yml` |
| 記録ファイル | `plugins/Othello/stats.yml`（対人戦・CPU戦の勝敗）／`plugins/Othello/solo-records.yml`（CPU段位戦） |
| 権限ノード | `othello.admin`（既定OP） |

## 導入手順

1. ビルドした `Othello` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される。
3. `/othello wand` で設定ツールを受け取ると、以下の設定をコマンドなしで行えます（コマンドでも可）。
4. `/othello setstartspawn`・`/othello setlobby` で初期スポーンとロビーを設定する。
5. 盤の基準点（a1マス）を `/othello setboard` で設定し、`/othello buildboard` で盤の床を敷く。
6. 黒・白の観戦（着席）位置を `/othello setspawn black`・`setspawn white` で設定する。
7. 参加・離脱・開始の看板と、**ソロ看板（CPU段位戦の入口）** を設置し、`/othello status` で確認する。

## 設定ツール（ワンド）

```text title="設定ツールを受け取る（OP限定・ロビー参加中は受け取れません）"
/othello wand
```

```text title="設定メニューを直接開く（統合版でしゃがみ＋タップが効かないとき）"
/othello menu
```

ブレイズロッドの「オセロ 設定ツール」で、見ているブロック・看板に直接セットアップできます。受け取り時・使用時の両方で `othello.admin` を確認するため、OP以外が手に入れても使えません。

| 操作 | 動作 |
|---|---|
| 左クリック／長押し（ブロック） | そのブロックを盤の基準点（a1）にする（`setboard` 相当）。範囲を黄色の枠で表示 |
| 右クリック／タップ（看板） | 選んでいる看板モードで登録・解除する（モードなしなら登録状況を表示） |
| 右クリック／タップ（空中・ブロック） | 設定状況の要約＋盤の枠を表示 |
| しゃがみ＋左クリック／長押し | 盤の枠を表示 |
| しゃがみ＋右クリック／タップ | 設定メニューを開く |

設定メニューでできること（すべて即保存）：

- **地点**：初期スポーン／ロビー／黒・白のスポーンを現在地に設定、盤の基準点（視線先、なければ足元）、盤の床を敷く、盤の枠を表示
- **看板モード**：参加／離脱／開始（対人戦・CPU やさしい・ふつう・つよい）／ソロ（CPU段位戦）／登録解除／モード解除
- **数値**：手番の制限時間（5秒刻み）、開始カウントダウン、1マスのブロック数（1〜4・対局中は不可）、ロビー定員
- **切り替え**：ソロ（CPU段位戦）の有効／無効、置けるマスのヒント、めくるアニメーション、直前の石を光らせる、時間切れの扱い（ランダム／負け）、勝ち残り、CPU戦のプレイヤーの色（ランダム→黒→白）
- **進行**：対局の強制停止、設定の再読み込み、設定ツールの回収

!!! note "統合版（Geyser）での操作"
    統合版はブレイズロッドで空中をタップしてもサーバーに届かないため、**空中タップ（腕振り）で設定状況の表示**になります。しゃがみ＋タップでメニューが開かないときは `/othello menu` を使ってください。

## セットアップ手順（コマンド）

地点系は **実行した位置**、看板系は看板を **見ながら** 実行します。すべて `othello.admin` 権限が必要です。

```text title="初期スポーン（途中抜けの戻り先）"
/othello setstartspawn
```

```text title="受付ロビー"
/othello setlobby
```

```text title="黒／白のプレイ位置を設定"
/othello setspawn black
/othello setspawn white
```

```text title="盤の基準点（a1マス）を現在地に設定"
/othello setboard
```

```text title="盤の床を敷き直す（cell-size・床素材の変更後にも実行）"
/othello buildboard
```

```text title="参加／離脱／開始の看板を登録（看板を見て実行）"
/othello setsign join
/othello setsign leave
/othello setsign start classic
```

```text title="ソロ看板（CPU段位戦の入口）を登録（看板を見て実行）"
/othello setsign solo
```

設定ツール（ワンド）でも、メニューの **「ソロ看板モード」** を選んで看板を右クリック／タップすれば登録できます。

!!! note "盤の敷設（buildboard）"
    `/othello setboard` で盤の基準点（a1）を決めたあと、`/othello buildboard` を実行すると **8×8の市松模様の床が自動で敷かれます**。1マスのブロック数（`board.cell-size`）や床素材（`floor-a`/`floor-b`）を変更したら、再度 `buildboard` で敷き直してください。

## CPU段位戦（ソロモード）

1人で CPU に何段まで勝ち抜けるかを競うモードです。遊び方は [プレイヤー向けページ](player.md#cpu段位戦ソロモード) を見てください。

- 入口は **ソロ看板** と `/othello solo`。ソロで入った人は **ソロ在籍** になり、対人戦には出場しません（`/othello join` で通常在籍に戻れます）。
- 盤面は対人戦と共用です。どちらかが対局中は、もう一方は始められません（自動では始めません）。
- 今の CPU の開始看板・`/othello start cpu-○○` は、押した本人の **練習1局** になります。コンソール・コマンドブロックから実行した場合だけ、今までどおり待機列の先頭の人が出場します。
- ソロの対局には **ほかの在籍者が観戦** できます（ボスバーも見えます）。
- 記録は `solo-records.yml`（データ用。ConfigUpdater の対象外）。段位戦で使った段位が今の cpu-easy / normal / hard にあたるときは、`stats.yml` の CPU 戦績にも数えます。

### 段位の調整

段位は config の `solo.dan.stages`（リスト）で決めます。**上から順に勝ち抜く** 順番です。行を足す・消す・並べ替えると段位が変わります（消した段位の記録は「（削除された段位）」と表示され、順位は突破数で比べます）。

| 項目 | 説明 |
|---|---|
| `id` | 記録のキー。`.` と空白は使えない。**変えると今までの記録とつながらない** |
| `display` | 表示名（例：3級） |
| `icon` | ソロメニューのアイコン（ブロック・アイテム名） |
| `depth` | 読みの手数。0＝ランダム、1＝1手読み、2以上＝探索 |
| `solve` | 空きマスがこの数以下なら最後まで読み切る（0＝しない） |
| `mistake` | ミス率（0.0〜1.0。その確率でランダムな手を打つ） |
| `strong-eval` | 確定石・角の取られ方・開放度も見る評価を使う（2手以上読むときに強くなる） |

既定の8段階は、となりの段位どうしで100局ずつ対局させて、**上の段位が7割前後勝つ** ように調整しています。

!!! tip "調整のコツ"
    - `strong-eval: false` で偶数の手数（2・4…）を読ませると、1つ下の段位とあまり差が出ません。2手以上読む段位は `strong-eval: true` がおすすめです。
    - 思考時間は既定で1手 **最大1.5秒**（`solo.dan.max-think-ms`）。超えそうなときは読み終えた一番深い結果で打つので、深くしすぎてもサーバーは止まりません。
    - 既定の三段（6手読み・14マス読み切り）は、1手あたり最長で約0.9秒でした（テスト環境：2コア）。

```text title="段位戦の記録を消す（1人／全員。コンソールからも可）"
/othello ranking reset <プレイヤー>
/othello ranking reset all
```

!!! note "統合版（Geyser）"
    ソロメニューはチェスト画面で表示・タップ操作できます。統合版はネザースター（アイテムに使う動作が無い）で空中をタップしてもサーバーに届かないため、**空中タップ（腕振り）** と **持ってスニーク** でもメニューが開くようにしています。lore の改行や色の見え方は実機で確認してください。

## config.yml 設定項目

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `max-lobby` | 16 | ロビーに入れる人数（出場者以外は観戦） |
| `countdown-seconds` | 3 | 開始カウントダウン |
| `turn-seconds` | 30 | 1手の制限時間（秒） |
| `timeout-action` | random | 時間切れ時：`random`＝ランダムに置く／`forfeit`＝負け |
| `result-seconds` | 8 | 終局後、盤面を残す秒数 |

### 盤面（`board`）

| キー | 既定値 | 説明 |
|---|---|---|
| `board.cell-size` | 2 | 1マスのブロック数（1〜4）。変更したら `buildboard` で敷き直す |
| `board.floor-a` | GREEN_CONCRETE | 市松模様の床A |
| `board.floor-b` | LIME_CONCRETE | 市松模様の床B |
| `board.black-stone` | BLACK_CONCRETE | 黒石の素材 |
| `board.white-stone` | WHITE_CONCRETE | 白石の素材 |
| `board.stone-margin` | 0.1 | 石の余白（マスに対する割合） |
| `board.stone-thickness` | 0.15 | 石の厚み（ブロック） |

### 操作・表示・待機列・CPU

| キー | 既定値 | 説明 |
|---|---|---|
| `click-range` | 32 | 盤を指せる距離（ブロック） |
| `hints` | true | 置けるマスを本人にだけ緑の粒で表示 |
| `flip-animation` | true | 石をめくるアニメーション |
| `glow-last-move` | true | 直前に置いた石を光らせる |
| `queue.winner-stays` | true | 対人戦の勝者を次の対局にも続けて出す（勝ち残り） |
| `cpu.think-ticks` | 20 | CPUが打つまでの最低待ち時間（20＝1秒） |
| `cpu.player-color` | random | CPU戦のプレイヤーの色（`random`/`black`＝先手/`white`＝後手） |

### ソロモード（`solo`）

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | true | false でソロを無効にする（ソロメニュー・ソロ看板から始められない）。設定メニューでも切り替え可 |
| `solo.menu-delay` | 3 | 対局が終わってからソロメニューを開き直すまで（秒） |
| `solo.broadcast-result` | true | 段位戦の結果をサーバー全体に流す（1段も突破できなかったときは流さない） |
| `solo.ranking-size` | 10 | `/othello ranking dan` に表示する人数 |
| `solo.dan.draw-clears` | false | true で引き分けを突破扱いにする |
| `solo.dan.max-think-ms` | 1500 | CPU の1手の思考時間の上限（ミリ秒。CPU戦すべてに効く） |
| `solo.dan.stages` | 8段階 | 段位のリスト（上の「段位の調整」） |
| `solo.legacy-modes.cpu-easy／cpu-normal／cpu-hard` | kyu5／kyu4／kyu1 | 今の CPU の開始看板・`start cpu-○○` を、どの段位の練習1局にするか |

!!! note "既存サーバーへの反映"
    更新後の起動時に、ConfigUpdater が `solo` ブロックを config.yml に自動で追加します（今の値は上書きしません。旧ファイルは `config.yml.bak-日時` に退避）。config.yml を作り直す必要はありません。

### 環境・ロビー共通インベントリ

| キー | 既定値 | 説明 |
|---|---|---|
| `keep-food` | true | ロビー在籍中は満腹度を減らさない |
| `protect-lobby` | true | ロビー在籍中はダメージを受けない |
| `lobby-inventory.*` | ― | ロビー入退室時の所持品退避・復元（`enabled`/`gamemode`/`clear-effects`/`reset-health`/`reset-exp`/`expire-days`） |

!!! note "自動生成される領域（手動編集不要）"
    `default-spawn` / `lobby-spawn` / `spawns`（black/white）/ `board.origin` / `signs`（看板）はコマンドで自動保存されます。設定変更は次の対局から反映されます（`/othello reload` で再読み込み）。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/othello wand` | 設定ツール（ワンド）を受け取る |
| `/othello menu` | 設定メニュー（GUI）を開く |
| `/othello setstartspawn` | 初期スポーンを設定 |
| `/othello setlobby` | ロビーを設定 |
| `/othello setspawn <black\|white>` | 黒／白のプレイ位置を設定 |
| `/othello setboard` | 盤の基準点（a1）を設定 |
| `/othello buildboard` | 盤の床を敷く／敷き直す |
| `/othello setsign <join\|leave\|start <モード>\|delete>` | 看板を設定／解除 |
| `/othello setsign solo` | ソロ看板（CPU段位戦の入口）を登録 |
| `/othello ranking reset <プレイヤー\|all>` | 段位戦の記録を消す（コンソールからも可） |
| `/othello stop` | 対局を強制終了（段位戦の対局なら、その対局は無効で挑戦は続く） |
| `/othello reload` | config を再読み込み |
| `/othello leave <プレイヤー>` | 指定プレイヤーを強制離脱させる（OP限定。引数なしは全員可の自己離脱） |
| `/othello lobbyfix <プレイヤー> [force]` | ロビー退避データを手動で復旧 |
| `/othello status` | 設定状況・現在の状況を確認（全員可） |
| `/othello top` / `stats` | ランキング／成績を表示（全員可） |
| `/othello solo [dan\|<段位>]` / `ranking dan` | ソロ（CPU段位戦）／段位戦ランキング（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `othello.admin` | OP | セットアップ・盤敷設・看板設置・設定ツール／メニュー・強制停止・lobbyfix など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/othello join`・`/othello leave`・`/othello start <モード>`・`/othello solo`・`/othello resign`・`/othello status`・`/othello top`・`/othello stats`・`/othello ranking dan` は権限チェックがなく、全プレイヤーが使えます。

## トラブルシューティング

??? failure "対局が開始できない"
    `/othello status` を確認してください。盤（`setboard`＋`buildboard`）・黒/白のプレイ位置・ロビーが設定されている必要があります。

??? failure "ソロ（段位戦）が始められない"
    ソロメニューの開始ボタンがバリアのときは、理由が表示されています。「盤面を使用中」は対人戦か他の人のソロが対局中、「ソロは無効」は `solo.enabled: false` です。`/othello status` の [ソロ（CPU段位戦）] でも確認できます。

??? failure "石を置いても反応しない"
    `click-range`（既定32）以内から、置けるマス（緑の粒が出る合法手）をクリックしているか確認してください。自分の手番であることも必要です。

??? failure "盤の床がずれている・マスが合わない"
    `board.cell-size` を変えたら必ず `/othello buildboard` で敷き直してください。`setboard` の基準点（a1マス）の位置も確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← オセロ 概要へ](index.md){ .md-button }
