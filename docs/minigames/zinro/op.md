<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Zinro ― OP・運営ガイド { .page-op #zinro-op }

Zinro（Minecraft人狼プラグイン）の導入・初期設定・config・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Zinro |
| バージョン | 1.4.0 |
| api-version | 26.1.2 |
| メインコマンド | `/zinro`（エイリアス `/zr`） |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Zinro/config.yml` |

!!! note "対応環境"
    Paper 1.26.x（api-version `26.1.2`）で動作します。プラグインが赤色で読み込まれない場合は、サーバーのJavaバージョンを確認してください（Paper 26.1 系は Java 25 が必要です）。

## 導入手順

1. ビルドした `Zinro` のJARファイルをサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/Zinro/config.yml` が自動生成される。
3. 後述の初期設定（位置設定・占い看板・ゲート看板）を行う。
4. 設定を変更したら `/zinro save` で保存し、`/zinro reload` で再読み込みする。

## 初期設定

### 1. 位置の設定

OP権限で、設定したい場所に **立った状態** で以下を実行します（実行位置が座標として保存されます）。

```text title="初期スポーン地点（途中抜け時などの戻り先）"
/zinro setstartspawn
```

```text title="ロビー（ゲーム開始前の待機場所）"
/zinro setlobby
```

```text title="ゲームフィールド（開始時のテレポート先）"
/zinro setspawn
```

### 2. フィールド範囲の設定（任意）

スケルトン（エメラルドの番人）をフィールド範囲内にランダム出現させたい場合に設定します。

```text title="フィールド範囲の角1"
/zinro setfield 1
```

```text title="フィールド範囲の角2"
/zinro setfield 2
```

!!! tip "スケルトン出現範囲について"
    `setfield` で `pos1`・`pos2` を設定すると、その範囲内にスケルトンがランダム出現します。未設定の場合は `config.yml` の `skeleton.spawn` 座標を中心に、`skeleton.spawn_radius` の半径内へ出現します。

### 3. 占い看板の設置

占いアイテムを使うための看板を、参加人数分（最大20個）設置します。設置したい看板を見ながら実行します。

```text title="占い看板を番号1で登録"
/zinro setboard 1
```

```text title="占い看板を番号2で登録"
/zinro setboard 2
```

（以下、3〜19も同様に実行）

```text title="占い看板を番号20で登録"
/zinro setboard 20
```

看板番号は1〜20まで。例えば10人プレイなら看板を10個設置します。ゲーム開始時に各看板へプレイヤー名が自動で割り当てられます。

### 4. ゲート看板の作成

プレイヤーが右クリックで参加・退出できる看板を設置します。看板を見ながら以下のコマンドで登録します（テキストはプラグインが自動書き込みします）。

```text title="参加看板として登録（テキストは自動書き込み）"
/zinro setsign join
```

```text title="退出看板として登録"
/zinro setsign leave
```

```text title="ショップ看板として登録（任意）"
/zinro setsign shop
```

登録するとプラグインが `[人狼]` / `▶クリックで参加`（参加看板）や `[人狼]` / `▶クリックで退出`（退出看板）などのテキストを自動で書き込みます。手書きによる自動登録は廃止されています。

### 5. 設定の保存

```text title="設定を保存する"
/zinro save
```

## config.yml 主要項目

```yaml
time:
  day: 60
  night: 60

skeleton:
  health: 4.0
  spawn_radius: 50
  spawn:
    world: world
    x: 0.0
    y: 64.0
    z: 0.0

game:
  lobby_return_time: 10

role_distribution:
  # 人数ごとの配役
```

| キー | 既定値 | 説明 |
|---|---|---|
| `time.day` | 60 | 昼時間（秒） |
| `time.night` | 60 | 夜時間（秒） |
| `skeleton.health` | 4.0 | スケルトン（エメラルドの番人）の体力 |
| `skeleton.spawn_radius` | 50 | スケルトン出現半径（フィールド範囲未設定時に使用） |
| `skeleton.spawn` | world (0,64,0) | スケルトン出現の中心座標 |
| `game.lobby_return_time` | 10 | ゲーム終了後にロビーへ戻るまでの待ち時間（秒） |
| `locations.spawn / lobby / field` | （未設定） | 各位置座標。`/zinro setstartspawn`・`/zinro setlobby`・`/zinro setspawn` で自動保存 |
| `field_area.pos1 / pos2` | （未設定） | フィールド範囲。`/zinro setfield 1`・`/zinro setfield 2` で自動保存 |
| `boards.board_1 〜 board_20` | （未設定） | 占い看板の座標。`/zinro setboard` で自動保存 |
| `role_distribution.<人数>` | 2〜20人分を同梱 | 人数ごとの役職配分 |

### 配役設定（`role_distribution`）

プレイヤー人数（2〜20）ごとに各役職の人数を指定します。役職キーは `werewolf`（人狼）／`villager`（村人）／`vampire`（吸血鬼）／`lunatic`（狂人）／`affiliate`（狼付き）／`cupid`（キューピット）です。

```yaml
role_distribution:
  10:
    werewolf: 2
    villager: 5
    vampire: 1
    lunatic: 1
    affiliate: 1
```

!!! warning "配役の合計人数に注意"
    各役職の合計人数がプレイヤー人数と一致する必要があります。一致しない場合はゲーム開始時にエラーになります。人狼を増やしたいときは村人を減らすなど、合計が合うように調整してください。

!!! note "ショップ価格について"
    ショップアイテムの価格はプラグイン内部で固定されています（例: 人狼の斧 3E、占いの心 6E など）。同梱の `config.yml` には価格を変更する項目は含まれていません。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `zinro.admin` | OP | 管理・セットアップコマンドすべて |

!!! note "プレイヤー向けの権限"
    参加・退出はゲート看板で行うため、一般プレイヤーはコマンド権限なしで遊べます。`/zinro shop` は `zinro.admin` 権限が必要です。一般プレイヤーはショップ看板またはショップの杖からショップを開きます。

## 管理コマンド

すべて `zinro.admin` 権限が必要です。`/zinro shop` も `zinro.admin` 権限が必要で、一般プレイヤーはショップ看板またはショップの杖から開きます。

| コマンド | 説明 |
|---|---|
| `/zinro` | コマンドのヘルプを表示 |
| `/zinro start` | ゲームを開始する（最低2人の参加が必要） |
| `/zinro stop` | ゲームを強制終了する |
| `/zinro setstartspawn` | 初期スポーン地点を現在地に設定 |
| `/zinro setlobby` | ロビー地点を現在地に設定 |
| `/zinro setspawn` | ゲームスポーン地点を現在地に設定 |
| `/zinro setfield <1\|2>` | フィールド範囲を現在地に設定 |
| `/zinro setboard <1-20>` | 見ている看板を占い看板として登録 |
| `/zinro setsign <join\|leave\|shop>` | 視線先の看板をゲート看板として登録 |
| `/zinro leave <player>` | 指定プレイヤーをロビーから強制離脱させる |
| `/zinro save` | 設定を保存する |
| `/zinro reload` | 設定を再読み込みする |

## 運営の流れ

1. プレイヤーがゲート看板からロビーに参加するのを待つ（最低2人）。
2. `/zinro start` でゲームを開始する。役職割り当て・看板へのプレイヤー名表示・フィールドへのテレポートが自動で行われます。
3. 昼夜サイクルが自動進行します。勝敗が決まるとゲームが終了し、約10秒後に全員がロビーへ戻ります。
4. 異常時は `/zinro stop` で強制終了できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    最低2人がロビーに参加しているか、`spawn`・`lobby`・`field` の位置が設定済みか、プレイヤー数分の占い看板が設置済みかを確認してください。`/zinro setboard <1-20>` で看板を追加できます。

??? failure "スケルトン（エメラルドの番人）が出現しない"
    `/zinro setfield` でフィールド範囲を設定するか、`config.yml` の `skeleton.spawn` 座標を設定してください。スケルトンは夜の時間にのみ、10秒ごとに出現します。

??? failure "占いアイテムが使えない"
    占い看板が `/zinro setboard` で人数分設置されているか確認してください。占いの心・騎士の加護・霊媒の遺灰は、設置済みの占い看板に向かって右クリックして使用します。

??? failure "プラグインが赤色（読み込み失敗）で表示される"
    サーバーのJavaバージョンが古い可能性があります。Zinroが要求するバージョンに合わせてJavaを更新してください。

??? failure "設定変更が反映されない"
    `config.yml` を直接編集した場合は `/zinro reload` を実行してください。コマンドで設定を変更した場合は `/zinro save` で保存してからリロードします。

## 実装・ドキュメントに関する注意

!!! warning "同梱ドキュメントとコマンド体系の差異"
    Zinroには複数の同梱ドキュメント（README.md・SETUP_GUIDE.md・QUICKSTART.md・各種PHASEレポートなど）が含まれていますが、内容が古く現行バージョンと食い違う箇所があります。**このWIKIは現行コード（v1.4.0）の実装を正としています。** 特に次の点に注意してください。

    - `/zinro join`・`/zinro leave`（自己参加）・`/zinro vote`・`/zinro config`・`/zinro setup ...` といったコマンドは、現行コードには **存在しません**。参加・退出はゲート看板、投票は投票用紙GUIで行います。位置設定は `/zinro setstartspawn`・`/zinro setlobby`・`/zinro setspawn`・`/zinro setfield` です。
    - 一部ドキュメントに「死神（REAPER）削除済み」とありますが、現行の役職定義には未使用の死神が残っています。実際の配役には登場しないため通常プレイへの影響はありません。
    - 価格変更コマンドや `shop_prices` 設定は現行の同梱 `config.yml` には含まれません。ショップ価格は固定です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Zinro 概要へ](index.md){ .md-button }
