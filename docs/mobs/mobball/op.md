<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# MobBall ― OP・運営ガイド { .page-op #mobball-op }

MobBall の導入・モジュール構成・`config.yml`・コマンド・権限・旧プラグインからの移行手順をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | MobBall |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインクラス | `jp.henry.mobball.MobBall` |
| 依存プラグイン | なし（`softdepend: [CasinoPlugin]`） |
| 設定ファイル | `plugins/MobBall/config.yml` ＋ `plugins/MobBall/modules/<id>.yml` |
| 配布 jar | `MobBall-1.0.0.jar` |

!!! info "統合プラグインです"
    MobBall は、生活鯖で別々に動いていた **VillagerBall**（村人をエンダーパール/エンダーアイ風ボールに収納）と **Monsterball**（村人をスライムボール風ボールに収納＋手動 restore で取引復元）の 2 プラグインを 1 つの jar に統合したものです。`ModuleRegistry` パターンで内部を **villager / monster の 2 モジュール** に分割しており、`config.yml` のフラグで個別に ON / OFF できます。旧 plugin.yml のコマンドと権限はそのまま維持しているため、**既存のサーバー運用・LuckPerms 設定は無修正でそのまま動きます**。

## 導入手順

1. ビルドした `MobBall-1.0.0.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/MobBall/config.yml` と `plugins/MobBall/modules/villager.yml`・`monster.yml` が自動生成される。
3. `config.yml` の `modules:` ブロックで、使いたいモジュールだけを `enabled: true` にする（既定はどちらも `true`）。
4. 各モジュールの個別設定（村人ボールの素材・成功率・クールダウンなど）は `modules/villager.yml` を編集する（monster には config なし）。
5. 設定を変更したら `/villagerball reload`（villager モジュール）または `/mb reload`（メイン config）で再読み込みする。

!!! tip "旧プラグインを併用していた場合"
    旧 VillagerBall と Monsterball の **両方を併用**していたサーバーでは、ボールが「素材」と「PDC キー」で区別されているため、両モジュールを `enabled: true` にしたままで併用できます。混乱を避けたい場合は、片方を `enabled: false` にして運用を統一してください。

## モジュール一覧

| モジュール ID | 由来プラグイン | 内容 | 個別設定ファイル |
|---|---|---|---|
| `villager` | VillagerBall | 村人をエンダーパール/エンダーアイ風のボールに収納。取引データは捕獲時に JSON 保存 → 解放時に自動復元 | `modules/villager.yml` |
| `monster` | Monsterball | 村人をスライムボール風のボールに収納。取引データは捕獲時に保存 → 解放後 `/monsterball restore` で復元 | `modules/monster.yml`（空・元プラグインに config なし） |

## config.yml 主要項目

`config.yml` は **モジュール有効フラグと全体設定のみ** を扱います。各モジュールの個別設定は `modules/<id>.yml` に分離されています。

| キー | 既定値 | 説明 |
|---|---|---|
| `modules.villager.enabled` | `true` | villager モジュールの有効化フラグ |
| `modules.monster.enabled` | `true` | monster モジュールの有効化フラグ |
| `debug.verbose` | `false` | 詳細ログ出力（現状未使用、将来拡張用） |

!!! note "モジュールを止めるとどうなるか"
    `modules.<id>.enabled: false` にしたモジュールは `onEnable` が呼ばれず、**コマンドもリスナーも一切登録されません**。例えば monster だけを停止すれば `/monsterball` は「不明なコマンド」になり、モンスターボールでの捕獲・解放も無効になります。既存配布済みのモンスターボールアイテムが残っていても、そのモジュールが OFF なら反応しません。

### villager モジュール（`modules/villager.yml`）

旧 `plugins/VillagerBall/config.yml` と **完全互換** のフォーマットです。既存サーバーから移行する場合、旧ファイルをリネームコピーするだけで済みます。

