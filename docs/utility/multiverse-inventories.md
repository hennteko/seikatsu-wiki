<div class="audience-banner op">🛠️ OP・運営向けページ — Multiverse-Inventories は管理者（OP）専用のアドオンプラグインです。</div>

# Multiverse-Inventories ― OP・運営ガイド { .page-op }

<span class="badge done">公開プラグイン</span>

ワールド（またはワールドグループ）ごとに、インベントリ・経験値・体力・実績などを **分離** できる Multiverse のアドオンです。たとえば「ミニゲーム用ワールド」と「サバイバルワールド」で持ち物を別々に管理できます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Multiverse-Inventories |
| バージョン | 5.3.2 |
| 種別 | 公開プラグイン（mvplugins.org 製） |
| メインコマンド | `/mvinv` |
| 依存 | Multiverse-Core（必須） |
| 設定ファイル | `plugins/Multiverse-Inventories/config.yml` ／ `groups.yml` |
| 公式サイト | https://mvplugins.org |

## 仕組み（グループとシェア）

- **グループ** … 複数のワールドをまとめた単位。同じグループ内のワールドではデータが共有され、グループが違うとデータが分離されます。
- **シェア（shares）** … 何を共有・分離するかの項目。`inventory_contents`（インベントリ）・`ender_chest_contents`（エンダーチェスト）・`exp`（経験値）・`health`（体力）・`hunger`（満腹度）・`bed_spawn`（リスポーン地点）など。

つまり「ミニゲーム用ワールドを1つのグループにまとめ、サバイバルとは別グループにする」と、両者で持ち物が完全に分かれます。

## 導入手順

1. Multiverse-Core を先に導入しておく。
2. [mvplugins.org](https://mvplugins.org) から Multiverse-Inventories 5.3.2 を入手し、`plugins/` に配置。
3. サーバーを再起動する。
4. 下記コマンドでグループを作成・設定する。

## コマンド一覧

メインコマンドは `/mvinv` です。

| コマンド | 説明 |
|---|---|
| `/mvinv create-group <名前>` | 新しいグループを作成する |
| `/mvinv delete-group <名前>` | グループを削除する |
| `/mvinv add-worlds <グループ> <ワールド...>` | グループにワールドを追加（ワイルドカード・正規表現に対応） |
| `/mvinv remove-worlds <グループ> <ワールド...>` | グループからワールドを削除 |
| `/mvinv add-shares <グループ> <シェア...>` | グループに共有項目を追加 |
| `/mvinv remove-shares <グループ> <シェア...>` | グループから共有項目を削除 |
| `/mvinv list` | グループ一覧を表示 |
| `/mvinv info <グループ>` | グループの詳細（所属ワールド・シェア）を表示 |
| `/mvinv migrate <元> <先>` | プレイヤーデータを移行する |
| `/mvinv reload` | 設定を再読み込みする |

!!! tip "引数の詳細はゲーム内ヘルプが最も正確です"
    各コマンドの細かい引数は、ゲーム内で `/mvinv` と入力すると表示されるヘルプが最新かつ正確です。

## 権限ノード

OP は既定ですべてのコマンドを使用できます。スタッフへ委譲する場合は `multiverse.inventories.*`（全許可）や、`multiverse.inventories.group` / `.info` / `.list` / `.migrate` などの個別ノードを付与します。

**バイパス権限** … 特定のプレイヤーを分離対象から除外できます（config の `enable-bypass-permissions: true` が必要）。

| 権限 | 効果 |
|---|---|
| `mvinv.bypass.group.<グループ名>` | そのグループの分離を無視する |
| `mvinv.bypass.world.<ワールド名>` | そのワールドの分離を無視する |
| `mvinv.bypass.*` | すべてのグループ・ワールドの分離を無視する |

正確なノード一覧は公式の[Permissions List](https://mvplugins.org/inventories/reference/permissions-list/)を参照してください。

## 設定ファイル

| ファイル | 内容 |
|---|---|
| `config.yml` | 全体設定（バイパス権限の有効化・最終位置の保存など） |
| `groups.yml` | グループ定義。コマンドで管理されるため手動編集は最小限に |

## 注意点・トラブルシューティング

??? failure "持ち物が分離されない / 共有されてしまう"
    対象ワールドが正しいグループに入っているか `/mvinv info <グループ>` で確認してください。グループ未所属のワールドの扱いは config の設定に依存します。

??? warning "データ移行・グループ変更は慎重に"
    `/mvinv migrate` やシェア構成の変更はプレイヤーデータに影響します。実行前にワールド・プレイヤーデータのバックアップを取ってください。

??? failure "プラグインが起動しない"
    Multiverse-Core が必須です。Core を先に導入し、Core・アドオンとも 5系でバージョンを揃えてください。

## 公式リンク

- 公式ドキュメント： https://mvplugins.org
- コマンド解説： https://mvplugins.org/inventories/fundamentals/commands-usage/
- 権限一覧： https://mvplugins.org/inventories/reference/permissions-list/
- シェア一覧： https://mvplugins.org/inventories/reference/shares-list/
