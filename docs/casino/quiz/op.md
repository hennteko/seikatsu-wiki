<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# クイズ ― OP・運営ガイド { .page-op #quiz-op }

クイズ（quiz モジュール）の有効化・設定・問題の追加・看板の設置・権限・管理コマンドをまとめます。クイズは CasinoPlugin の1モジュールなので、専用 jar の導入は不要です。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `quiz` |
| 所属プラグイン | CasinoPlugin（`jp.casinoplugin.CasinoPlugin`） |
| メインコマンド | `/quiz`（エイリアス `/q`）、`/qd`、`/qt`、`/quizsign`（**次の更新で廃止予定**。`/quiz setsign ranking` 等に移行） |
| モジュール設定ファイル | `plugins/CasinoPlugin/modules/quiz.yml` |
| データファイル | `plugins/CasinoPlugin/quiz/`（`questions.yml` / `players.yml` / `signs.yml` / `questions/` フォルダ） |
| 依存モジュール | `bank`（報酬・参加費にエメラルド口座を使用。必須） |

!!! info "quiz モジュールの位置づけ"
    クイズは単独プラグインではなく、CasinoPlugin に統合された **quiz モジュール** です。報酬の付与・タワー参加費の徴収は、すべて `bank` モジュールのエメラルド口座（`EmeraldAPI`）経由で行われます。`bank` が無効だと quiz モジュールは起動に失敗します。CasinoPlugin 全体の構成は [CasinoPlugin の OP ページ](../casino-plugin/op.md) を参照してください。

## 有効化

クイズは CasinoPlugin に内蔵されているため、**専用 jar の導入は不要** です。

1. CasinoPlugin が `plugins/` に導入されていることを確認する。
2. `plugins/CasinoPlugin/config.yml` を開き、`modules.quiz.enabled` を `true` にする。
3. サーバーを起動（または再起動）する。起動時に `plugins/CasinoPlugin/modules/quiz.yml` と `plugins/CasinoPlugin/quiz/questions.yml` / `players.yml` が自動生成される。

```yaml
# plugins/CasinoPlugin/config.yml
modules:
  quiz:
    enabled: true
```

!!! warning "bank モジュールが前提です"
    クイズの報酬・参加費はエメラルド口座を使います。`modules.bank.enabled` が `false` の状態だと、quiz モジュールは起動時に「bank module required」エラーで停止します。`bank` は有効のまま運用してください。

## quiz.yml 設定項目

`plugins/CasinoPlugin/modules/quiz.yml` でクイズの動作を調整します。主なキーは以下の通りです。

### デイリークイズ（`daily_quiz`）

| キー | 既定値 | 説明 |
|---|---|---|
| `daily_quiz.enabled` | `true` | デイリークイズを有効にするか |
| `daily_quiz.questions_per_day` | `5` | 1日に出題する問題数 |
| `daily_quiz.reset_hour` | `0` | リセット時刻（メッセージ表示に使用。既定は0時） |
| `daily_quiz.allow_retry` | `false` | 同日中の再挑戦を許可するか（false で1日1回のみ） |
| `daily_quiz.rewards.per_correct` | `5` | 1問正解ごとの報酬エメラルド |
| `daily_quiz.rewards.perfect_bonus` | `25` | 全問正解時の追加ボーナスエメラルド |
| `daily_quiz.streak_bonuses` | `3:15` `7:50` `14:150` `30:500` | 連続参加日数ごとのボーナス（日数: エメラルド） |

### クイズタワー（`tower`）

