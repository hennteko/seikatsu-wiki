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

`config.yml` の `settings` / `game` / `loadout` セクションで挙動を設定できます。

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 16 | 最大参加人数（`/zankipvp setmax <数>` で変更可） |
| `settings.min-players` | 2 | 最低開始人数（`/zankipvp setmin <数>` で変更可。0人開始のみ常に拒否） |
| `settings.default-lives` | 3 | プレイヤーの初期残機数（`/zankipvp setzanki <数>` で変更可） |
| `settings.respawn-delay` | 5 | リスポーン待機時間（秒） |
| `game.respawn-invincible-seconds` | 10 | リスポーン直後の無敵時間（秒）。この間、**すべてのダメージが無効化**される（耐性付与ではなく完全無敵・発光表示）。`/zankipvp setprotection <秒>` で変更可 |
| `game.result-seconds` | 10 | 試合終了後、結果発表を表示してからロビー復帰するまでの秒数 |

!!! info "リスポーンキット（loadout）は config で定義します"
    リスポーン・参加時に配られる装備は `loadout` セクションで定義します。`loadout.items.<スロット番号>`（0〜8 がホットバー）に `material`／`amount`／`name`／`enchantments`／`potion-effects` を、`loadout.offhand` にオフハンド装備を指定します。既定では木の剣（ノックバックI）・弓・特殊ポーション（スプラッシュ）・パン×64・矢×64・盾（オフハンド）が配られます。loadout が未定義／不正な場合はコード内蔵の既定キットにフォールバックします。

!!! note "locations / signs セクションについて"
    `config.yml` の `locations`（地点）と `signs`（看板）はコメントアウトされた雛形です。実際の地点はゲーム内の `/zankipvp setstartspawn`・`/zankipvp setlobby`・`/zankipvp setfield`・`/zankipvp setspawn` コマンド、看板は `/zankipvp setsign <join|leave|start>` コマンドで登録します（テキストはプラグインが自動書き込み）。手書きで編集する必要はありません。

!!! note "残機・無敵・人数の設定は config に保存されます"
    `/zankipvp setzanki <数字>`・`/zankipvp setprotection <秒>`・`/zankipvp setmax <数字>`・`/zankipvp setmin <数字>` で設定した値は **`config.yml` に即保存され、サーバー再起動後も維持** されます。`config.yml` の `settings.*` / `game.*` を直接編集して `/zankipvp reload` で反映することもできます。

!!! warning "既存サーバーは config が自動追記されません"
    本プラグインは `saveDefaultConfig()` のみのため、旧バージョンから更新した場合 `min-players` / `respawn-delay` / `game.respawn-invincible-seconds` / `game.result-seconds` / `loadout` 等の新キーは既存 `config.yml` に自動追記されません（コード側に既定値があるため動作はします）。値をファイルで変更したい場合は手動追記、または `/zankipvp setprotection` 等のコマンドで設定してください。

!!! success "旧 respawn-protection は自動移行されます"
    旧バージョンの `settings.respawn-protection` キーは、プラグイン起動時に自動で `game.respawn-invincible-seconds` へ移行され、旧キーは削除されます（移行はログに出力されます）。

## セットアップ手順

OP権限で、設定したい場所に **その場に立って** 以下のコマンドを実行します（実行位置が座標として保存されます）。

```text title="初期リスポーン地点（退出時の戻り先）"
/zankipvp setstartspawn
```

```text title="受付ロビー（参加時の待機場所）"
/zankipvp setlobby
```

```text title="ゲームエリアの角1"
/zankipvp setfield 1
```

```text title="ゲームエリアの角2"
/zankipvp setfield 2
```

```text title="赤チームのスポーン地点"
/zankipvp setspawn red
```

```text title="青チームのスポーン地点"
/zankipvp setspawn blue
```

```text title="黄チームのスポーン地点"
/zankipvp setspawn yellow
```

```text title="緑チームのスポーン地点"
/zankipvp setspawn green
```

設定状況は `/zankipvp status` で確認できます。

!!! tip "チームスポーンは4色すべて必須"
    `/zankipvp start` は、赤・青・黄・緑 **4チームすべてのスポーン地点** が設定されていないとエラーになり開始できません。少人数で運用する場合でも4色すべて設定してください。

### 看板の設置

ロビーには参加・退出・開始用の看板を設置します。看板を見ながら（5ブロック以内）以下のコマンドで登録します（テキストはプラグインが自動書き込みします）。`zankipvp.admin` 権限が必要です。**開始看板は `setstart` ではなく `setsign start` で登録します。**

```text title="参加看板として登録"
/zankipvp setsign join
```

```text title="退出看板として登録"
/zankipvp setsign leave
```

```text title="開始看板として登録"
/zankipvp setsign start
```

```text title="視線先の看板の登録を解除"
/zankipvp setsign delete
```

登録するとプラグインが `[ZankiPvP]` / `lobby` / `クリックで参加` 等のテキストを自動で書き込みます。手書き登録は廃止されています。

| 看板の種類 | 役割 |
|---|---|
| 参加看板 | クリックでゲームに参加。現在人数が表示される |
| 退出看板 | クリックでゲームから退出 |
| 開始看板 | クリックでゲーム開始（`/zankipvp setsign start` で登録） |

登録済みの看板を破壊するには `zankipvp.admin` 権限が必要で、破壊すると登録も解除されます。

## コマンド

### プレイヤー用（全員可・看板と同等／読み取り専用）

