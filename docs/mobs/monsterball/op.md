<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Monsterball ― OP・運営ガイド { .page-op #monsterball-op }

Monsterball の導入、コマンド、権限、配布手順、トラブルシュートをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Monsterball |
| バージョン | 1.0-SNAPSHOT |
| api-version | 26.1.2 |
| メインクラス | `com.example.monsterball.Monsterball` |
| メインコマンド | `/monsterball`（サブ: `give` / `restore`） |
| 依存プラグイン | なし |
| 設定ファイル | なし（`config.yml` は使用しない） |
| アイテム素材 | `SLIME_BALL`（PDC でモンスターボール判定） |

!!! info "似たプラグインとの併用について"
    別途 **VillagerBall** プラグインがある場合、両者とも「村人を右クリックで捕獲」する仕様のため、**同じプレイヤーが両方のボールを所持していると意図しない誤動作の原因**になります。運用上はどちらかに統一するのが安全です。

## 導入手順

1. ビルド済みの `Monsterball-1.0-SNAPSHOT.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動する。コンソールに `Monsterball Listener initialized. Reflection will be attempted on first use.` が出れば読み込み成功。
3. 初回の捕獲時にリフレクションが初期化されます。成功すると `All Villager reflection methods successfully initialized!` が表示されます。失敗時は警告ログが出ます（後述「トラブルシュート」参照）。

!!! note "config.yml はありません"
    Monsterball には設定ファイルがありません。挙動を変更する設定項目はなく、コードに直書きされた挙動で動作します。

## アイテム配布手順（`/monsterball give`）

OP のみ実行できます。

```text
/monsterball give
```

- 実行プレイヤーのインベントリに **空のモンスターボール 1 個** が追加されます。
- コンソールや非プレイヤーからは実行できません（「このコマンドはプレイヤーのみ実行可能です。」）。
- 個数指定オプションはありません（1 回 1 個）。必要数だけ繰り返し実行するか、配布マクロを使ってください。

## 取引データ復元コマンド（`/monsterball restore`）

```text
/monsterball restore
```

- OP のみ実行可能。
- 実行者の **視線から 5 ブロック以内** にいる村人をターゲットとして検出します（視線との内積 > 0.99 ≒ 約 8 度以内）。
- 対象の村人に一時保存された取引データ（PDC キー `monsterball:temp_data`）を読み出し、職業・レベル・取引レシピ・経験値・カスタム名を復元します。
- 成功時はそのキーが削除されます。失敗時はキーが残り、再試行できます。

!!! warning "なぜ手動復元が必要か"
    村人の解放直後に取引を一括設定しても、Minecraft 側がデフォルト取引で上書きするタイミングがあるため、完全復元には **解放後に改めて `restore` を実行する** 二段構えが必要です。プレイヤーは OP に依頼する形になります。

## コマンド一覧

| コマンド | 権限 | 説明 |
|---|---|---|
| `/monsterball` | 全員（実行のみ可） | サブコマンドのヘルプを表示 |
| `/monsterball give` | OP | 空のモンスターボールを 1 個取得 |
| `/monsterball restore` | OP | ターゲットしている村人の取引データを復元 |

タブ補完: 第 1 引数で `give` / `restore` を補完します。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `monsterball.admin` | OP | `plugin.yml` の `commands.monsterball.permission` に指定されている管理権限 |

!!! warning "実コードの権限チェックは `isOp()`"
    `plugin.yml` では `monsterball.admin`（既定 op）が宣言されていますが、コマンド実装側（`MonsterballCommand`）は **`player.isOp()` で判定** しています。LuckPerms 等で `monsterball.admin` だけ付与しても `give` / `restore` は通りません。`isOp()` で判定されるため、運用上は **OP フラグ** を付与する必要があります（食い違い）。

## 仕組み（内部仕様の要点）

- アイテムは **スライムボール** を流用。PDC キー `monsterball:is_ball` で「モンスターボールである」ことを判定します。
- 捕獲済みボールには PDC キー `monsterball:entity_data`（JSON 文字列）と `monsterball:entity_name` が追加されます。
- 解放時は村人エンティティの PDC に `monsterball:temp_data` として元データを一時保存し、`/monsterball restore` でそれを読み出して反映します。
- 取引データ（レシピ・経験値・職業）の取得・設定は **リフレクション** で `getVillagerData` / `setVillagerData` / `getRecipes` / `setRecipes` / `getVillagerExperience` / `setVillagerExperience` などを呼び出しています。Paper / Spigot のバージョンによっては動作しない可能性があります。

## トラブルシュート

??? failure "「権限がありません。」と表示される"
    現在の実装は **OP フラグ判定** です。`/op <名前>` で OP 権限を付与するか、サーバー設定で OP として登録してください。LuckPerms 等のノード付与だけでは通りません。

??? failure "捕獲できない／右クリックしても反応がない"
    - 対象が **村人（Villager）以外** ではないか確認してください。Monsterball は村人のみ対応です。
    - すでに **データ入りのボール** を持っていないか確認してください（チャットに「既にデータが入っています」と出ます）。
    - メインハンドにモンスターボールを持っているか確認してください（オフハンドでは反応しません）。

??? failure "解放後、取引が初期化される"
    仕様です。解放後に **`/monsterball restore` で復元** してください。それでも復元されない場合は、サーバー起動時ログに `Full data capture reflection failed.` が出ていないか確認します（リフレクション失敗時は取引・職業データが保存されません）。

??? failure "リフレクション失敗のログが出る"
    `Villager trade data (recipes/experience/profession) will NOT be fully saved.` というログが出る場合、Bukkit/Paper API のメソッド名がプラグインの想定（`getVillagerData` 等）と一致していません。サーバーバージョン（特に Mojang マッピングの違い）を確認してください。基本の名前・タイプは保存されますが、職業・レベル・取引は保存・復元できません。

??? failure "解放した村人が壁の中・空中の高い場所に出現する"
    解放位置はブロック右クリック時はその面の外側、空中右クリック時はプレイヤーの前方 1.5 ブロック先（Y は +0.5）です。狭所では押し出されたり詰まったりするので、開けた場所で解放してください。

## 既知の制約

- **捕獲対象は村人のみ。** 他のモブ（行商人 Wandering Trader 含む `Villager` のサブクラス外）は捕獲できません。
- **設定ファイルなし。** 動作のカスタマイズはできません。
- **権限はコード上 `isOp()` 判定。** パーミッションプラグインだけでの権限委譲はできません。
- **アイテムはスライムボール流用。** クラフトレシピやドロップ取得はなく、配布は `/monsterball give` のみです。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Monsterball 概要へ](index.md){ .md-button }