| キー | 既定値 | 説明 |
|---|---|---|
| `tower.enabled` | `true` | クイズタワーを有効にするか |
| `tower.total_floors` | `100` | タワーの総階数 |
| `tower.entry_cost` | `100` | 参加費（エメラルド） |
| `tower.max_failures` | `10` | この回数失敗するとゲームオーバー |
| `tower.answer_time_seconds` | `30` | 1問あたりの回答制限時間（秒）。`0` 以下で無制限。画面を閉じても時間は進み、`/quiz tower` で同じ問題を開き直せる |
| `tower.checkpoint_rewards.<階>` | 30階`100` / 60階`300` / 90階`500` / 100階`1000`（他は`0`） | 10階ごとのチェックポイント報酬（`emeralds` / `command` / `message`。各階に `message` あり） |
| `tower.retreat_reward_enabled` | `true` | チェックポイントでの撤退報酬を有効にするか |
| `tower.game_over_reward_percent` | `50` | ゲームオーバー時に撤退報酬の何%を残念賞として払うか（`0` で没収） |

撤退報酬は固定値ではなく **`entry_cost × 到達階 ÷ 10`** で計算されます。ゲームオーバー時は、既定でこの撤退報酬の `tower.game_over_reward_percent`%（既定50%）が残念賞として支払われます（`0` で没収）。チェックポイント報酬の `command` を指定すると、`{player}` をプレイヤー名に置換してコンソールから実行されます（カスタムアイテム配布などに利用可能）。

!!! warning "回答制限時間のキーに注意"
    タワーの回答制限時間は **`tower.answer_time_seconds`**（同梱 `quiz.yml` に記載・既定30秒）で制御されます。同じく `quiz.yml` にある `tower.time_limit_per_question` は **現在のコードでは参照されていません**（旧仕様の名残）。時間を変えたい場合は `tower.answer_time_seconds` を編集してください。

### 練習モード（`practice`）

| キー | 既定値 | 説明 |
|---|---|---|
| `practice.questions_per_round` | `10` | 練習モードで1回に出題する問題数 |
| `practice.rewards.per_correct` | `1` | 1問正解ごとの報酬エメラルド（`0` で報酬なし） |
| `practice.rewards.daily_cap` | `30` | 1日に受け取れる上限エメラルド（`0` で上限なし。日付の切替は `daily_quiz.reset_hour`） |

練習モードは参加費がなく、選択した難易度（easy / medium / hard / expert）から `questions_per_round` 問を出題します。1問正解ごとに `per_correct`（既定1E）を付与し、1日あたり `daily_cap`（既定30E）を上限とします。回答数・正解数はランキングにも合算されます。これらのキーは同梱 `quiz.yml` に記載されています。

### ランキング（`rankings`）

| キー | 既定値 | 説明 |
|---|---|---|
| `rankings.enabled` | `true` | ランキング機能を有効にするか |
| `rankings.reset_schedule` | `monthly` | リセット周期（`monthly` / `weekly` / `never`） |
| `rankings.reset_day` | `1` | リセットする日（月の何日） |
| `rankings.reset_hour` | `0` | リセットする時刻（何時） |
| `rankings.display_top` | `10` | ランキングに表示する人数 |

### その他

| キー | 既定値 | 説明 |
|---|---|---|
| `anti_cheat.*` | ― | 不正防止用の設定群（最低回答時間・問題の重複防止など） |
| `messages.*` | ― | チャットメッセージのテンプレート（`&` カラーコード対応） |
| `debug.enabled` | `false` | デバッグログの出力 |

!!! note "設定変更後はリロードを"
    `quiz.yml` や `questions.yml` を編集したら、`/quiz reload` で再読み込みできます（ランキング看板も更新）。サーバー全体を再起動する必要はありません。旧 `/quiz admin reload` も当面は動作しますが「次の更新で廃止します」と警告が出ます。

## 問題の追加方法

クイズの問題は `plugins/CasinoPlugin/quiz/questions.yml` で管理します。初回起動時に既定の問題セットが自動生成されます。

問題は `questions:` セクションの下に、一意の ID（`q001` など）をキーとして1問ずつ記述します。

```yaml
questions:
  q001:
    text: "マインクラフトで最も硬いブロックは？"
    choices:
      - "黒曜石"
      - "ダイヤモンドブロック"
      - "岩盤"
      - "エンダーチェスト"
    correct: 2          # 0=A, 1=B, 2=C, 3=D（正解の選択肢のインデックス）
    difficulty: "easy"  # easy / medium / hard / expert
    category: "minecraft"
```

