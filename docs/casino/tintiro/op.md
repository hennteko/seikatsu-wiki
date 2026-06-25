<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# チンチロ ― OP・運営ガイド { .page-op #tintiro-op }

チンチロ（tintiro モジュール）の有効化・設定・看板セットアップ・コマンド・権限をまとめます。チンチロは単独プラグインではなく、統合プラグイン **CasinoPlugin の1モジュール** として動作します。

## 基本情報

| 項目 | 値 |
|---|---|
| モジュール ID | `tintiro` |
| 所属プラグイン | CasinoPlugin（`jp.casinoplugin.CasinoPlugin`） |
| メインコマンド | `/tintiro`（エイリアス `/ti`） |
| 設定ファイル | `plugins/CasinoPlugin/modules/tintiro.yml` |
| 有効化フラグ | `plugins/CasinoPlugin/config.yml` の `modules.tintiro.enabled` |
| 依存モジュール | `bank`（エメラルド口座機能。必須） |
| 専用jar | 不要（CasinoPlugin に内蔵） |

!!! info "CasinoPlugin のモジュールです"
    チンチロは専用jarを導入するゲームではありません。CasinoPlugin（エメラルド銀行＋8ゲームの統合プラグイン）の中の `tintiro` モジュールとして動作します。CasinoPlugin 本体の導入手順・共通設定は [CasinoPlugin の OP・運営ガイド](../casino-plugin/op.md) を参照してください。

## 有効化

チンチロは CasinoPlugin に内蔵されているため、**専用jarの追加導入は不要** です。CasinoPlugin が動いていれば、あとはモジュールを有効化するだけです。

1. `plugins/CasinoPlugin/config.yml` を開く。
2. `modules:` ブロックの `tintiro` を `enabled: true` にする。

    ```yaml
    modules:
      tintiro:
        enabled: true
    ```

3. サーバーを再起動（または CasinoPlugin をリロード）する。
4. 起動時に `plugins/CasinoPlugin/modules/tintiro.yml` が自動生成される。

!!! warning "bank モジュールが必須です"
    チンチロの賭け金・配当はすべてエメラルド口座を通じて処理されます。`bank` モジュールが無効だと tintiro モジュールは起動に失敗します（起動時に EmeraldAPI 未準備のエラーになります）。`modules.bank.enabled` は true 固定で運用してください。

## tintiro.yml 設定項目

個別設定ファイルは `plugins/CasinoPlugin/modules/tintiro.yml` です。設定項目はごく少なく、看板や地点設定コマンドで書き込まれる座標が中心です。

| キー | 既定値 | 説明 |
|---|---|---|
| `shonben-probability-percent` | `5` | サイコロ1つあたりに「ションベン」（出目0）が出る確率（パーセント）。3つのサイコロそれぞれに独立で判定される |
| `locations.spawn` | （未設定） | スポーン地点（離脱時の転送先）。`/tintiro setstartspawn` で書き込まれる |
| `locations.lobby` | （未設定） | ロビー地点（参加時・ゲーム終了時の転送先）。`/tintiro setlobby` で書き込まれる |
| `locations.field1` / `field2` | （未設定） | ゲームエリアの対角2点。`/tintiro setfield <1\|2>` で書き込まれる |
| `bet_options` | （未設定） | （任意）`bet` 看板クリックで開く掛け金変更GUIに並べる金額リスト。未設定時は既定の18段階（1〜1,000,000）を使用する |

!!! tip "掛け金GUIの金額を変えるには bet_options"
    `bet` 看板をクリックすると共通の **掛け金変更GUI** が開きます。並べる金額を変えたい場合は `tintiro.yml` に `bet_options`（数値リスト）を追加してください。未設定なら既定の18段階（1 / 5 / 10 / 25 / 50 / 100 / 250 / 500 / 1000 / 2500 / 5000 / 10000 / 25000 / 50000 / 100000 / 250000 / 500000 / 1000000）が使われます。この項目は自動生成されず、初期の `tintiro.yml` には含まれないため、必要な場合のみ手動で追記してください。

!!! note "座標は手動編集より設定コマンドを推奨"
    `locations.spawn` / `locations.lobby` / `locations.field1` / `locations.field2` は `world` / `x` / `y` / `z` / `yaw` / `pitch` を持つ座標ブロックです。ゲーム内で `/tintiro setstartspawn` ・ `/tintiro setlobby` ・ `/tintiro setfield <1|2>` を実行すると、実行時の立ち位置が自動で書き込まれます。手動で編集する場合は tintiro.yml 内のコメントに記載された形式に従ってください。