| キー | 既定値 | 説明 |
|---|---|---|
| `messages.*` | 旧 VillagerBall そのまま | プレフィックスと各種メッセージ（`&` カラーコード） |
| `item.empty.material` | `ENDER_PEARL` | 空のボールの素材 |
| `item.empty.name` | `&6村人ボール &7(空)` | 表示名 |
| `item.empty.custom-model-data` | `0` | カスタムモデルデータ |
| `item.empty.glow` | `true` | エンチャント光沢 |
| `item.filled.material` | `ENDER_EYE` | 村人入りボールの素材 |
| `item.filled.name` | `&6村人ボール &a(村人入り)` | 表示名 |
| `item.filled.custom-model-data` | `1` | カスタムモデルデータ |
| `gameplay.capture-success-rate` | `1.0` | 捕獲成功率（0.0〜1.0） |
| `gameplay.profession-rates.enabled` | `false` | 職業別成功率の有効化 |
| `gameplay.profession-rates.rates.<JOB>` | 0.8〜1.0 | 職業別成功率（大文字キー：`FARMER`, `LIBRARIAN` 等） |
| `gameplay.allow-capture-while-trading` | `false` | 取引中の村人を捕獲できるか |
| `gameplay.effects.capture.sound` | `ENTITY_ENDERMAN_TELEPORT` | 捕獲時のサウンド |
| `gameplay.effects.capture.particle` | `PORTAL` | 捕獲時のパーティクル |
| `gameplay.cooldown.enabled` | `false` | クールダウン機能の有効化 |
| `gameplay.cooldown.capture` | `3` | 捕獲後クールダウン（秒） |
| `gameplay.cooldown.release` | `1` | 解放後クールダウン（秒） |
| `data.save-trades` | `true` | 取引内容を保存 |
| `data.save-experience` | `true` | 経験値を保存 |
| `data.save-health` | `true` | 体力を保存 |
| `data.save-custom-name` | `true` | カスタム名を保存 |
| `data.save-profession` | `true` | 職業を保存 |
| `data.save-villager-level` | `true` | 村人レベルを保存 |
| `data.save-villager-type` | `true` | バイオームタイプを保存 |

!!! tip "サウンド・パーティクル名の表記"
    旧形式（`ENTITY_ENDERMAN_TELEPORT`）も新形式（`entity.enderman.teleport`）も受け付けます。内部で大文字／アンダースコアを小文字／ドットに変換し、Paper 26.1.2 の Registry 経由で解決されます。不明な名前を指定するとサーバーログに警告が出ます。

### monster モジュール（`modules/monster.yml`）

旧 Monsterball には設定ファイルがありませんでした。MobBall でも **monster モジュールには調整可能な設定項目はありません**（空ファイルが自動生成されるのみ）。挙動はコードに直書きされた値で動作します。

## コマンド体系

| コマンド | エイリアス | 必要権限 | 役割 |
|---|---|---|---|
| `/mobball` | `/mb` | `mobball.admin`（modules / reload） | 統合管理（`modules` / `reload` / `version`） |
| `/villagerball give [プレイヤー] [個数]` | `/vb`, `/vball` | `villagerball.admin` | 空の村人ボールを配布 |
| `/villagerball reload` | `/vb`, `/vball` | `villagerball.admin` | villager モジュールの config をリロード |
| `/villagerball help` | `/vb`, `/vball` | なし | コマンドヘルプ |
| `/monsterball give` | ― | `monsterball.admin` | 空のモンスターボールを 1 個取得 |
| `/monsterball restore` | ― | `monsterball.admin` | ターゲットしている村人（5 ブロック以内、視線内積 > 0.99）の取引データを復元 |
| `/monsterball` | ― | なし | サブコマンドのヘルプを表示 |

!!! info "既存コマンド体系は維持"
    `/villagerball`・`/monsterball` は旧プラグインと同じ名前・同じエイリアス・同じ権限ノードで動きます。プレイヤー向けマクロ・アナウンス・サーバー設定はそのまま使えます。新規追加されたのは統合管理用の `/mobball`（`/mb`）だけです。

## 権限ノード

| 権限 | 既定 | 用途 | 由来 |
|---|---|---|---|
| `mobball.admin` | op | `/mb modules`・`/mb reload`（統合管理） | 新規追加 |
| `villagerball.admin` | op | `/villagerball give`・`/villagerball reload` | VillagerBall 既存 |
| `villagerball.use` | true | 基本利用（plugin.yml に定義） | VillagerBall 既存 |
| `villagerball.capture` | true | 空のボールで村人を捕獲 | VillagerBall 既存 |
| `villagerball.release` | true | 村人入りボールから解放 | VillagerBall 既存 |
| `monsterball.admin` | op | `/monsterball give`・`/monsterball restore` | Monsterball 既存 |

!!! note "monster モジュールの権限判定について（旧仕様からの修正）"
    旧 Monsterball では `MonsterballCommand` 内部で `player.isOp()` で判定していたため、LuckPerms 等で `monsterball.admin` を付与しても通らないという食い違いがありました。MobBall ではコマンド実装も `player.hasPermission("monsterball.admin")` に修正されており、**plugin.yml の宣言どおりに権限プラグインから付与できます**。

## 既存サーバーからの移行

