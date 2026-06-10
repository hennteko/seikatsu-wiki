<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# KartRace ― OP・運営ガイド { .page-op #kartrace-op }

KartRace の導入・コース設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | KartRace |
| バージョン | 1.0.0 |
| api-version | 26.1.2 |
| メインコマンド | `/kartrace`（別名 `/kr` `/kart` `/race`）、`/kartadmin`（別名 `/kra`） |
| 依存プラグイン | EmeraldBank（必須）／ WorldGuard（任意・softdepend） |
| 稼働形態 | 生活鯖では統合版 **CasinoPlugin に内包** されたカートレースモジュールとして稼働 |
| 設定ファイル | `plugins/KartRace/config.yml` |

!!! info "CasinoPlugin 版について"
    単体プラグインとしての KartRace と、生活鯖で実稼働している CasinoPlugin 内蔵モジュールは同系統ですが、コマンド名・設定ファイルの場所・一部挙動が異なる場合があります。本ページは単体版 KartRace のソースを基準に記載しています。CasinoPlugin 側の固有設定は CasinoPlugin のドキュメントも併せて確認してください。

## 導入手順

1. EmeraldBank を先に `plugins/` フォルダへ導入する（KartRace は EmeraldBank に依存します）。
2. ビルドした `KartRace-1.0.0.jar` を `plugins/` フォルダに配置する。
3. サーバーを起動すると `plugins/KartRace/config.yml` が自動生成される。
4. 後述の手順でコースを設定する。
5. `/kartadmin enable` でロビーを有効化する。
6. 設定を変更したら `/kartadmin reload` で再読み込みする。

!!! warning "EmeraldBank が必須です"
    EmeraldBank が見つからない場合、KartRace は起動時に自動で無効化されます。レース報酬・ショップ・強化はすべて EmeraldBank の通貨（エメラルド）を使用します。

## config.yml 主要項目

### ロビー設定（`lobby`）

| キー | 既定値 | 説明 |
|---|---|---|
| `min_players` | 2 | レース開始に必要な最小人数 |
| `max_players` | 8 | ロビーの最大人数 |
| `countdown_seconds` | 30 | 最小人数到達後の待機カウントダウン（秒） |
| `start_countdown_seconds` | 5 | スタート直前のカウントダウン（秒） |
| `spawn` | - | ロビースポーン位置（`/kartadmin setlobby` で設定） |

### コース設定（`course`）

| キー | 既定値 | 説明 |
|---|---|---|
| `name` | メインコース | コース名（看板に表示） |
| `world` | world | コースのワールド名 |
| `laps` | 3 | 周回数 |
| `time_limit` | 300 | 制限時間（秒） |
| `start_positions` | - | スタート位置（最大8人分・`/kartadmin setstart` で設定） |
| `finish_line` | - | ゴールライン領域（`/kartadmin setfinish` で設定） |
| `checkpoints` | `[]` | チェックポイント一覧 |
| `item_boxes` | `[]` | アイテムボックス位置一覧 |

### 報酬設定（`rewards`）

| キー | 既定値 | 説明 |
|---|---|---|
| `base_per_player` | 50 | 参加者1人あたりの賞金プール追加額 |
| `finish_bonus` | 10 | 完走ボーナス |
| `distribution` | 40/25/15/10/5/3/2/0 | 1〜8位への賞金配分（%） |
| `bonuses.perfect_lap` | 50 | ノーミスラップのボーナス |
| `bonuses.first_blood` | 30 | 最初にアイテムをヒットさせたボーナス |
| `bonuses.comeback` | 100 | 最下位から1位への逆転ボーナス |

### 強化・ガレージ・アイテム・物理

- `upgrade` … 最大レベル（`max_level: 100`）と保護アイテム（safety_scroll / lucky_charm / guaranteed_scroll / destruction_shield）のコストを設定。
- `garage` … 初期スロット数（`initial_slots: 2`）、最大スロット数（`max_slots: 10`）、スロット拡張コストを設定。
- `items` … アイテムの有効化（`enabled`）、アイテムボックス復活時間（`box_respawn_time: 30`秒）、各アイテムの出現重み（`weights`）を設定。
- `physics` … カートの基本最高速度・加速度・摩擦・旋回率・ブースト回復速度を設定。
- `messages` … プレフィックスやレース開始・強化成功／失敗などのメッセージ文面を設定。

!!! warning "config の構造に注意"
    内部処理（RaceManager）の一部は `race.laps` / `race.time_limit` / `race.start_positions` といったキーを参照します。一方で初期 `config.yml` は `course.*` 配下にこれらを定義しています。コース座標の設定は `/kartadmin` コマンド経由で行うのが確実です。CasinoPlugin 版では設定構造が異なる可能性があるため、実機での挙動確認を推奨します。

!!! note "設定変更後は必ず `/kartadmin reload`"
    `config.yml` を編集したら `/kartadmin reload` を実行してください。

## コース・看板の作成手順

### コースのセットアップ

専用ワールドにコースを用意し、OP権限で **その場に立って** 以下のコマンドを実行します（実行位置が座標として保存されます）。

```text title="ロビースポーン地点"
/kartadmin setlobby
```

```text title="スタート位置 #1（1〜8まで繰り返す）"
/kartadmin setstart 1
```

```text title="チェックポイント #1（番号順に設定）"
/kartadmin setcheckpoint 1 [幅] [高さ]
```

```text title="ゴールライン"
/kartadmin setfinish [幅] [高さ]
```