!!! tip "役の倍率は設定では変更できません"
    役の判定ロジックと配当倍率（役のランク差 × ベット額）は実装に組み込まれており、`tintiro.yml` で個別に変更する項目はありません。調整できるのは `shonben-probability-percent`（ションベン確率）のみです。

## セットアップ手順

**① 有効化** — config.yml で `modules.tintiro.enabled: true` にし、サーバーを起動する。

**② 地点を設定** — OP権限を持った状態で、設定したい場所に立って以下を実行する。

```text title="ロビー地点（参加者の集合・ゲーム進行地点）を現在地に設定"
/tintiro setlobby
```

```text title="離脱時の転送先を現在地に設定"
/tintiro setstartspawn
```

```text title="ゲームエリアの角1を現在地に設定"
/tintiro setfield 1
```

```text title="ゲームエリアの角2を現在地に設定"
/tintiro setfield 2
```

```text title="設定状況を確認"
/tintiro status
```

**③ 看板を設置** — プレイヤーが参加できるよう、後述の **チンチロ看板** を設置する。

!!! warning "ロビー地点が未設定だと看板から参加できません"
    `lobby` 地点が未設定の場合、`lobby` 看板を右クリックしても「ロビー地点が設定されていません」と表示され参加できません。モジュール起動時に座標が未設定だと、コンソールにも警告が出ます。必ず `/tintiro setlobby` を実行してください。

## チンチロ看板の設置

看板を設置し、看板を見ながら（6ブロック以内）以下のコマンドを実行すると、テキストが自動で整形されます（実行には **OP権限が必要**）。

```text title="参加看板（join / lobby どちらでも可）"
/tintiro setsign join
```

```text title="退出看板"
/tintiro setsign leave
```

```text title="振りGUI看板"
/tintiro setsign open
```

```text title="掛け金変更GUI看板"
/tintiro setsign bet
```

```text title="開始看板"
/tintiro setsign start
```

```text title="看板の設定を解除"
/tintiro setsign remove
```

| 看板の種類 | 右クリック時の動作 |
|---|---|
| 参加看板（`join` / `lobby`） | チンチロロビーに参加し、ロビー地点へ転送 |
| 退出看板（`leave`） | チンチロロビーから離脱し、スポーン地点へ転送 |
| 振りGUI看板（`open`） | 自分の番にサイコロGUIを開く（`/tintiro open` 相当） |
| 掛け金看板（`bet`） | クリックで掛け金変更GUIを開く |
| 開始看板（`start`） | ゲームを開始する（OP専用。クリックした人が親になる） |

!!! note "看板の自動整形について"
    `setsign` は **視線先の看板（6ブロック以内）** を種別に応じて自動整形します。1行目は `[チンチロ]`、2〜4行目は種別ごとの説明・人数表示などに書き換わります。参加看板は `join` と `lobby` のどちらの種別名でも同じ参加看板になります。`bet` 看板は金額引数を取らず、クリックすると掛け金変更GUIが開きます（旧仕様の `setsign bet <金額>` ・ `setstart` は廃止）。

!!! note "看板からの参加上限は6人"
    `lobby` 看板からの参加は最大6人までです（満員になると参加を断られます）。3行目・4行目は設置時に自動で説明文・参加人数表示に書き換わります。金額看板は末尾の `E` を付けても付けなくても認識されます。

## ゲーム運営コマンド

```text title="プレイヤーをロビーに参加させる（名前省略時は自分）"
/tintiro join <プレイヤー名>
```

```text title="プレイヤーをロビーから退出させる（名前省略時は自分）"
/tintiro leave <プレイヤー名>
```

```text title="ロビー全員のベット額を設定（例: 100エメラルド）"
/tintiro bet 100
```

```text title="ゲームを開始（実行者が親になる）"
/tintiro start
```

```text title="自分の番にサイコロGUIを開く"
/tintiro open
```

```text title="ゲームを強制終了（全体にアナウンス）"
/tintiro stop
```

## 管理コマンド一覧

チンチロのコマンドはすべて `/tintiro`（`/ti`）に集約されています。プレイヤー操作と運営操作は同じコマンド体系です。