| キー | 説明 |
|---|---|
| `text` | 問題文 |
| `choices` | 選択肢。**ちょうど4つ** 必要（A〜D に対応） |
| `correct` | 正解の番号。`0`〜`3`（A〜D） |
| `difficulty` | 難易度。`easy` / `medium` / `hard` / `expert` の4段階 |
| `category` | カテゴリ（現状は内部分類用） |

!!! tip "questions/ フォルダで追加するのが簡単（リスト形式対応）"
    `questions.yml` のマップ形式（ID 指定）のほか、`plugins/CasinoPlugin/quiz/questions/` フォルダ内の `.yml` ファイルも自動で読み込まれます。初回起動時にサンプル `quiz/questions/sample.yml` が展開されます。このフォルダ内のファイルは **ID 不要のリスト形式** でも記述でき（自動採番）、まとめて問題を追加する用途に向いています。マップ形式・リスト形式の両方に対応しています。

    ```yaml
    # quiz/questions/ 内のファイル（リスト形式の例）
    questions:
      - text: "問題文"
        choices: ["A", "B", "C", "D"]
        correct: 0
        difficulty: "expert"
        category: "general"
    ```

!!! warning "問題フォーマットの注意"
    選択肢が4つでない、または `correct` が 0〜3 の範囲外の問題は、読み込み時に無視されます（起動ログに警告が出ます）。問題が1問も読み込めないとクイズモジュールは停止します。

!!! tip "難易度と階の対応"
    クイズタワーは階に応じて難易度を選びます（1〜30階＝easy、31〜60階＝easy+medium、61〜90階＝medium+hard、91〜100階＝hard）。デイリークイズは easy 問題から出題されます。`expert` 難易度はタワーでは使われず、**練習モードの「最難関」専用** です。各難易度の問題をバランスよく用意してください。

## 参加看板・タワーロビーの設置

プレイヤーがコマンドを使わずにクイズを始められるよう、**参加看板** を設置できます。看板は OP（または `quiz.admin`）のみ設置でき、1行目を `[Quiz]` にして手書きすると自動整形されます。`/quiz setsign` を使うと、見ている看板（6ブロック以内）に自動で内容を書き込めます。

```text title="見ている看板をタワーロビー移動看板にする（join / tower どちらでも同じ動作）"
/quiz setsign join
```

```text title="見ている看板を退出看板にする（退出地点へテレポート）"
/quiz setsign leave
```

```text title="見ている看板をデイリークイズ看板にする"
/quiz setsign daily
```

```text title="見ている看板を練習モード看板にする（難易度選択GUIを開く）"
/quiz setsign practice
```

```text title="見ている看板を個人成績看板にする（成績GUIを開く）"
/quiz setsign stats
```

```text title="見ている看板をタワーロビー移動看板にする"
/quiz setsign tower
```

```text title="見ている看板をレンジ看板にする（レンジ: 1-30 / 31-60 / 61-90 / 91-100）"
/quiz setsign range <レンジ>
```

```text title="見ている看板をランキング看板にする（タイプ: answers / correct / tower）"
/quiz setsign ranking <answers|correct|tower> <順位>
```

```text title="見ている看板の設定を解除する"
/quiz setsign delete
```

| 看板タイプ | 右クリック時の動作 |
|---|---|
| `join` / `tower` | タワーロビーへテレポート（両者は同じ動作） |
| `leave` | 退出地点（`setstartspawn` 未設定ならワールドスポーン）へテレポート |
| `daily` | デイリークイズを開始 |
| `practice` | 練習モードの難易度選択 GUI を開く |
| `stats` | 個人成績 GUI（回答数・正答数・正答率・連続参加・タワー記録・累計獲得エメラルド）を開く |
| `ranking` | ランキング表示看板（`answers` / `correct` / `tower` の指定順位。5分ごとに自動更新） |
| `1-30` / `31-60` / `61-90` / `91-100` | 該当レンジの物理エリアへテレポートしてタワーを開始/再開（自分の現在階がそのレンジに入っているときのみ） |

