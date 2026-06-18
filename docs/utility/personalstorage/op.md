<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。使い方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# PersonalStorage（個人ストレージ）― OP・運営ガイド { .page-op #personalstorage-op }

PersonalStorage の導入・コマンド・config・データ保存・権限をまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | PersonalStorage |
| メインコマンド | `/ps`（エイリアス `/personalstorage`、OP / `ps.admin`） |
| プレイヤー操作 | 「個人ストレージ」の本を右クリック（権限不要） |
| 依存プラグイン | なし（Paper系サーバー前提） |
| 設定ファイル | `plugins/PersonalStorage/config.yml` |
| データ保存 | `plugins/PersonalStorage/playerdata/<UUID>.json`（プレイヤーごと） |

!!! info "仕組み"
    プレイヤーごとに独立した無限スタック倉庫を提供します。アクセスは **配布した本の右クリック** で行い、コマンドは原則OP用です。データは UUID ごとの JSON にシリアライズ保存され、外部DBは不要です。

## 導入手順

1. ビルドした `PersonalStorage` の jar を `plugins/` に配置する。
2. サーバーを起動すると `plugins/PersonalStorage/config.yml` と `playerdata/` フォルダが生成される。
3. `/ps give <プレイヤー>` で各プレイヤーに「個人ストレージ」の本を配布する。
4. 使わせたくないワールドがあれば `/ps ngworld <ワールド名>` で登録する。

## コマンド

すべて `ps.admin`（既定OP）が必要です（本の右クリックは権限不要）。

| コマンド | 説明 |
|---|---|
| `/ps give` | 自分に「個人ストレージ」の本を取得する（プレイヤー実行時） |
| `/ps give <プレイヤー>` | 指定プレイヤーに本を配布する（オンラインのみ） |
| `/ps open` | 自分の倉庫GUIを開く（プレイヤー専用） |
| `/ps save` | ログイン中プレイヤーのデータを手動でバックアップ保存する |
| `/ps ngworld <ワールド名>` | 本を使用禁止にするワールドを追加する（config保存） |
| `/ps okworld <ワールド名>` | 使用禁止ワールドから解除する（config保存） |

!!! note "usage 表記との差異"
    `plugin.yml` の usage 表記は `/ps <give|open>` のみですが、実際には上記6サブコマンド（`save` / `ngworld` / `okworld` を含む）が実装されています。タブ補完にも対応しています。

## config.yml

```yaml
# 個人ストレージ本を使用できないワールドのリスト
ng-worlds:
  - "example_world_disable"
```

| キー | 既定値 | 説明 |
|---|---|---|
| `ng-worlds` | `["example_world_disable"]` | この一覧のワールドでは本を右クリックしても倉庫を開けない（「このワールドでは使用できません。」と表示） |

- 既定値の `example_world_disable` はダミーのワールド名です。実在ワールドを禁止したい場合は `/ps ngworld <ワールド名>` で追加してください（`/ps okworld` で解除）。
- ワールド名は **大文字・小文字を区別** します。コマンド引数は正確に一致させてください。

!!! warning "既存サーバーは config が自動追記されません"
    本プラグインは `saveDefaultConfig()` のみで、既存の `config.yml` に新キーを自動追記しません。ただし `ng-worlds` は `/ps ngworld` / `okworld` コマンドで動的に追記・保存されるため、通常は手動編集不要です。

## データ保存

- 保存先: `plugins/PersonalStorage/playerdata/<UUID>.json`。各アイテムは Bukkit シリアライズ → Base64 文字列＋数量（`long`）で保持し、エンチャント等のメタ情報も保存されます。
- 保存タイミング: **5分ごとの自動保存**（非同期）／`/ps save`（手動）／**プレイヤー退出時**（保存してメモリ解放）／サーバー停止時。
- ログイン中プレイヤーのデータのみメモリに保持する設計です。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `ps.admin` | OP | `/ps` の全コマンド（give / open / save / ngworld / okworld）とタブ補完 |

!!! note "本の使用に権限は不要"
    「個人ストレージ」の本の右クリックには権限チェックがありません。本を配布されたプレイヤーは誰でも自分の倉庫を開けます（NGワールドでのみブロック）。

## 注意・既知の制限

!!! warning "サーバー種別・バージョン表記"
    GUI判定や表示に Adventure API を使用するため **Paper 系サーバーが前提** です（素の Spigot では動作しない可能性があります）。また `plugin.yml` の `api-version` がこのプロジェクト独自の表記（`26.1.2`）になっており、環境によっては読み込みに注意が必要です。

!!! note "運用上の留意点"
    - 1種類あたり実質無制限に保管できるため、サーバー経済への影響を考慮して配布範囲を決めてください。
    - 機能は収納・取り出しのみで、カテゴリ分け・検索・自動収納などはありません（高度な共有倉庫が必要な場合は GlobalStorage を併用）。
    - Shift＋クリックでの一括取り出し時、インベントリの空き状況によっては数量計算がずれる可能性が指摘されています。大量取り出し時は挙動を確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← PersonalStorage 概要へ](index.md){ .md-button }
