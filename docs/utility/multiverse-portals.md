<div class="audience-banner op">🛠️ OP・運営向けページ — Multiverse-Portals は管理者（OP）がポータルを設置・管理するアドオンプラグインです。</div>

# Multiverse-Portals ― OP・運営ガイド { .page-op }

<span class="badge done">公開プラグイン</span>

ワールド間・地点間を結ぶ「ポータル（範囲指定の通り抜けゲート）」を作成できる Multiverse のアドオンです。プレイヤーは設置されたポータルを通り抜けるだけで移動できます。

!!! info "PortalVisualizer との関係"
    生活鯖の自作プラグイン「PortalVisualizer」は、この Multiverse-Portals が作るポータルをパーティクルで可視化するものです。2つはセットで運用します。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Multiverse-Portals |
| バージョン | 5.2.1 |
| 種別 | 公開プラグイン（mvplugins.org 製） |
| メインコマンド | `/mvp` |
| 依存 | Multiverse-Core（必須） |
| 設定ファイル | `plugins/Multiverse-Portals/config.yml` ／ `portals.yml` |
| 公式サイト | https://mvplugins.org |

## 導入手順

1. Multiverse-Core を先に導入しておく。
2. [mvplugins.org](https://mvplugins.org) から Multiverse-Portals 5.2.1 を入手し、`plugins/` に配置。
3. サーバーを再起動する。

## ポータル作成の流れ

1. `/mvp wand` で選択ツール（既定では木の斧）を入手する。
2. 木の斧で、ポータルにする範囲の **2点** を左クリック・右クリックで選択する。
3. `/mvp create <名前> [行き先]` でポータルを作成する。
4. 行き先を後から設定・変更する場合は、ポータルを `/mvp select <名前>` で選び、`/mvp modify dest <行き先>` を実行する。

### 行き先（destination）の指定方法

| 書式 | 意味 |
|---|---|
| `w:<ワールド名>` | そのワールドのスポーン地点へ |
| `e:<ワールド>:<x>,<y>,<z>` | 座標を指定して移動（`e:@here` で実行地点） |
| `p:<ポータル名>` | 別のポータルへ |

## コマンド一覧

メインコマンドは `/mvp` です。

| コマンド | 説明 |
|---|---|
| `/mvp create <名前> [行き先]` | 選択範囲でポータルを作成する |
| `/mvp remove <名前>` | ポータルを削除する |
| `/mvp list` | ポータル一覧を表示する |
| `/mvp info <名前>` | ポータルの詳細を表示する |
| `/mvp select <名前>` | ポータルを選択する（続けて modify する用） |
| `/mvp modify <項目> <値>` | 選択中ポータルの設定を変更（`dest`＝行き先・`owner`＝所有者・`loc`＝範囲 など） |
| `/mvp wand` | ポータル作成ツール（木の斧）を入手する |
| `/mvp debug` | デバッグ表示（範囲の可視化など） |
| `/mvp reload` | 設定を再読み込みする |

!!! tip "引数の詳細はゲーム内ヘルプが最も正確です"
    各コマンドの細かい引数は、ゲーム内で `/mvp` と入力すると表示されるヘルプが最新かつ正確です。

## 権限ノード

OP は既定ですべての管理コマンドを使用できます。ポータルの **利用** に関わる権限に注意してください。

| 権限 | 効果 |
|---|---|
| `multiverse.portal.create` | ポータルを作成できる（OP・スタッフ向け） |
| `multiverse.portal.access.<ポータル名>` | そのポータルを通行できる。**既定ではポータル利用にこの権限が必要** |
| `multiverse.portal.exempt.<ポータル名>` | ポータルの利用料金を免除する |
| `multiverse.portal.fill.<ポータル名>` | ポータル枠を水・溶岩で埋められる |

正確なノード一覧は公式の[Permissions List](https://mvplugins.org/portals/reference/permissions-list/)を参照してください。

## 設定ファイル

| ファイル | 内容 |
|---|---|
| `config.yml` | 全体設定（利用権限の要否・選択ツールの種類など） |
| `portals.yml` | 各ポータルの定義。コマンドで管理されるため手動編集は最小限に |

## 注意点・トラブルシューティング

??? failure "プレイヤーがポータルを通れない"
    既定では各ポータルに `multiverse.portal.access.<ポータル名>` 権限が必要です。プレイヤー（または全員）にこの権限を付与するか、config で利用権限の要否を調整してください。

??? failure "ポータルが反応しない / 範囲がずれている"
    `/mvp info <名前>` で範囲を確認し、必要なら `/mvp select` →`/mvp modify loc` で再設定してください。`/mvp debug` で範囲を可視化できます。

??? failure "プラグインが起動しない"
    Multiverse-Core が必須です。Core を先に導入し、Core・アドオンとも 5系でバージョンを揃えてください。

## 公式リンク

- 公式ドキュメント： https://mvplugins.org
- コマンド解説： https://mvplugins.org/portals/fundamentals/commands-usage/
- 権限一覧： https://mvplugins.org/portals/reference/permissions-list/