```text title="アイテムボックスを追加"
/kartadmin setitembox
```

- `setcheckpoint` / `setfinish` の幅・高さは省略可能（既定: 幅5・高さ3ブロック）。
- 設定したスタート位置・ゴール・ロビーは `/kartadmin showmarkers` でパーティクル表示して確認できます。
- スタート位置は最大8人分（1〜8）まで設定できます。

!!! tip "チェックポイントは番号順に通過させる"
    チェックポイントはショートカット防止のため、プレイヤーが番号順に通過する必要があります。コースの要所に順番どおり配置してください。

### 看板の作成

看板は **参加／退出／ガレージ／ショップ** の4種別に分かれています。設置済みの看板を見ながら（6ブロック以内）以下のコマンドで種別を指定して登録します。

```text title="看板を登録（種別を指定）"
/kartadmin setsign <join|leave|garage|shop>
```

- `join` … クリックでロビーに参加する看板
- `leave` … クリックでロビーから退出する看板
- `garage` … クリックでガレージGUIを開く看板（`kartrace.garage` 権限が必要）
- `shop` … クリックでショップGUIを開く看板（`kartrace.shop` 権限が必要）

手書き設置も可能です。看板の **1行目に `[KartRace]`**、**2行目に種別**（`join` / `leave` / `garage` / `shop`、未指定は `join` 扱い）を入力すると自動で登録されます。手書き登録には `kartrace.sign.create` 権限が必要です（既定: OP）。

- 参加看板にはコース名・参加人数・状態（待機中／カウントダウン中／レース中など）が自動表示されます。
- 参加と退出はトグルではなく別々の看板です。それぞれ設置してください。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `kartrace.play` | 全員 | レースに参加できる |
| `kartrace.garage` | 全員 | ガレージを開ける |
| `kartrace.shop` | 全員 | ショップを開ける |
| `kartrace.admin` | OP | 管理コマンド（`/kartadmin`）を使用できる。`forcestart` / `forcestop` などすべてこの権限で判定 |
| `kartrace.sign.create` | OP | レース看板を作成できる |

## 管理コマンド

`/kartadmin`（別名 `/kra`）は `kartrace.admin` 権限が必要です。

| コマンド | 説明 |
|---|---|
| `/kartadmin reload` | config.yml を再読み込み |
| `/kartadmin enable` | ロビーを有効化する |
| `/kartadmin disable` | ロビーを無効化する |
| `/kartadmin setlobby` | ロビースポーン位置を設定 |
| `/kartadmin setstart <番号>` | スタート位置（1〜8）を設定 |
| `/kartadmin clearstart` | スタート位置をクリア |
| `/kartadmin setcheckpoint <番号> [幅] [高さ]` | チェックポイントを設定 |
| `/kartadmin setfinish [幅] [高さ]` | ゴールラインを設定 |
| `/kartadmin setitembox` | アイテムボックスを追加 |
| `/kartadmin showmarkers` | 設定済みの位置をパーティクルで表示 |
| `/kartadmin forcestart` | レースを強制開始（参加者1人以上で可） |
| `/kartadmin forcestop` | レースを強制終了 |
| `/kartadmin give <player> <kart_id>` | 指定プレイヤーにカートを付与 |
| `/kartadmin setlevel <player> <強化タイプ> <レベル>` | 強化レベルを設定（0〜100） |
| `/kartadmin setsign <join\|leave\|garage\|shop>` | 視線先の看板を指定種別の看板として登録 |

!!! note "強化タイプ"
    `/kartadmin setlevel` の強化タイプは SPEED / ACCELERATION / HANDLING / BOOST_POWER / BOOST_DURATION / BOOST_CHARGE / WEIGHT / GRIP の8種類です。

## WorldGuard 連携

WorldGuard は `softdepend`（任意の依存プラグイン）として宣言されており、導入されていなくても KartRace は動作します。

!!! warning "現状の連携範囲"
    設計上はコース境界（逸脱検知）に WorldGuard リージョンを利用する想定がありますが、現在のソースには WorldGuard を直接利用する処理は含まれていません。コース境界の扱いはプラグイン内部のリージョン判定が基本です。CasinoPlugin 版での連携状況は別途確認してください。

## トラブルシューティング

??? failure "プラグインが起動しない／自動で無効化される"
    EmeraldBank が `plugins/` に導入されているか確認してください。EmeraldBank が見つからない場合、KartRace は起動時に自動で無効化されます。

??? failure "プレイヤーがレースに参加できない"
    `/kartadmin enable` でロビーが有効化されているか確認してください。無効化状態（DISABLED）では参加できません。また `[KartRace]` 看板が正しく登録されているか、`/kartadmin setlobby` でロビー座標が設定済みかも確認してください。

??? failure "周回がカウントされない／ゴール判定されない"
    チェックポイントが番号順に設定されているか、ゴールラインが `/kartadmin setfinish` で設定済みかを確認してください。チェックポイント未設定の場合は周回判定が行われません。

??? failure "スタート位置が足りない"
    参加人数分のスタート位置（最大8）を `/kartadmin setstart <番号>` で設定してください。スタート位置が不足すると、その分のプレイヤーはコマンド実行位置などにスポーンします。

??? failure "報酬が支払われない"
    報酬は EmeraldBank の通貨で支払われます。EmeraldBank が正常に動作しているか、`rewards` の配分設定が0になっていないかを確認してください。完走（タイムが記録された）プレイヤーのみが順位配分の対象です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← KartRace 概要へ](index.md){ .md-button }
