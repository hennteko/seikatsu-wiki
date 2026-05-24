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

個別設定ファイルは `plugins/CasinoPlugin/modules/tintiro.yml` です。設定項目はごく少なく、看板や `/tintiro setup` で書き込まれる座標が中心です。

| キー | 既定値 | 説明 |
|---|---|---|
| `shonben-probability-percent` | `5` | サイコロ1つあたりに「ションベン」（出目0）が出る確率（パーセント）。3つのサイコロそれぞれに独立で判定される |
| `locations.spawn` | （未設定） | スポーン地点（離脱時の転送先）。`/tintiro setup spawn` で書き込まれる |
| `locations.lobby` | （未設定） | ロビー地点（参加時・ゲーム終了時の転送先）。`/tintiro setup lobby` で書き込まれる |

!!! note "座標は手動編集より /tintiro setup を推奨"
    `locations.spawn` / `locations.lobby` は `world` / `x` / `y` / `z` / `yaw` / `pitch` を持つ座標ブロックです。ゲーム内で `/tintiro setup spawn` ・ `/tintiro setup lobby` を実行すると、実行時の立ち位置が自動で書き込まれます。手動で編集する場合は tintiro.yml 内のコメントに記載された形式に従ってください。

!!! tip "役の倍率は設定では変更できません"
    役の判定ロジックと配当倍率（役のランク差 × ベット額）は実装に組み込まれており、`tintiro.yml` で個別に変更する項目はありません。調整できるのは `shonben-probability-percent`（ションベン確率）のみです。

## セットアップ手順

1. config.yml で `modules.tintiro.enabled: true` にし、サーバーを起動する。
2. チンチロを設置したい場所に行き、OP権限を持った状態で `/tintiro setup lobby` を実行する（参加者の集合・ゲーム進行地点）。
3. 続けて離脱時の転送先で `/tintiro setup spawn` を実行する。
4. `/tintiro setup status` で spawn / lobby の設定状況を確認する（どちらも「設定済み」になっていればOK）。
5. プレイヤーが参加できるよう、後述の **チンチロ看板** を設置する。

!!! warning "ロビー地点が未設定だと看板から参加できません"
    `lobby` 地点が未設定の場合、`lobby` 看板を右クリックしても「ロビー地点が設定されていません」と表示され参加できません。モジュール起動時に座標が未設定だと、コンソールにも警告が出ます。必ず `/tintiro setup lobby` を実行してください。

## チンチロ看板の設置

看板の **1行目に `[チンチロ]`** と記入して設置すると、チンチロ用看板になります（設置には **OP権限が必要**）。2行目で看板の種類を指定します。

| 2行目の記入 | 看板の種類 | 右クリック時の動作 |
|---|---|---|
| `lobby` | 参加看板 | チンチロロビーに参加し、ロビー地点へ転送 |
| `leave` | 退出看板 | チンチロロビーから離脱し、スポーン地点へ転送 |
| 金額（例: `100E` / `1000`） | ベット額看板 | ロビー全員のベット額をその金額に設定 |

!!! note "看板からの参加上限は6人"
    `lobby` 看板からの参加は最大6人までです（満員になると参加を断られます）。3行目・4行目は設置時に自動で説明文・参加人数表示に書き換わります。金額看板は末尾の `E` を付けても付けなくても認識されます。

## 管理コマンド

チンチロのコマンドはすべて `/tintiro`（`/ti`）に集約されています。プレイヤー操作と運営操作は同じコマンド体系です。

| コマンド | 説明 |
|---|---|
| `/tintiro join [プレイヤー名]` | 指定プレイヤー（省略時は自分）をロビーに参加させる |
| `/tintiro leave [プレイヤー名]` | 指定プレイヤー（省略時は自分）をロビーから退出させる |
| `/tintiro bet <金額>` | ロビー全員のベット額を設定する |
| `/tintiro start` | ゲームを開始する（実行者が親になる） |
| `/tintiro open` | 自分の番にサイコロGUIを開く |
| `/tintiro stop` | ゲームを強制終了する（全体にアナウンスされる） |
| `/tintiro setup spawn` | 初期リスポーン地点を現在地に設定する |
| `/tintiro setup lobby` | ロビー地点を現在地に設定する |
| `/tintiro setup status` | spawn / lobby の設定状況を表示する |

!!! note "/tintiro setup はプレイヤー専用"
    `setup` サブコマンドは立ち位置を座標として保存するため、コンソールからは実行できません（プレイヤーが実行する必要があります）。`stop` は進行中のゲームを強制終了し、全体に「○○ がゲームを強制終了しました」とアナウンスします。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| （専用権限なし） | ― | チンチロには plugin.yml 上の専用権限ノードがありません |

!!! info "チンチロには専用の権限ノードがありません"
    `tintiro` コマンド・看板に対する専用の権限ノードは plugin.yml に定義されていません。`/tintiro` コマンドは通常どおり全員が実行でき、`join` / `leave` / `bet` / `start` / `stop` も権限チェックなしで動作します。**チンチロ看板の設置**（看板1行目に `[チンチロ]` と記入）と `/tintiro setup` 系は **OP権限（バニラの op 判定）** が必要です。一般プレイヤーに使わせたくない管理操作（`stop` など）を制限したい場合は、別途コマンド制限プラグインで対応してください。

## トラブルシューティング

??? failure "`/tintiro` が「不明なコマンド」になる"
    `config.yml` の `modules.tintiro.enabled` が `false` になっていないか確認してください。無効モジュールはコマンド・リスナーが一切登録されません。起動ログに「module [tintiro] は config で無効化されています」が出ていないか確認します。

??? failure "起動ログに tintiro モジュールの有効化失敗が出る"
    tintiro は `bank` モジュール（EmeraldAPI）に依存します。`modules.bank.enabled` が true になっているか確認してください。bank が無効・未起動だと「EmeraldAPI not bound; bank module required before tintiro」で起動に失敗します。

??? failure "looby 看板を右クリックしても参加できない"
    `lobby` 地点が未設定の可能性があります。OP権限で `/tintiro setup lobby` を実行し、`/tintiro setup status` で「設定済み」になっているか確認してください。また、ゲーム進行中や満員（6人）のときも参加できません。

??? failure "チンチロ看板が設置できない"
    看板1行目に `[チンチロ]` と記入した看板の設置には **OP権限** が必要です。OPでないと「チンチロ看板を設置するにはOP権限が必要です」と表示され設置がキャンセルされます。2行目は `lobby` / `leave` / 金額 のいずれかを記入してください。

??? failure "ゲームが開始できない"
    `/tintiro start` には、親（コマンド実行者）以外に **最低1人の子** が必要です。また、各プレイヤーがベット額を設定し、**ベット額の最大支払額ぶんの残高** を持っている必要があります。残高不足のプレイヤーは自動でロビーから退出させられます。

??? failure "賭け金・配当が処理されない"
    エメラルドのやり取りは `bank` モジュール（EmeraldAPI）経由です。bank モジュールが有効か確認してください。送金に失敗した場合はコンソールに「EmeraldAPI transact failed!」の警告が出ます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← チンチロ 概要へ](index.md){ .md-button }