### タワーのロビー・レンジエリアの設定

クイズタワーは 30階ごとのレンジ（1-30 / 31-60 / 61-90 / 91-100）に分かれ、レンジ境界（31・61・91階）でいったん区切られます。レンジごとに物理スポーン地点を設定すると、看板を通じてプレイヤーを各エリアへ誘導できます。

```text title="現在地をタワーロビー地点に設定する"
/quiz setlobby
```

```text title="現在地を指定レンジのスポーン地点に設定する（レンジ: 1-30 / 31-60 / 61-90 / 91-100）"
/quiz setrange <レンジ>
```

```text title="現在地を退出地点に設定する（/quiz leave・leave 看板のテレポート先）"
/quiz setstartspawn
```

!!! note "看板・ロビーは任意設定"
    ロビーやレンジ看板を設置しなくても、`/qt`・`/qd` や参加看板からクイズを開始できます。物理エリアを使った演出を行いたい場合のみ、`setlobby` と各レンジの `setrange`、`[Quiz] tower`・レンジ看板を組み合わせて設置してください。レンジスポーンが未設定の場合はテレポートを行わず、GUI のみ開きます。

!!! tip "旧コマンド名は廃止予定"
    地点設定の旧名 `/quiz settowerlobby`・`/quiz setexit` は当面は互換エイリアスとして動作しますが、正式名は `/quiz setlobby`・`/quiz setstartspawn` です。

## ランキング看板の設置

設置済みの看板をランキング表示看板として登録できます。看板は **5分ごとに自動更新** されます。現在は `/quiz setsign ranking` で登録します。

```text title="見ている看板をランキング看板として登録（タイプ: answers / correct / tower）"
/quiz setsign ranking <answers|correct|tower> <順位>
```

```text title="見ている看板の登録を解除"
/quiz setsign delete
```

```text title="すべてのランキング看板を手動更新（/quiz reload に統合）"
/quiz reload
```

!!! warning "`/quizsign` は次の更新で廃止予定"
    旧 `/quizsign create|remove|update|list` は当面動作しますが、実行すると「この書き方は次の更新で廃止します」と警告が出ます。以下へ移行してください。

    | 旧コマンド | 新しい方法 |
    |---|---|
    | `/quizsign create <タイプ> <順位>` | `/quiz setsign ranking <answers\|correct\|tower> <順位>` |
    | `/quizsign remove` | `/quiz setsign delete` |
    | `/quizsign update` | `/quiz reload`（ランキング看板も更新） |
    | `/quizsign list` | `/quiz status` |

看板の **タイプ** は3種類です。

| タイプ | 表示内容 |
|---|---|
| `answers` | クイズ回答数ランキング |
| `correct` | クイズ正答数ランキング |
| `tower` | クイズタワー記録ランキング（100階制覇者はクリアタイム、未制覇者は到達階） |

`<順位>` には 1・2・3… のように表示したい順位を指定します（1看板につき1順位）。登録情報は `plugins/CasinoPlugin/quiz/signs.yml` に保存されます。

## 管理コマンド

```text title="quiz.yml と questions.yml を再読み込み（ランキング看板も更新）"
/quiz reload
```

```text title="クイズの設定状況・登録看板数などを表示（全員可）"
/quiz status
```

```text title="進行中のクイズを強制停止（マルチ/ソロを指定可）"
/quiz stop [multi|solo [プレイヤー]]
```

