<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ElytraRace ― OP・運営ガイド { .page-op #elytrarace-op }

ElytraRace の導入・コース作成・看板設置・config.yml・権限・管理コマンド・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | ElytraRace |
| バージョン | 1.0.0 |
| 対応サーバー | Paper 1.26.x（api-version 26.1.2） |
| メインコマンド | `/elytrarace`（エイリアス `/er`）/ join・leave・start・solo・practice・retry・ghost・ranking・stats・status は全員可、設定系は `elytrarace.admin` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/ElytraRace/config.yml`（設定・地点・コース・看板をすべて保存） |
| 記録ファイル | `plugins/ElytraRace/records.db`（SQLite。ベストタイム・統計。自動生成） |

!!! success "実装状況：新規追加ゲーム"
    エリトラ飛行によるチェックポイント・レース、**対戦レース／ソロ（ゴースト・区間タイム付き。旧・練習モード）の2モード**、ロケット花火の自動補充、疑似死亡（クラッシュ時のチェックポイント送還）、パーティクルによるリング可視化、順位サイドバー、SQLiteによるベストタイム記録・ランキング、看板6種（参加／離脱／開始／ソロ（メニュー）／ソロ（コース指定・旧練習）／ランキング）に対応しています。

## 導入手順

1. ビルドした `ElytraRace-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/ElytraRace/config.yml` が自動生成され、記録用の `records.db` も自動作成される。
3. エリトラで飛べるレース専用ワールド（開けた空間・落下地形など）を用意する。
4. 後述の手順でロビー・初期スポーン・コース（開始地点／チェックポイント／ゴール）を作成する。
5. 参加・開始などの看板を設置する。
6. 設定を変更したら `/elytrarace reload` で再読み込みする。

!!! note "既存サーバーを更新する場合（config は自動追記されます）"
    本プラグインは起動時に、サーバー上の `config.yml` を jar 内の最新版と突き合わせ、**不足しているキーを既定値＋コメント付きで自動追記** します（既存の値は変更しません／追記があったときだけ更新前を `config.yml.bak-日時` に退避）。バージョンアップ後に新しい設定項目が追加された場合も、起動するだけで反映されます。地点・コース・看板などのデータ領域（`signs` / `courses` / `default-spawn` / `lobby-spawn`）は補完対象外で、**各設定コマンドの実行時に即 `config.yml` へ自動保存** されます（手書き編集は不要）。

!!! tip "サーバー実装について"
    Paper 1.26.x（api-version 26.1.2）を対象にビルドされています。エリトラ飛行・パーティクル・スコアボードを多用するため、Paper の採用を推奨します。

## セットアップ手順（コース作成）

`elytrarace.admin` 権限（既定OP）を持った状態で、レース専用ワールドに入り、**設定したい地点に立って／看板を見て** コマンドを実行します。各コマンドは実行のたびに **即 `config.yml` へ自動保存** されます（`save` 不要）。

コースは **コース名（例: `sky1`）** で識別します。同じコース名に対して開始地点・チェックポイント・ゴールを設定していくとコースが組み上がります。

標準フロー（setstartspawn → setlobby → setspawn → setcheckpoint add → setgoal → 看板設置）:

```text title="① 初期スポーン（途中抜け・切断時の戻り先）を現在地に設定"
/elytrarace setstartspawn
```

```text title="② 待機ロビーを現在地に設定"
/elytrarace setlobby
```

```text title="③ コースの開始地点（スタート・向き含む）を現在地に設定"
/elytrarace setspawn <コース名>
```

```text title="④ チェックポイント（リング）を視線の先の位置に追加（順番に繰り返す）"
/elytrarace setcheckpoint <コース名> add
```

```text title="⑤ ゴールリングを視線の先の位置に設定"
/elytrarace setgoal <コース名>
```

```text title="設定状況・コースの不足項目を確認する"
/elytrarace status
```

!!! note "リング（チェックポイント／ゴール）の置き方"
    チェックポイントとゴールは **プレイヤーの視線（eye location）の位置** を中心とした **球形の判定** で通過を検出します（半径は `checkpoint-radius`、既定5.0ブロック）。`setcheckpoint ... add` と `setgoal` は **見ている方向の空中の位置** ではなく、実行者の目の位置が中心になります。飛行ルート上の通過させたい空間に立って（または浮いて）実行してください。チェックポイントは追加した順番が通過順になります。

!!! note "レース開始に必要な条件"
    対戦レース・練習を開始できる条件は、そのコースに **開始地点（setspawn）とゴール（setgoal）が両方設定済み** であることです（チェックポイントは0個でも「開始→ゴール」だけのコースとして成立します）。加えて対戦レースには **ロビー地点（setlobby）** が必要です。`/elytrarace status` で各コースの `開始○／ゴール○／CP個数` と地点設定を確認できます。

### チェックポイントの削除・数値設定

```text title="指定番号のチェックポイントを削除（1始まり・以降の番号は繰り上げ）"
/elytrarace setcheckpoint <コース名> delete <番号>
```

```text title="ロケット花火の維持本数（最小〜最大）を設定"
/elytrarace setrocket <最小> <最大>
```

```text title="対戦レースの制限時間（秒）を設定（0=無制限）"
/elytrarace settime <秒>
```

!!! tip "奈落（落下）判定 void-y の設定について"
    コースには「このY座標を下回ると落下とみなしチェックポイントへ送還する」**`void-y`** を設定できます。**専用コマンドは無く**、既定値は無効（`-10000`）です。使用する場合は `config.yml` の `courses.<コース名>.void-y` に手動で数値を記入し、`/elytrarace reload` で反映してください。未設定でも致死ダメージ（クラッシュ）ではチェックポイントへ戻されます。

## 看板の設置（参加・離脱・開始・練習・ランキング）

看板は **プレイヤーがコマンドを覚えずに操作できる補助手段** です。設置・解除は **`/elytrarace setsign` コマンド専用**（`elytrarace.admin` 権限が必要）。設置した看板を **見ながら（5ブロック以内）** 実行すると、プラグインが座標を登録し整形済みテキストを書き込みます。

```text title="参加看板を登録（クリックで参加＋ロビーへ）"
/elytrarace setsign join
```

```text title="離脱看板を登録（クリックで離脱＋初期地点へ）"
/elytrarace setsign leave
```

```text title="開始看板を登録（クリックで対戦レース開始・コース名必須）"
/elytrarace setsign start <コース名>
```

```text title="コース指定のソロ看板を登録（旧・練習看板。setsign solo <コース名> と同じ）"
/elytrarace setsign practice <コース名>
```

```text title="ランキング看板を登録（上位記録を自動表示・コース名必須）"
/elytrarace setsign ranking <コース名>
```

```text title="見ている看板の登録を解除（テキストも消去）"
/elytrarace setsign delete
```

!!! note "看板の仕組み（座標で判定・テキストは表示専用）"
    登録した看板は **ブロック座標** を `config.yml` の `signs` リストに保存し、クリック判定は **保存座標との一致** で行います（看板のテキストには依存しません）。テキストはプラグインが自動描画し、参加看板・開始看板の4行目には **参加人数や「試合中」などの状態が自動表示**（参加・離脱・開始・ゴールなどのタイミングで更新）されます。ランキング看板には1位のプレイヤー名とタイムが自動表示され、クリックすると上位ランキングがチャットに出ます。**登録済みの看板は破壊できません**（`/elytrarace setsign delete` で解除してから壊してください）。再起動後も看板表示は自動で復元されます。

## config.yml 主要項目

すべてのキーはコード側に既定値があり、`config.yml` に無くても既定値で動作します（起動時に不足キーは最新版から自動追記されます）。

| キー | 既定値 | 説明 |
|---|---|---|
| `checkpoint-radius` | 5.0 | リング（チェックポイント／ゴール）の球形判定半径（ブロック） |
| `rocket.min` | 10 | 自動補充で維持する最小本数（`/elytrarace setrocket` で変更可） |
| `rocket.max` | 64 | 自動補充で維持する最大本数（配布時の本数・`setrocket` で変更可） |
| `rocket.regen-amount` | 1 | 1回の補充で増える本数 |
| `rocket.regen-interval` | 1 | 補充の間隔（秒） |
| `rocket.power` | 2 | ロケット花火の飛行力（FireworkMeta power） |
| `time-limit` | 300 | 対戦レースの制限時間（秒／0=無制限）。ソロは常に無制限（`/elytrarace settime` で変更可） |
| `min-players` | 1 | 対戦レース開始に必要な最低人数 |
| `actionbar-interval` | 5 | アクションバー（距離・進捗・タイム表示）の更新間隔（tick） |
| `ranking-display-count` | 10 | `/elytrarace ranking` で表示する件数 |
| `result-seconds` | 10 | レース終了時の結果タイトルの表示秒数 |
| `default-spawn` | （コメントアウト） | 初期スポーン（途中抜け・切断時の戻り先）。`setstartspawn` で自動生成 |
| `lobby-spawn` | （コメントアウト） | ロビー地点。`setlobby` で自動生成 |
| `signs` | `[]` | 登録看板のリスト（`setsign` で自動追記。world/x/y/z/kind/course） |
| `courses` | `{}` | コース定義（`setspawn`/`setcheckpoint`/`setgoal` で自動生成。spawn/goal/void-y/checkpoints） |
| `messages.prefix` | `&6[ElytraRace] &r` | チャット接頭辞（`&` カラーコード対応） |

!!! note "コース定義の中身（courses.<コース名>）"
    各コースは `spawn`（開始地点・向き含む）、`goal`（ゴールリング中心）、`checkpoints`（順序付きリング中心のリスト）、`void-y`（下回ると送還するY・既定 `-10000`＝無効）を持ちます。これらは `setspawn`／`setgoal`／`setcheckpoint` コマンドで自動生成・自動保存されるため、**手書き編集は基本的に不要** です（`void-y` のみコマンドが無いため手動記入）。

## ソロモード・ゴースト・区間タイム

練習モードは **ソロ**（共通ルール14章のGUIロビー方式）にまとめました。ソロロビーは対戦と同じ **ロビー地点（`/elytrarace setlobby`）** を使います。

```text title="ソロ看板（メニュー）を登録"
/elytrarace setsign solo
```

```text title="コース指定のソロ看板を登録（クリックでそのコースをソロで即開始＝旧練習看板と同じ）"
/elytrarace setsign solo <コース名>
```

```text title="走っている全員を止める（対戦レース＋ソロ。ソロはロビーへ戻してメニュー）"
/elytrarace stop
```

```text title="コースのゴースト・区間・前回の記録を消す（全員分。ランキングは消えない）"
/elytrarace ghost reset <コース名>
```

```text title="特定の人のゴースト・区間・前回の記録だけ消す"
/elytrarace ghost reset <コース名> <プレイヤー>
```

- 既存の **練習看板**（`kind: practice`）はそのまま使えます（表示が「>> ソロ >>」に変わり、クリックでそのコースをソロで即開始）。`/elytrarace practice` / `setsign practice` も別名として残っています。
- 同じコースを何人でも同時に走れます（アリーナの取り合いはしません）。対戦中にソロを止めたい場合は `solo.block-during-race: true`。
- ゴーストは `plugins/ElytraRace/ghosts/<コース>/<uuid>.best|last.ghost`、区間・好み・前回の結果は `plugins/ElytraRace/solo-data.yml` に保存されます。ランキングは今まで通り `records.db`。
- コースの開始地点・リング・ゴール・リング半径を変えると、古いゴースト・区間は自動で比較に使われなくなります（ランキングは残ります）。
- ソロロビーにいる人は無敵・満腹度が減りません。ホットバー中央にソロメニューのネザースターを置きます（捨てる・移動は不可）。

| config キー | 既定 | 内容 |
|---|---|---|
| `solo.enabled` | true | ソロ（と練習看板）を使えるか |
| `solo.menu-delay` | 3 | ロビーへ戻ってからメニューを開くまでの秒数 |
| `solo.broadcast-top` | true | ソロで1位を更新したら全体告知 |
| `solo.block-during-race` | false | 対戦レース中はソロを始められないようにする |
| `splits.enabled` | true | 区間表示・区間表・BossBar |
| `splits.show-in-race` | true | 対戦レース中も本人に自己ベスト比を出す |
| `splits.bossbar` | true | 走行中の BossBar |
| `ghost.enabled` | true | ゴーストの記録・再生 |
| `ghost.default` | best | 初めての人のゴーストの種類（best/top/last/off） |
| `ghost.sample-ticks` | 2 | 位置を記録する間隔（tick） |
| `ghost.max-seconds` | 900 | これより長い走りはゴーストを保存しない |
| `ghost.entity` | ARMOR_STAND | Java版の走者に見せる姿（ARMOR_STAND / MANNEQUIN）。統合版の走者には常に ARMOR_STAND |
| `ghost.trail` | true | ゴーストの位置にパーティクル |

これらの項目は、プラグイン更新後の起動時に既存の config.yml へ自動で追加されます（既存の値は変わりません）。

!!! warning "統合版での見え方（実機確認）"
    - ゴーストは **見ている本人だけに表示**（`setVisibleByDefault(false)` + `showEntity`）。他の人（統合版含む）から見えないことを実機で確認してください。
    - `ghost.entity: MANNEQUIN` は Java 版の走者だけに使われます（統合版で表示できない可能性があるため）。マネキンには当たり判定があるので、気になる場合は ARMOR_STAND のままにしてください。
    - ゴーストの発光（Glowing）は使っていません（統合版に出ないため）。名前表示とパーティクルで目印にしています。


## 設定ツール（/elytrarace wand）

OP用の設定ツール（**ブレイズロッド**）で、コマンドを打たずにコースを作れます。受け取る時と使う時の両方で `elytrarace.admin` を確認するため、非OPが手に入れても使えません。エリトラレースに参加中（ロビー・ソロ・走行中）は受け取れません。

```text title="設定ツールを受け取る（持っていれば説明を更新）"
/elytrarace wand
```

```text title="設定メニューを直接開く"
/elytrarace menu
```

| 操作 | 内容 |
|---|---|
| 左クリック/長押し（ブロック） | 選択中のコースに **リングを追加**（目の位置） |
| 左クリック（空中）※Java版のみ | 同上（クリエイティブで飛びながら置ける） |
| 右クリック/タップ（看板） | 選んでいる **看板モード** で登録／解除 |
| 右クリック/タップ（空中・ブロック） | 選択中コースの設定状況を表示 |
| しゃがみ＋左クリック/長押し | 選択中コースの開始地点（緑）・リング（白）・ゴール（炎）を自分にだけ表示 |
| しゃがみ＋右クリック/タップ | **設定メニュー** を開く |

**設定メニュー 1ページ目（コース・看板）**：コースを選ぶ／新しいコースを作る（現在地を開始地点に `course1` `course2`…）、初期スポーン・ロビー、開始地点・リング追加・最後のリングを消す・ゴール・奈落ライン、看板モード（参加／離脱／開始／ソロ（メニュー）／ソロ（コース）／ランキング／解除）、このコースのゴーストを全部消す、対戦開始・強制停止・config 再読み込み・設定ツールの回収。

**設定メニュー 2ページ目（数値・ON/OFF）**：リング判定半径・対戦の制限時間・最低人数・ロケット（最低／最大／飛行力／補充本数／補充間隔）・結果発表の秒数・ソロメニューを開くまでの秒数・ゴーストの記録間隔と最長、ソロ／対戦中のソロ禁止／1位告知／区間タイム／対戦中の区間表示／BossBar／ゴースト／ゴーストのパーティクル／看板の蝋引き／空中タップ／参加通知の ON/OFF、ゴーストの初期値と姿。変更は **即 config.yml に保存** されます。

- 好きな名前のコースを作るときは `/elytrarace setspawn <名前>` を使ってください（GUI では名前を入力できません）。途中のリングを消すときは `/elytrarace setcheckpoint <コース> delete <番号>`。
- 名前表示・色・ロビーのゲームモード・メッセージなど **文字の設定** は config.yml を編集して `/elytrarace reload`（メニューの「config を再読み込み」でも可）。
- リング判定半径を変えると、今までのゴースト・区間は比較に使われなくなります（ランキングは残ります）。

!!! info "統合版の場合"
    - 空中の右クリックが届かないため、**空中タップ＝設定状況の表示**、**しゃがんで空中タップ＝設定メニュー** です。
    - 空中タップは左クリック扱いで届くため、統合版では空中の左クリックでリングを置きません。リングは **ブロックを長押し** するか、メニューの「リングを目の位置に追加」で置いてください。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `elytrarace.admin` | OP | セットアップ・看板設置・コース作成・数値設定・`/elytrarace stop` / `reload`。設定系コマンドのみコード側でこの権限を確認する |

!!! warning "`/elytrarace` のコマンドレベル権限は無し（設定系のみ admin）"
    `plugin.yml` の `elytrarace` コマンドには **コマンドレベルの `permission` が設定されていません**。そのため **`/elytrarace join` / `leave` / `start` / `practice` / `ranking` / `stats` / `status` は全員が実行できます**。コース設定（setstartspawn / setlobby / setspawn / setcheckpoint / setgoal / setrocket / settime / setsign）と `/elytrarace stop` / `reload` は **コード内で `elytrarace.admin` を確認** します。プレイヤーは看板クリック・コマンドのどちらでも参加・離脱・開始・練習ができます。

## コマンド一覧

| コマンド | 権限 | 説明 |
|---|---|---|
| `/elytrarace join` | 全員 | レースに参加してロビーへ（看板クリックと同等） |
| `/elytrarace leave` | 全員 | レースから離脱し初期地点へ（看板クリックと同等） |
| `/elytrarace start <コース>` | 全員 | 対戦レースを開始（参加者がいれば誰でも可・コンソール／コマブロも可） |
| `/elytrarace solo [コース] [best\|top\|last\|off]` | 全員 | ソロメニュー／コース指定ですぐ走る |
| `/elytrarace practice <コース>` | 全員 | `solo <コース>` の別名（旧・練習モード） |
| `/elytrarace retry` | 全員 | ソロ中にスタートからやり直す |
| `/elytrarace ghost <best\|top\|last\|off>` | 全員 | ゴーストの種類 |
| `/elytrarace ghost reset <コース> [プレイヤー]` | `elytrarace.admin` | ゴースト・区間・前回の記録を消す |
| `/elytrarace ranking <コース>` | 全員 | コースのタイムランキングを表示 |
| `/elytrarace stats` | 全員 | 自分のコース別統計（挑戦・クリア・ベスト）を表示 |
| `/elytrarace status` | 全員 | 設定状況・進行状態を表示（読み取り専用） |
| `/elytrarace back [番号]` | 全員 | レース中にチェックポイントへ戻る（番号省略で選択メニュー・統合版の戻るアイテム代替） |
| `/elytrarace giveup` | 全員 | レース／ソロ中にリタイアしてロビーへ（統合版のギブアップアイテム代替） |
| `/elytrarace setstartspawn` | `elytrarace.admin` | 初期スポーン（戻り先）を現在地に設定 |
| `/elytrarace setlobby` | `elytrarace.admin` | ロビー地点を現在地に設定 |
| `/elytrarace setspawn <コース>` | `elytrarace.admin` | コースの開始地点を現在地に設定 |
| `/elytrarace setcheckpoint <コース> add\|delete <番号>` | `elytrarace.admin` | チェックポイント（リング）を追加／削除 |
| `/elytrarace setgoal <コース>` | `elytrarace.admin` | ゴールリングを視線位置に設定 |
| `/elytrarace setrocket <最小> <最大>` | `elytrarace.admin` | ロケット花火の維持本数を設定 |
| `/elytrarace settime <秒>` | `elytrarace.admin` | 対戦レースの制限時間を設定（0=無制限） |
| `/elytrarace wand` | `elytrarace.admin` | 設定ツール（ブレイズロッド）を受け取る |
| `/elytrarace menu` | `elytrarace.admin` | 設定メニュー（GUI）を開く |
| `/elytrarace setsign <join\|leave\|start\|solo\|practice\|ranking\|delete>` | `elytrarace.admin` | 看板を各用途で登録／解除 |
| `/elytrarace stop` | `elytrarace.admin` | 進行中の対戦レース・ソロの走りを強制終了 |
| `/elytrarace lobbyfix <プレイヤー> [force]` | `elytrarace.admin` | ロビー入場前の持ち物が戻らないときの個別復旧（force で退避データ破棄＋ロック解除） |
| `/elytrarace reload` | `elytrarace.admin` | config を再読み込み（コース・看板も再描画） |

!!! note "自動保存・reload について"
    地点・コース・看板・数値設定は **各コマンドの実行時に即 `config.yml` へ保存** されます（`save` は不要）。`config.yml` を手動編集した場合や `void-y` を追記した場合は `/elytrarace reload` で反映してください（コースと看板が再読み込み・再描画されます）。

## トラブルシューティング

??? failure "レースが開始できない / コースが見つからない"
    `/elytrarace status` でコース一覧と設定状況を確認してください。対象コースに **開始地点（setspawn）とゴール（setgoal）** が両方設定されている必要があります。対戦レースには **ロビー地点（setlobby）** も必須です。参加者が最低人数（`min-players`・既定1人）に達しているかも確認してください。

??? failure "チェックポイント／ゴールを通過判定してくれない"
    リングは **球形の判定**（既定半径5.0ブロック）です。飛行ルートが判定球から外れていると通過になりません。`checkpoint-radius` を大きくするか、リング位置を飛行ルート上に置き直してください（`setcheckpoint ... add` は実行者の **目の位置** が中心になります）。チェックポイントは **正しい順番** に通過する必要があります。

??? failure "看板をクリックしても反応しない / 参加できない"
    看板は **`/elytrarace setsign` で登録済み** か確認してください（手書きの看板は機能しません）。クリック判定は **保存座標との一致** で行うため、看板を壊して置き直した場合は再登録が必要です。開始看板はそのコースに参加者（ロビー在籍者）がいないと開始されません。`/elytrarace join` / `start` はコマンドでも全員実行できます。

??? failure "ロケット花火が補充されない / 多すぎる・少なすぎる"
    補充は `rocket.min`〜`rocket.max` の範囲を維持するよう動作します。`/elytrarace setrocket <最小> <最大>` で範囲を変更できます（`0 <= 最小 <= 最大`）。補充量・間隔は `config.yml` の `rocket.regen-amount` / `regen-interval` で調整し、`/elytrarace reload` で反映してください。

??? failure "制限時間を変えたい / 無制限にしたい"
    `/elytrarace settime <秒>` で対戦レースの制限時間を設定します（`0` で無制限）。練習モードは常に無制限です。

??? failure "落下してもチェックポイントに戻らない（奈落へ落ちる）"
    致死ダメージ（クラッシュ）ではチェックポイントへ送還されますが、単なる落下で奈落へ吸い込まれるコースでは **`void-y` の設定** が有効です。`config.yml` の `courses.<コース名>.void-y` に「このYを下回ると送還する」数値を記入し、`/elytrarace reload` で反映してください（専用コマンドはありません。既定 `-10000`＝無効）。

??? failure "config を編集したのに反映されない"
    `config.yml` を手動編集した後は `/elytrarace reload` を実行してください。新しいキーは起動時に最新版から自動追記されるため、バージョンアップ時の手書き追記は基本的に不要です（キーが無くても既定値で動作します）。

??? failure "記録・ランキングが保存されない"
    記録は `plugins/ElytraRace/records.db`（SQLite）に保存されます。起動ログに「Database connected successfully!」が出ているか確認してください。ファイルの書き込み権限が無い、またはフォルダが作成できない環境では記録が保存されません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ElytraRace 概要へ](index.md){ .md-button }
