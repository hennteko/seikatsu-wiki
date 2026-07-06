<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ElytraRace ― OP・運営ガイド { .page-op #elytrarace-op }

ElytraRace の導入・コース作成・看板設置・config.yml・権限・管理コマンド・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | ElytraRace |
| バージョン | 1.0.0 |
| 対応サーバー | Paper 1.26.x（api-version 26.1.2） |
| メインコマンド | `/elytrarace`（エイリアス `/er`）/ join・leave・start・practice・ranking・stats・status は全員可、設定系は `elytrarace.admin` |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/ElytraRace/config.yml`（設定・地点・コース・看板をすべて保存） |
| 記録ファイル | `plugins/ElytraRace/records.db`（SQLite。ベストタイム・統計。自動生成） |

!!! success "実装状況：新規追加ゲーム"
    エリトラ飛行によるチェックポイント・レース、**対戦レース／練習モードの2モード**、ロケット花火の自動補充、疑似死亡（クラッシュ時のチェックポイント送還）、パーティクルによるリング可視化、順位サイドバー、SQLiteによるベストタイム記録・ランキング、看板5種（参加／離脱／開始／練習／ランキング）に対応しています。

## 導入手順

1. ビルドした `ElytraRace-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/ElytraRace/config.yml` が自動生成され、記録用の `records.db` も自動作成される。
3. エリトラで飛べるレース専用ワールド（開けた空間・落下地形など）を用意する。
4. 後述の手順でロビー・初期スポーン・コース（開始地点／チェックポイント／ゴール）を作成する。
5. 参加・開始などの看板を設置する。
6. 設定を変更したら `/elytrarace reload` で再読み込みする。

!!! warning "既存サーバーを更新する場合の注意（config の自動追記なし）"
    本プラグインは起動時に **`saveDefaultConfig()` のみ** を実行します。**既存の `config.yml` があっても新しいキーは自動追記されません**。ただしコード側がすべてのキーに既定値を持つため、キーが不足していても既定値で動作します。新しい設定項目を反映したい場合は、既存 `config.yml` に手動で追記するか、退避して再生成してください。**地点・コース・看板は各設定コマンドの実行時に即 `config.yml` へ自動保存** されます（手書き編集は不要）。

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

```text title="練習看板を登録（クリックした本人がソロ練習開始・コース名必須）"
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

すべてのキーはコード側に既定値があり、`config.yml` に無くても既定値で動作します（`saveDefaultConfig()` のみのため既存ファイルへの自動追記はありません）。

| キー | 既定値 | 説明 |
|---|---|---|
| `checkpoint-radius` | 5.0 | リング（チェックポイント／ゴール）の球形判定半径（ブロック） |
| `rocket.min` | 10 | 自動補充で維持する最小本数（`/elytrarace setrocket` で変更可） |
| `rocket.max` | 64 | 自動補充で維持する最大本数（配布時の本数・`setrocket` で変更可） |
| `rocket.regen-amount` | 1 | 1回の補充で増える本数 |
| `rocket.regen-interval` | 1 | 補充の間隔（秒） |
| `rocket.power` | 2 | ロケット花火の飛行力（FireworkMeta power） |
| `time-limit` | 300 | 対戦レースの制限時間（秒／0=無制限）。練習モードは常に無制限（`/elytrarace settime` で変更可） |
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
| `/elytrarace practice <コース>` | 全員 | 指定コースを1人で練習（時間無制限） |
| `/elytrarace ranking <コース>` | 全員 | コースのタイムランキングを表示 |
| `/elytrarace stats` | 全員 | 自分のコース別統計（挑戦・クリア・ベスト）を表示 |
| `/elytrarace status` | 全員 | 設定状況・進行状態を表示（読み取り専用） |
| `/elytrarace setstartspawn` | `elytrarace.admin` | 初期スポーン（戻り先）を現在地に設定 |
| `/elytrarace setlobby` | `elytrarace.admin` | ロビー地点を現在地に設定 |
| `/elytrarace setspawn <コース>` | `elytrarace.admin` | コースの開始地点を現在地に設定 |
| `/elytrarace setcheckpoint <コース> add\|delete <番号>` | `elytrarace.admin` | チェックポイント（リング）を追加／削除 |
| `/elytrarace setgoal <コース>` | `elytrarace.admin` | ゴールリングを視線位置に設定 |
| `/elytrarace setrocket <最小> <最大>` | `elytrarace.admin` | ロケット花火の維持本数を設定 |
| `/elytrarace settime <秒>` | `elytrarace.admin` | 対戦レースの制限時間を設定（0=無制限） |
| `/elytrarace setsign <join\|leave\|start\|practice\|ranking\|delete>` | `elytrarace.admin` | 看板を各用途で登録／解除 |
| `/elytrarace stop` | `elytrarace.admin` | 進行中の対戦レースを強制終了 |
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
    `config.yml` を手動編集した後は `/elytrarace reload` を実行してください。なお本プラグインは **既存 config への新キー自動追記を行いません**。新しいキーを追記して使う場合は正しいパス・スペルで記入してください（既定値はコード側にあるため、キーが無くても既定で動作します）。

??? failure "記録・ランキングが保存されない"
    記録は `plugins/ElytraRace/records.db`（SQLite）に保存されます。起動ログに「Database connected successfully!」が出ているか確認してください。ファイルの書き込み権限が無い、またはフォルダが作成できない環境では記録が保存されません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ElytraRace 概要へ](index.md){ .md-button }
