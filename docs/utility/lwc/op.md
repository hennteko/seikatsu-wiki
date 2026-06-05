<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。使い方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# LWC（チェスト保護） ― OP・運営ガイド { .page-op #lwc-op }

LWC（LWCX）の導入・`core.yml`・管理コマンド・権限・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | LWCX（LWC Extended）。jar 名は `LWC-2.4.2.jar` |
| バージョン | 2.4.2（MC 1.20.6〜1.21.x 対応） |
| 種別 | 公開プラグイン（pop4959 によるメンテナンス版） |
| メインコマンド | `/lwc`（`/cprivate` 等は短縮エイリアス） |
| 依存 | なし（Vault 導入時はグループ指定 `g:` が利用可能） |
| 設定ファイル | `plugins/LWC/core.yml` ほか |
| データ保存 | 既定は SQLite（`plugins/LWC/lwc.db`）。`core.yml` で MySQL に変更可 |
| 公式Wiki | https://github.com/pop4959/LWCX/wiki |

!!! warning "オリジナル LWC との違い"
    オリジナルの LWC（Hidendra 版）は開発終了しています。LWCX はその後継メンテナンス版で、コマンド体系は同じですが、新ブロック対応・フラグ追加（hopperin / hopperout 等）が行われています。古い解説サイトとは差分がある点に注意してください。

## 導入手順

1. [SpigotMC](https://www.spigotmc.org/resources/lwc-extended.69551/) または GitHub Releases から LWCX の jar を入手する。
2. `LWC-2.4.2.jar` を `plugins/` フォルダに配置する。
3. サーバーを再起動すると `plugins/LWC/` に設定ファイル群と `lwc.db` が自動生成される。
4. 既定でチェスト等は **設置時に自動保護（private）** される。挙動を変える場合は `core.yml` を編集し `/lwc admin reload` する。

## core.yml の主な設定

保護対象ブロックや自動保護の挙動は `core.yml` の `protections` セクションで制御します。

```yaml
protections:
  blocks:
    chest:
      enabled: true
      autoRegister: private   # 設置時に自動で private 保護を作成
    furnace:
      enabled: true
    trapped_chest:
      enabled: true
      autoRegister: private
```

| 設定 | 説明 |
|---|---|
| `enabled` | そのブロックを保護対象にするか |
| `autoRegister` | 設置時の自動保護タイプ（`private` / `public` / `off`） |
| `ignoreBlockDestruction` | true にすると保護されていても破壊可能になる（中身アクセスのみ保護） |
| `ignoreExplosions` | 爆発からの保護を無効化するか |

!!! tip "ワールド単位の制御"
    `core.yml` 末尾の方にはワールドごとの上書き設定も書けます。ミニゲーム用ワールドで保護を無効にしたい場合などに利用できます。保護数の上限は `lwc.protect` 系権限と `limits` 設定（または LWC の limits プラグイン機構）で制御します。

## 管理コマンド（/lwc admin）

| コマンド | 説明 |
|---|---|
| `/lwc admin view <保護ID>` | 保護されたインベントリを遠隔で閲覧 |
| `/lwc admin find <プレイヤー>` | プレイヤーが作成した保護を一覧表示 |
| `/lwc admin forceowner <プレイヤー>` | 保護の所有者を変更（実行後ブロックをクリック） |
| `/lwc admin remove <保護ID>` | 保護IDを指定して削除 |
| `/lwc admin purge <プレイヤー>` | プレイヤーの **全保護を削除**（引退者の整理に） |
| `/lwc admin cleanup` | データベースのクリーンアップ |
| `/lwc admin reload` | 設定の再読み込み |
| `/lwc admin version` | バージョン確認 |
| `/lwc admin report` | パフォーマンスレポート表示 |
| `/lwc history <プレイヤー>` | 保護の作成・削除履歴を検索 |

!!! danger "purge / clear 系は破壊的操作"
    `/lwc admin purge` はそのプレイヤーの保護を全削除、`/lwc admin clear` は **サーバー上の全保護を削除** します。実行前にサーバーフォルダごとバックアップを取ってください。

## 権限ノード

LuckPerms（[→ LuckPerms ページ](../luckperms.md)）で配布します。

| ノード | 説明 |
|---|---|
| `lwc.protect` | **既定でプレイヤー全員に付与**。自分の保護の作成・削除・フラグ設定・共有など基本機能一式 |
| `lwc.mod` | 任意の保護を **開ける**（削除はできない）。モデレーター向け |
| `lwc.admin` | `/lwc admin` を含む完全な管理権限。**全保護の削除すら可能** なので付与は最小限に |
| `lwc.deny` | 保護可能ブロックへの一切のアクセスを禁止 |
| `lwc.shownotices` | 保護付きブロックを開いた際に「誰の保護か」の通知を表示。運営向け |
| `lwc.admin.<サブコマンド>` | `/lwc admin` の特定サブコマンドのみ許可（例: `lwc.admin.cleanup`） |

## トラブルシューティング

| 症状 | 対処 |
|---|---|
| 自動保護が効かない | `core.yml` の該当ブロックの `autoRegister` を確認。プレイヤーが `/cnolock` モードになっていないかも確認 |
| 特定ブロックを保護対象にしたい / 外したい | `core.yml` の `protections.blocks` に追記・編集して `/lwc admin reload` |
| 引退プレイヤーの保護を整理したい | `/lwc admin find <名前>` で確認後、`/lwc admin purge <名前>` |
| ホッパー式の装置が動かないと報告された | 該当チェストに `/chopper on`（または `hopperin` / `hopperout` フラグ）を設定してもらう |
| 保護の所有者を移したい | `/lwc admin forceowner <新所有者>` → 対象ブロックをクリック |