| コマンド | 必要権限 | 説明 |
|---|---|---|
| `/quiz solo` | 全員可 | ソロメニュー（デイリー・タワー・練習・成績） |
| `/quiz status` | 全員可 | 設定状況・登録看板数・総問題数などを表示 |
| `/quiz reload` | OP または `quiz.admin` | `quiz.yml` と `questions.yml` を再読み込み（ランキング看板も更新） |
| `/quiz setsign <join\|leave\|daily\|practice\|stats\|tower\|range <レンジ>\|ranking <種類> <順位>\|delete>` | OP または `quiz.admin` | 参加・ランキング看板の設置・解除 |
| `/quiz setlobby` | OP または `quiz.admin` | 現在地をタワーロビー地点に設定 |
| `/quiz setrange <レンジ>` | OP または `quiz.admin` | 現在地を指定レンジのスポーン地点に設定 |
| `/quiz setstartspawn` | OP または `quiz.admin` | 現在地を退出地点（`/quiz leave`・leave 看板の移動先）に設定 |
| `/quiz stop [multi\|solo [プレイヤー]]` | OP または `quiz.admin` | 進行中のクイズを強制停止 |
| `/quiz wand` / `/quiz menu` | OP または `quiz.admin` | 管理ツールの取得・管理メニュー |
| `/quiz lobbyfix <プレイヤー> [force]` | OP または `quiz.admin` | ロビー退避した持ち物の手動復元 |
| `/quizsign <create\|remove\|update\|list>` | OP または `quiz.admin` | （**廃止予定**）旧ランキング看板管理。上記 `/quiz setsign ranking` 等へ移行 |

!!! note "旧 `/quiz admin reload` / `/quiz admin stats`"
    旧 `/quiz admin reload` は `/quiz reload` に、`/quiz admin stats` は `/quiz status` に統合されました。旧コマンドも当面は動作しますが「次の更新で廃止します」と警告が出ます。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `quiz.use` | true | クイズ機能（デイリー／タワー／ランキング）の利用。子に `quiz.daily` / `quiz.tower` / `quiz.ranking` を含む |
| `quiz.daily` | true | デイリークイズを利用できる |
| `quiz.tower` | true | クイズタワーを利用できる |
| `quiz.ranking` | true | クイズランキングを表示できる |
| `quiz.admin` | op | クイズ管理コマンド全権限（看板・地点・reload・stop・lobbyfix など、既定で OP に付与）。子に `quiz.use` を含む |

!!! note "プレイヤー用の quiz.* は既定で全員に付与"
    `quiz.use`（および子の `quiz.daily` / `quiz.tower` / `quiz.ranking`）は **既定値が true** で、すべてのプレイヤーが最初からクイズを遊べます。特定のプレイヤーにだけ遊ばせたくない場合は、権限プラグイン（LuckPerms 等）で `quiz.use` を剥奪してください。管理コマンドは OP または `quiz.admin`（`default: op`）の単一ノードで判定します（旧 `quiz.admin.reload` / `quiz.admin.sign` は廃止され `quiz.admin` に統合されました）。運営スタッフ（非 OP）には `quiz.admin` を付与すれば、利用と管理がまとめて有効になります。

## トラブルシューティング

??? failure "クイズのコマンドが「不明なコマンド」になる"
    `config.yml` の `modules.quiz.enabled` が `false` になっている可能性があります。`true` にしてサーバーを再起動してください。起動ログで quiz モジュールが正常に起動しているかも確認します。

??? failure "起動時に「bank module required」で quiz が止まる"
    クイズはエメラルド口座（bank モジュール）に依存します。`modules.bank.enabled` が `true` であることを確認してください。

??? failure "プレイヤーがクイズを遊べない"
    `quiz.use` 系の権限は既定 true なので通常は全員遊べます。遊べない場合は権限プラグインで `quiz.use`（および子ノード）が剥奪・`false` 設定になっていないか確認してください。

??? failure "問題が出題されない / 起動時に quiz が停止する"
    `questions.yml` に有効な問題が1問も無いと quiz モジュールは停止します。選択肢が4つでない、`correct` が範囲外などの不正な問題は無視されます。起動ログの警告を確認し、`/quiz reload` で再読み込みしてください。

??? failure "ランキング看板が更新されない"
    看板は5分ごとに自動更新されます。すぐに反映したい場合は `/quiz reload` で手動更新してください。看板の登録状況は `/quiz status` で確認できます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← クイズ概要へ](index.md){ .md-button }
