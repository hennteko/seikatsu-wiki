<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Zankipvp ― OP・運営ガイド { .page-op #zankipvp-op }

Zankipvp の導入・地点セットアップ・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Zankipvp |
| 説明 | 残機制チームPvPミニゲームプラグイン |
| api-version | 26.1.2 |
| メインコマンド | `/zankipvp`（エイリアス `/zpvp`・`/zanki`） |
| 依存プラグイン | なし（単独プラグイン） |
| 設定ファイル | `plugins/Zankipvp/config.yml` |

## 導入手順

1. ビルドした `Zankipvp-x.x.x.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Zankipvp/config.yml` が自動生成される。
3. 後述の「セットアップ手順」でゲーム地点と看板を設定する。
4. 設定を変更したら `/zankipvp reload` で再読み込みする。

## config.yml 設定項目

`config.yml` の `settings` セクションで主要な数値を設定できます。

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 16 | 最大参加人数 |
| `settings.default-lives` | 3 | プレイヤーの初期残機数 |
| `settings.respawn-delay` | 5 | リスポーン待機時間（秒） |
| `settings.respawn-protection` | 10 | リスポーン保護時間（秒）。この間、耐性IIが付与される |

!!! note "locations / signs セクションについて"
    `config.yml` の `locations`（地点）と `signs`（看板）はコメントアウトされた雛形です。実際の地点はゲーム内の `/zankipvp setup` コマンド、看板は `[ZankiPvP]` 看板の設置時に **自動で書き込まれます**。手書きで編集する必要はありません。

!!! warning "残機数の設定とリロードについて"
    `/zankipvp zanki <数字>` で設定した初期残機数はメモリ上にのみ保持され、`config.yml` には保存されません。サーバー再起動時は `config.yml` の `default-lives` が初期値として使われます。常に同じ残機数で運用したい場合は `config.yml` の `settings.default-lives` を直接編集してください。

## セットアップ手順

OP権限で、設定したい場所に **その場に立って** 以下のコマンドを実行します（実行位置が座標として保存されます）。

```text
/zankipvp setup exit                       # 初期リスポーン地点（退出時の戻り先）
/zankipvp setup lobby                      # 受付ロビー（参加時の待機場所）
/zankipvp setup area pos1                  # ゲームエリアの角1
/zankipvp setup area pos2                  # ゲームエリアの角2
/zankipvp setup spawn red                  # 赤チームのスポーン地点
/zankipvp setup spawn blue                 # 青チームのスポーン地点
/zankipvp setup spawn yellow               # 黄チームのスポーン地点
/zankipvp setup spawn green                # 緑チームのスポーン地点
```

設定状況は `/zankipvp status` で確認できます。

!!! tip "チームスポーンは4色すべて必須"
    `/zankipvp start` は、赤・青・黄・緑 **4チームすべてのスポーン地点** が設定されていないとエラーになり開始できません。少人数で運用する場合でも4色すべて設定してください。

### 看板の設置

ロビーには参加・退出用の看板を設置します。看板の **1行目に `[ZankiPvP]`**、**2行目に種類** を書いて設置すると自動登録されます（`zankipvp.admin` 権限が必要）。

| 看板の種類 | 1行目 | 2行目 | 役割 |
|---|---|---|---|
| 参加看板 | `[ZankiPvP]` | `lobby` | クリックでゲームに参加。現在人数が表示される |
| 退出看板 | `[ZankiPvP]` | `leave` | クリックでゲームから退出 |

登録済みの看板を破壊するには `zankipvp.admin` 権限が必要で、破壊すると登録も解除されます。

## 管理コマンド

すべて `zankipvp.admin` 権限が必要です（`start`・`stop` はコマンドブロック・コンソールからも実行可能）。

| コマンド | 説明 |
|---|---|
| `/zankipvp setup spawn <red\|blue\|yellow\|green>` | チーム別スポーン地点を設定 |
| `/zankipvp setup lobby` | 受付ロビー地点を設定 |
| `/zankipvp setup area pos1` | ゲームエリアの角1を設定 |
| `/zankipvp setup area pos2` | ゲームエリアの角2を設定 |
| `/zankipvp setup exit` | 初期リスポーン地点（退出先）を設定 |
| `/zankipvp start` | ゲームを開始する（コマンドブロック対応） |
| `/zankipvp stop` | ゲームを強制終了する（コマンドブロック対応） |
| `/zankipvp zanki <数字>` | 初期残機数を設定（1以上） |
| `/zankipvp maxplayers <数字>` | 最大参加人数を設定（2以上） |
| `/zankipvp status` | 現在の地点・ゲーム設定の状況を表示 |
| `/zankipvp reload` | config.yml を再読み込み |

!!! note "ゲーム開始の条件"
    `/zankipvp start` は、ロビーに **2人以上** が参加し、かつ4チームのスポーン地点がすべて設定されている場合に開始できます。条件を満たさない場合はエラーメッセージが表示されます。

## 権限ノード

`plugin.yml` で定義されている権限は以下のとおりです。

| 権限 | 既定 | 用途 |
|---|---|---|
| `zankipvp.user` | 全員（true） | 基本的な使用権限 |
| `zankipvp.admin` | OP | ゲーム管理・セットアップコマンドの使用権限、看板の設置・破壊 |
| `zankipvp.all` | false | `zankipvp.user` + `zankipvp.admin` をまとめた親権限 |

!!! warning "実装と権限の対応について"
    実際のコマンド処理で権限チェックされるのは `zankipvp.admin` のみです。`setup`・`start`・`stop`・`zanki`・`maxplayers`・`status`・`reload` のすべてが `zankipvp.admin` を要求します。一般プレイヤー専用のコマンドは無いため、`zankipvp.user` は実装コード上では参照されていません（参加・退出は看板で行います）。

## ゲームの運営

1. ロビーの参加看板からプレイヤーが集まるのを待つ（`/zankipvp status` で人数を確認）。
2. 2人以上集まったら `/zankipvp start` でゲームを開始する。
3. 生き残りチームが1つになると自動で勝敗判定・終了処理が行われる。
4. 異常時は `/zankipvp stop` で強制終了する（全員がロビーへ戻る）。

## トラブルシューティング

??? failure "`/zankipvp start` でゲームが始まらない"
    参加人数が2人未満、または赤・青・黄・緑いずれかのチームスポーンが未設定の可能性があります。`/zankipvp status` で参加人数とチームスポーンの設定状況を確認してください。

??? failure "プレイヤーが看板で参加できない"
    参加看板の1行目が `[ZankiPvP]`、2行目が `lobby` になっているか確認してください。看板設置時に「参加看板を設置しました」と表示されていれば正しく登録されています。また、ゲーム終了処理中（ENDING状態）は参加できません。

??? failure "看板を設置・破壊できない"
    `[ZankiPvP]` 看板の設置・破壊には `zankipvp.admin` 権限が必要です。一般プレイヤーには設置・破壊できません。

??? failure "残機数を変えたのに再起動で元に戻る"
    `/zankipvp zanki` で設定した残機数は保存されません。恒久的に変更したい場合は `config.yml` の `settings.default-lives` を編集し、`/zankipvp reload` してからサーバーを再起動してください。

??? failure "リスポーン地点や設定が反映されない"
    `/zankipvp reload` で config.yml を再読み込みしてください。なお `setup` 系コマンドでの地点設定は即座に config.yml へ保存されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Zankipvp 概要へ](index.md){ .md-button }
