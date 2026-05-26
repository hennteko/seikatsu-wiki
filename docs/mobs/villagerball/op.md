<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# VillagerBall ― OP・運営ガイド { .page-op #villagerball-op }

VillagerBall の導入・config・配布手順・権限・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | VillagerBall |
| api-version | 26.1.2 |
| メインコマンド | `/villagerball`（`/vb`・`/vball`） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/VillagerBall/config.yml` |
| 説明 | 村人をモンスターボールのようにアイテムに収納できるプラグイン |

## 導入手順

1. ビルドした `VillagerBall.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/VillagerBall/config.yml` が自動生成される。
3. 必要に応じて `config.yml` を編集する（後述）。
4. 設定を変更したら `/villagerball reload` で再読み込みする。

## config.yml 主要項目

### メッセージ（`messages`）

プレフィックスと各種メッセージを `&` カラーコードで定義します。

| キー | 既定値 |
|---|---|
| `prefix` | `&6[VillagerBall] &r` |
| `capture-success` | `&a村人を捕獲しました！` |
| `capture-failed` | `&c村人の捕獲に失敗しました。` |
| `release-success` | `&a村人を解放しました！` |
| `release-failed` | `&c村人の解放に失敗しました。` |
| `no-permission` | `&cこのアクションを実行する権限がありません。` |
| `invalid-target` | `&c対象が村人ではありません。` |
| `inventory-full` | `&cインベントリがいっぱいです！` |
| `ball-given` | `&aVillagerBallを受け取りました！` |
| `config-reloaded` | `&a設定をリロードしました！` |
| `cannot-capture-trading` | `&c取引中の村人は捕獲できません。` |

### アイテム（`item`）

空・村人入りそれぞれのボールの素材・表示名・Lore・カスタムモデルデータ・グロー効果を設定します。

| キー | 既定値 | 説明 |
|---|---|---|
| `item.empty.material` | `ENDER_PEARL` | 空のボールの素材 |
| `item.empty.name` | `&6村人ボール &7(空)` | 表示名 |
| `item.empty.lore` | 2行 | アイテム説明 |
| `item.empty.custom-model-data` | `0` | カスタムモデルデータ（0で無効） |
| `item.empty.glow` | `true` | エンチャント光沢の有無 |
| `item.filled.material` | `ENDER_EYE` | 村人入りボールの素材 |
| `item.filled.name` | `&6村人ボール &a(村人入り)` | 表示名 |
| `item.filled.lore` | 4行 | 固定説明（その後に村人情報が自動追加される） |
| `item.filled.custom-model-data` | `1` | カスタムモデルデータ |
| `item.filled.glow` | `true` | エンチャント光沢の有無 |

!!! note "ボールの識別方法"
    ボールは PersistentDataContainer に `villagerball:ball_type`（`empty` または `filled`）を持っており、素材を変更しても識別は維持されます。村人データは `villagerball:villager_data` キーにJSON文字列として保存されます。

### ゲームプレイ（`gameplay`）

| キー | 既定値 | 説明 |
|---|---|---|
| `gameplay.capture-success-rate` | `1.0` | 捕獲成功率（0.0〜1.0、1.0で100%） |
| `gameplay.profession-rates.enabled` | `false` | 職業ごとの個別成功率を有効化 |
| `gameplay.profession-rates.rates.<JOB>` | 0.8〜1.0 | 職業別成功率（`NONE`・`FARMER`・`LIBRARIAN` など大文字キー） |
| `gameplay.allow-capture-while-trading` | `false` | 取引中の村人を捕獲できるか |
| `gameplay.effects.capture.sound` | `ENTITY_ENDERMAN_TELEPORT` | 捕獲時のサウンド |
| `gameplay.effects.capture.particle` | `PORTAL` | 捕獲時のパーティクル |
| `gameplay.effects.release.sound` | `ENTITY_ENDERMAN_TELEPORT` | 解放時のサウンド |
| `gameplay.effects.release.particle` | `PORTAL` | 解放時のパーティクル |
| `gameplay.cooldown.enabled` | `false` | クールダウン機能の有効化 |
| `gameplay.cooldown.capture` | `3` | 捕獲後のクールダウン（秒） |
| `gameplay.cooldown.release` | `1` | 解放後のクールダウン（秒） |

!!! tip "サウンド・パーティクル名の表記"
    旧形式（`ENTITY_ENDERMAN_TELEPORT`）も新形式（`entity.enderman.teleport`）も受け付けます。内部で大文字／アンダースコアを小文字／ドットに変換し、Paper 26.1.2 の Registry 経由で解決されます。不明な名前を指定するとサーバーログに警告が出ます。

### 保存データ（`data`）

各項目を `false` にすると、その情報は村人入りボールに保存されません（解放時に既定値で復元されます）。

| キー | 既定値 | 説明 |
|---|---|---|
| `data.save-trades` | `true` | 取引内容（結果アイテム・材料1〜2・使用回数・最大使用回数・経験値報酬・村人経験値・価格倍率・需要・特別価格） |
| `data.save-experience` | `true` | 村人の取引経験値 |
| `data.save-health` | `true` | 体力（最大体力を超える値はクランプされる） |
| `data.save-custom-name` | `true` | カスタム名（プレーンテキスト化して保存） |
| `data.save-profession` | `true` | 職業 |
| `data.save-villager-level` | `true` | 村人レベル |
| `data.save-villager-type` | `true` | バイオームタイプ |

!!! warning "取引保存の制約"
    保存される取引は **材料が最大2スロット** で、いずれも `Material` と数量のみです。カスタムNBT・エンチャント・カスタムモデルデータが付いたアイテム（例: エンチャント本の付与レベル、独自プラグインのアイテム）は復元されない点に注意してください。価格倍率・需要・特別価格は保存対象です。

## `/villagerball give` 配布手順

村人ボールはコマンドでのみ配布できます。

| 構文 | 動作 |
|---|---|
| `/villagerball give` | 自分に空のボールを1個渡す（コンソールからは不可） |
| `/villagerball give <プレイヤー>` | 指定プレイヤーに空のボールを1個渡す |
| `/villagerball give <プレイヤー> <個数>` | 指定プレイヤーに空のボールを指定個数渡す（1〜64） |

- 配布されるのは常に **空のボール** です。村人入りボールはコマンドで配布できません。
- 指定プレイヤーがインベントリ満杯の場合、配布は行われず「インベントリがいっぱいです」と通知されます（ドロップ等はしません）。
- タブ補完: 第1引数は `give`/`reload`/`help`、第2引数はオンラインプレイヤー名、第3引数は `1`/`8`/`16`/`32`/`64`。

!!! example "配布例"
    ```text
    /villagerball give                  # 自分に1個
    /vb give Steve                      # Steve に1個
    /vball give Alex 16                 # Alex に16個
    ```

## 管理コマンド

| コマンド | 必要権限 | 説明 |
|---|---|---|
| `/villagerball give [プレイヤー] [個数]` | `villagerball.admin` | 空のボールを配布 |
| `/villagerball reload` | `villagerball.admin` | `config.yml` を再読み込み |
| `/villagerball help` | なし | コマンドヘルプを表示 |

エイリアスは `/vb`・`/vball`。`plugin.yml` でコマンド自体に `permission: villagerball.admin` が設定されていますが、`help` サブコマンドは権限チェックを行いません（`give` と `reload` はサブコマンド内で `villagerball.admin` をチェック）。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `villagerball.admin` | OP | `/villagerball give`・`/villagerball reload` の実行 |
| `villagerball.use` | 全員 | 基本利用（plugin.yml に定義。現在の処理では参照されていません） |
| `villagerball.capture` | 全員 | 空のボールで村人を捕獲する |
| `villagerball.release` | 全員 | 村人入りボールから村人を解放する |

!!! note "捕獲・解放の権限を絞るには"
    一般プレイヤーの捕獲・解放を制限したい場合は、LuckPerms 等で `villagerball.capture`・`villagerball.release` を `false` に設定してください。捕獲または解放を試みた際に「&cこのアクションを実行する権限がありません。」が表示され、操作はキャンセルされます。

## トラブルシューティング

??? failure "村人を右クリックしてもボールに入らない"
    手に持っているのが **空の村人ボール**（PDC `ball_type=empty`）か確認してください。素材だけ揃えた自作エンダーパールでは判定されません。必ず `/villagerball give` で配布されたボールを使ってください。また、`villagerball.capture` 権限・捕獲クールダウン・取引中の村人かどうか・捕獲成功率もご確認ください。

??? failure "村人入りボールを右クリックしても解放されない"
    解放は **空中または地面（ブロック）への右クリック** で発動します。村人や他のエンティティへの右クリックでは発動しません。`villagerball.release` 権限・解放クールダウンも確認してください。村人データが破損している場合は「&c村人の解放に失敗しました。」と表示され、サーバーログに復元失敗のエラーが出力されます。

??? failure "解放した村人の取引がバニラと違う／空になっている"
    保存される取引は `Material` と数量のみで、カスタムNBT・エンチャント・カスタムモデルデータは復元されません。エンチャント本など複雑なアイテムを売る村人は、解放時にプレーンな本など別のアイテムに置き換わる可能性があります。`data.save-trades: false` で取引保存を無効化し、解放後にゼロから再構築する運用も検討してください。

??? failure "サウンド・パーティクルが鳴らない／表示されない"
    `config.yml` の `gameplay.effects.*.sound`／`particle` のキー名を確認してください。Paper 26.1.2 の Registry に存在しないキーを指定するとサーバーログに「無効なサウンド名」「無効なパーティクル名」の警告が出ます。旧形式（大文字・アンダースコア）も新形式（小文字・ドット）も受け付けます。

??? failure "OPなのに `/villagerball give` が使えない"
    `plugin.yml` でコマンド自体に `permission: villagerball.admin`（既定 `op`）が設定されています。OP権限を付与しているか、または LuckPerms 等で `villagerball.admin` を直接付与しているか確認してください。

??? failure "職業別の成功率が反映されない"
    `gameplay.profession-rates.enabled: true` に変更し、`gameplay.profession-rates.rates.<JOB>` を **大文字キー**（例: `FARMER`、`LIBRARIAN`、`NITWIT`）で設定してください。内部で職業キーを大文字化して参照しています。設定変更後は `/villagerball reload` を実行します。

## 実装メモ

- 捕獲時は `PlayerInteractAtEntityEvent`（メインハンドのみ）をフックし、`event.setCancelled(true)` でバニラの取引画面を抑止します。
- 解放時は `PlayerInteractEvent`（右クリック空中／ブロック、メインハンドのみ）をフックします。
- 村人データは JSON（Gson）でシリアライズされ、`PersistentDataContainer` の `villagerball:villager_data` キーに STRING として保存されます。
- 捕獲・解放の成否に関わらず、捕獲側は失敗時にもクールダウンが設定されます（連打抑止のため）。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← VillagerBall 概要へ](index.md){ .md-button }