| コマンド | 説明 |
|---|---|
| `/tintiro join [プレイヤー名]` | 指定プレイヤー（省略時は自分）をロビーに参加させる |
| `/tintiro leave [プレイヤー名]` | 指定プレイヤー（省略時は自分）をロビーから退出させる |
| `/tintiro bet <金額>` | ロビー全員のベット額を設定する |
| `/tintiro start` | ゲームを開始する（実行者が親になる） |
| `/tintiro open` | 自分の番にサイコロGUIを開く |
| `/tintiro stop` | ゲームを強制終了する（全体にアナウンスされる） |
| `/tintiro setstartspawn` | 初期リスポーン地点を現在地に設定する |
| `/tintiro setlobby` | ロビー地点を現在地に設定する |
| `/tintiro setfield <1\|2>` | ゲームエリアの角1/角2を現在地に設定する |
| `/tintiro status` | 地点の設定状況を表示する |
| `/tintiro setsign <join\|leave\|open\|bet\|start\|remove>` | 視線先の看板をチンチロ看板として登録/解除する（`bet` は掛け金変更GUI看板になり金額引数は不要） |

!!! note "地点設定コマンドはプレイヤー専用"
    `setstartspawn` / `setlobby` / `setfield` は立ち位置を座標として保存するため、コンソールからは実行できません（プレイヤーが実行する必要があります）。`stop` は進行中のゲームを強制終了し、全体に「○○ がゲームを強制終了しました」とアナウンスします。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `tintiro.admin` | OP | plugin.yml に定義される管理権限ノード |

!!! info "/tintiro コマンドは原則 OP 専用（判定は OP かどうか）"
    `/tintiro` 系のコマンド（`bet` / `start` / `open` / `stop` / `setstartspawn` / `setlobby` / `setfield` / `status` / `setsign`）は **OP（またはコマンドブロック）のみ** 実行できます。判定は実際には「OP かどうか」で行われるため、`tintiro.admin` 権限を非 OP プレイヤーに付与してもコマンドは実行できません。例外として `/tintiro join` ・ `/tintiro leave` は **自分自身を対象にする場合のみ一般プレイヤーでも実行可能** で、他プレイヤー名を指定して参加/離脱させる場合のみ OP 権限が必要です。一般プレイヤーは `[チンチロ]` 看板（`join` / `leave` / `bet` / `open`）から操作してください。チンチロ看板の設置にも OP 権限が必要です。

## トラブルシューティング

??? failure "`/tintiro` が「不明なコマンド」になる"
    `config.yml` の `modules.tintiro.enabled` が `false` になっていないか確認してください。無効モジュールはコマンド・リスナーが一切登録されません。起動ログに「module [tintiro] は config で無効化されています」が出ていないか確認します。

??? failure "起動ログに tintiro モジュールの有効化失敗が出る"
    tintiro は `bank` モジュール（EmeraldAPI）に依存します。`modules.bank.enabled` が true になっているか確認してください。bank が無効・未起動だと「EmeraldAPI not bound; bank module required before tintiro」で起動に失敗します。

??? failure "looby 看板を右クリックしても参加できない"
    `lobby` 地点が未設定の可能性があります。OP権限で `/tintiro setlobby` を実行し、`/tintiro status` で「設定済み」になっているか確認してください。また、ゲーム進行中や満員（6人）のときも参加できません。

??? failure "チンチロ看板が設置できない"
    看板の設置（`/tintiro setsign`）には **OP権限** が必要です。視線先（6ブロック以内）に看板がある状態で `/tintiro setsign <join|leave|open|bet|start>` を実行してください。看板が見つからない場合は「看板を見ながら実行してください」と表示されます。

??? failure "ゲームが開始できない"
    `/tintiro start` には、親（コマンド実行者）以外に **最低1人の子** が必要です。また、各プレイヤーがベット額を設定し、**ベット額の最大支払額ぶんの残高** を持っている必要があります。残高不足のプレイヤーは自動でロビーから退出させられます。

??? failure "賭け金・配当が処理されない"
    エメラルドのやり取りは `bank` モジュール（EmeraldAPI）経由です。bank モジュールが有効か確認してください。送金に失敗した場合はコンソールに「EmeraldAPI transact failed!」の警告が出ます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← チンチロ 概要へ](index.md){ .md-button }