!!! warning "既存サーバーからの移行手順"
    旧 VillagerBall / Monsterball が稼働しているサーバーに導入する場合は、以下の手順で **配布済みのボールアイテムを壊さずに** 差し替えできます。

    1. **旧 jar を削除** ― `plugins/VillagerBall.jar` と `plugins/Monsterball.jar` を削除する。
    2. **MobBall-1.0.0.jar を配置** ― `plugins/` に `MobBall-1.0.0.jar` を置く。
    3. **既存 config を継承（任意）** ― 旧 `plugins/VillagerBall/config.yml` を `plugins/MobBall/modules/villager.yml` に **リネームコピー** すれば、旧設定がそのまま継承されます（フォーマット完全互換）。Monsterball は元から config がないため作業不要。
    4. **サーバー再起動** ― `/villagerball`・`/monsterball` がそのまま動くこと、`/mb modules` で両モジュールが ENABLED になっていることを確認。
    5. **既存配布済みボールの動作確認**（重要） ― 既存プレイヤーが持っている村人ボール／モンスターボールが、**捕獲・解放ともそのまま動くか** 必ず確認する。

    🟢 **既存配布済みのボールアイテムは PDC owner（`villagerball:*` / `monsterball:*`）を維持したまま無修正で動作します**。MobBall 側で legacy fallback として旧 NamespacedKey を読みに行く実装になっているため、サーバー在庫・チェスト・プレイヤーインベントリ内のボールを再配布する必要はありません。

    🟢 **LuckPerms 等の権限設定は無修正で OK**。`villagerball.*` / `monsterball.*` の権限ノードは旧プラグインと同名・同既定値で残っています。

!!! tip "ロールバック"
    万一トラブルがあれば旧プラグインに戻せます。旧 2 つの jar はビルド成果物として残っており、`plugin.yml.bak_20260526` / `.bak_20260527` のバックアップも各プラグインフォルダに退避されています。

## トラブルシューティング

??? failure "あるモジュールのコマンドが「不明なコマンド」になる"
    そのモジュールが `config.yml` の `modules.<id>.enabled: false` で無効化されている可能性があります。無効モジュールはコマンド・リスナーが一切登録されません。`/mb modules` または起動ログで状態を確認してください。

??? failure "起動ログに「module [<id>] の有効化に失敗しました」と出る"
    そのモジュールの `onEnable` で例外が発生しています。`ModuleRegistry` は例外を捕捉してログに残すため、もう一方のモジュールは動き続けますが、該当モジュールは停止状態です。スタックトレースを確認し、`modules/<id>.yml` の設定不備などを疑ってください。

??? failure "村人を右クリックしてもボールに入らない（villager モジュール）"
    手に持っているのが **空の村人ボール**（PDC `ball_type=empty`、新キー `mobball:ball_type` または旧キー `villagerball:ball_type`）か確認してください。素材だけ揃えた自作エンダーパールでは判定されません。必ず `/villagerball give` で配布されたボールを使ってください。また、`villagerball.capture` 権限・捕獲クールダウン・取引中の村人かどうか・捕獲成功率も確認してください。

??? failure "村人入りボールを右クリックしても解放されない（villager モジュール）"
    解放は **空中またはブロックへの右クリック** で発動します。村人や他のエンティティへの右クリックでは発動しません。`villagerball.release` 権限・解放クールダウンも確認してください。村人データが破損している場合は「&c村人の解放に失敗しました。」と表示され、サーバーログに復元失敗のエラーが出力されます。

??? failure "解放した村人の取引がバニラと違う／空になっている（villager モジュール）"
    保存される取引材料は `Material` と数量のみで、カスタム NBT・エンチャント・カスタムモデルデータは復元されません。エンチャント本など複雑なアイテムを売る村人は、解放時にプレーンな本など別のアイテムに置き換わる可能性があります。`data.save-trades: false` で取引保存を無効化し、解放後にゼロから再構築する運用も検討してください。

??? failure "モンスターボールで捕獲はできたが、解放後の取引が初期化される（monster モジュール）"
    仕様です。Minecraft 側が解放直後にデフォルト取引で上書きするため、**5 ブロック以内で村人を直視**しながら `/monsterball restore` を実行して復元してください。それでも復元されない場合、サーバー起動時ログに `Full data capture reflection failed.` が出ていないか確認します（リフレクション失敗時は取引・職業データが保存されません）。

??? failure "OPなのに `/villagerball give` / `/monsterball give` が使えない"
    `plugin.yml` でコマンドに `permission: villagerball.admin` / `monsterball.admin`（既定 `op`）が設定されています。OP 権限を付与しているか、LuckPerms 等で対応する権限ノードを直接付与しているか確認してください。monster モジュールについては、旧 `isOp()` 判定から `hasPermission("monsterball.admin")` 判定に **修正済み** です。

??? failure "職業別の成功率が反映されない（villager モジュール）"
    `gameplay.profession-rates.enabled: true` に変更し、`gameplay.profession-rates.rates.<JOB>` を **大文字キー**（例: `FARMER`, `LIBRARIAN`, `NITWIT`）で設定してください。設定変更後は `/villagerball reload` を実行します。

??? failure "既存配布済みのボールが反応しない"
    `/mb modules` で該当モジュールが ENABLED になっているか確認してください。MobBall は legacy fallback として旧 PDC キー（`villagerball:*` / `monsterball:*`）も読みに行きますが、モジュール自体が無効ならリスナーが登録されていないので反応しません。それでも反応しない場合は、サーバーログにアイテム判定のエラーが出ていないか確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← MobBall 概要へ](index.md){ .md-button }