| コマンド | 説明 |
|---|---|
| `/zankipvp join [名前]` | ゲーム（受付ロビー）に参加する（参加看板と同等）。名前指定はOP限定 |
| `/zankipvp leave [名前]` | ゲームから退出する（退出看板と同等）。名前指定はOP限定 |
| `/zankipvp start` | ゲームを開始する（開始看板と同等・コマンドブロック対応） |
| `/zankipvp status` | 現在の地点・ゲーム設定の状況を表示する（読み取り専用） |
| `/zankipvp stats [名前]` | キル／デス／勝利／試合数の成績を表示する（読み取り専用） |

!!! note "join / leave / start / status / stats は全員が使えます"
    これらは権限不要で全プレイヤーが実行できます（`join`・`leave`・`start` は看板の右クリックと同じ動作、`status`・`stats` は読み取り専用）。`/zankipvp start` も誰でも実行できる点に注意してください（運営のみで開始したい場合は開始看板を設置せず、運営が管理する形で運用してください）。`join`・`leave` に **他プレイヤー名** を指定して操作するのは OP 限定です。

### 管理用（`zankipvp.admin`）

| コマンド | 説明 |
|---|---|
| `/zankipvp setspawn <red\|blue\|yellow\|green>` | チーム別スポーン地点を設定（リスポーン地点兼用） |
| `/zankipvp setlobby` | 受付ロビー地点を設定 |
| `/zankipvp setfield <1\|2>` | ゲームエリアの角を設定 |
| `/zankipvp setstartspawn` | 初期リスポーン地点（退出先）を設定 |
| `/zankipvp setsign <join\|leave\|start>` | 視線先の看板を参加／退出／開始看板として登録 |
| `/zankipvp setsign delete` | 視線先の看板の登録を解除 |
| `/zankipvp stop` | ゲームを強制終了する（コマンドブロック対応） |
| `/zankipvp setzanki <数字>` | 初期残機数を設定（1以上・config保存） |
| `/zankipvp setprotection <秒>` | リスポーン無敵時間を設定（0以上・config保存） |
| `/zankipvp setmax <数字>` | 最大参加人数を設定（2以上・config保存） |
| `/zankipvp setmin <数字>` | 最低開始人数を設定（1以上・config保存） |
| `/zankipvp reload` | config.yml を再読み込み（成績データも再読み込み） |

!!! note "ゲーム開始の条件"
    `/zankipvp start`（または開始看板）は、ロビーに **最低開始人数（既定2人・`settings.min-players`／`/zankipvp setmin` で変更可）以上** が参加し、かつ4チームのスポーン地点がすべて設定されている場合に開始できます。条件を満たさない場合はエラーメッセージが表示されます。

## 権限ノード

`plugin.yml` で定義されている権限は以下のとおりです。

| 権限 | 既定 | 用途 |
|---|---|---|
| `zankipvp.admin` | OP | 設定・管理コマンドの使用権限、看板の登録・破壊、他プレイヤーの join/leave 操作 |

!!! note "実装と権限の対応について"
    `join`・`leave`（自分自身）・`start`・`status`・`stats` は **権限不要で全プレイヤーが実行可能** です（`join`・`leave`・`start` は看板の右クリックと同等、`status`・`stats` は読み取り専用）。それ以外の設定・管理コマンド（`setspawn`・`setlobby`・`setfield`・`setstartspawn`・`setsign`（`delete` 含む）・`stop`・`setzanki`・`setprotection`・`setmax`・`setmin`・`reload`）と、`join`・`leave` への **他プレイヤー名指定** は `zankipvp.admin` が必要です。`stop` 等はコマンドブロック／コンソールからも実行できます。

## ゲームの運営

1. ロビーの参加看板からプレイヤーが集まるのを待つ（`/zankipvp status` で人数を確認）。
2. 最低開始人数（既定2人）以上集まったら `/zankipvp start` でゲームを開始する。
3. 生き残りチームが1つになると自動で勝敗判定・終了処理が行われる。
4. 異常時は `/zankipvp stop` で強制終了する（全員がロビーへ戻る）。

## トラブルシューティング

??? failure "`/zankipvp start` でゲームが始まらない"
    参加人数が最低開始人数（既定2人）未満、または赤・青・黄・緑いずれかのチームスポーンが未設定の可能性があります。`/zankipvp status` で参加人数とチームスポーンの設定状況を確認してください。

??? failure "プレイヤーが看板で参加できない"
    参加看板が `/zankipvp setsign join` で正しく登録されているか確認してください。登録済みの看板にはプラグインがテキストを自動書き込みします。また、ゲーム終了処理中（ENDING状態）は参加できません。

??? failure "看板を設置・破壊できない"
    `[ZankiPvP]` 看板の設置・破壊には `zankipvp.admin` 権限が必要です。一般プレイヤーには設置・破壊できません。

??? failure "残機数・無敵時間・参加人数を変えたい"
    `/zankipvp setzanki <数>`・`/zankipvp setprotection <秒>`・`/zankipvp setmax <数>`・`/zankipvp setmin <数>` で設定した値は **config.yml に即保存され、再起動後も維持** されます。`config.yml` の `settings.*` / `game.*` を直接編集した場合は `/zankipvp reload` で反映してください。

??? failure "リスポーン地点や設定が反映されない"
    `/zankipvp reload` で config.yml を再読み込みしてください。なお `setup` 系コマンドでの地点設定は即座に config.yml へ保存されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Zankipvp 概要へ](index.md){ .md-button }
