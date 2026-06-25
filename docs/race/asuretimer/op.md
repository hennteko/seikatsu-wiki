<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# AsureTimer ― OP・運営ガイド { .page-op #asuretimer-op }

AsureTimer の導入・config.yml・コース／看板のセットアップ・権限・管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | AsureTimer |
| バージョン | `${project.version}`（ビルド時に決定） |
| api-version | 26.1.2 |
| メインコマンド | `/asure` |
| 依存プラグイン | なし |
| データベース | SQLite（`plugins/AsureTimer/data.db`） |
| 設定ファイル | `plugins/AsureTimer/config.yml` |

!!! info "プラグインの概要"
    AsureTimer は単独のプラグインです。アスレチック（`asure`）コースとドロッパー（`dropper`）コースのタイム計測・ランキング機能を提供します。コースの開始・ゴール・ランキング表示は **看板** で行い、記録は SQLite データベースに保存されます。

## 導入手順

1. ビルドした AsureTimer の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/AsureTimer/` に `config.yml` が自動生成され、`data.db`（SQLite）が作成される。
3. アスレ／ドロッパー用のコースワールドを用意する。
4. 後述の手順でコースを登録し、スポーン地点・スタート地点・看板を設置する。
5. 設定を変更したら `/asure reload` で再読み込みする。

## config.yml 設定項目

### 全体設定

| キー | 既定値 | 説明 |
|---|---|---|
| `default-course` | `"1000m"` | コースIDを省略したとき（看板・コマンド）に使われる既定コース |
| `actionbar-interval` | 5 | 計測中アクションバーの更新間隔（tick／20=1秒） |
| `sign-update-interval` | 30 | ランキング看板の自動更新間隔（秒） |
| `ranking-display-count` | 10 | ランキング表示件数の設定値（※現状コードでは未使用。表示は常に10件固定） |

### コース設定（`courses`）

`courses.<コースID>` 以下に各コースを定義します。`spawn` / `start` 地点はコマンド（後述）で設定するのが簡単です。

| キー | 既定値 | 説明 |
|---|---|---|
| `courses.<ID>.type` | `asure` | コース種別。`asure`（アスレ）または `dropper`（ドロッパー） |
| `courses.<ID>.distance` | 1000 | アスレのゴール距離（m）。アクションバーの分母に使用。ドロッパーは `0` でよい |
| `courses.<ID>.spawn` | - | ロビー（スポーン）地点。クリア・ギブアップ後のテレポート先 |
| `courses.<ID>.start` | - | スタート地点。看板クリック時のテレポート先・距離計測の基準 |

`spawn` / `start` はそれぞれ `world` / `x` / `y` / `z` / `yaw` / `pitch` を持つ座標ブロックです。

### メッセージ設定（`messages`）

`messages` 以下でチャットメッセージを `&` カラーコード付きで編集できます。アスレ用（`start` / `giveup` / `clear` / `checkpoint` / `death-respawn` / `ranking-header` 等）とドロッパー用（`dropper-start` / `dropper-clear` / `dropper-giveup` / `dropper-ranking-header`）に加え、**戻るアイテム用（`return-teleport` / `return-no-checkpoint` / `return-menu-start` / `return-menu-entry`）** が用意されています。`{time}` `{distance}` `{rank}` `{player}` `{course}` `{index}` などのプレースホルダーが使えます。

### ギブアップアイテム設定（`giveup-item`）

| キー | 既定値 | 説明 |
|---|---|---|
| `giveup-item.material` | `NETHER_STAR` | ギブアップアイテムの素材（`Material` 名で指定。不正値は警告を出し `NETHER_STAR` にフォールバック） |
| `giveup-item.name` | `&c&lギブアップ` | 表示名（`&` カラーコード対応、イタリック装飾は自動でオフ） |
| `giveup-item.lore` | （2行） | アイテムの説明文（lore） |

!!! info "ギブアップアイテムの判定について"
    ギブアップ判定は **PersistentDataContainer（PDC）に専用タグが付いているか** で行われます。プラグインが配布したアイテムにだけ反応するため、同じ素材・同じ名前の通常アイテムを持っていても誤作動しません。`giveup-item.material` を別の素材（例: `BARRIER`）に変えても、配布されたアイテムが正しくギブアップアイテムとして機能します。素材変更後は `/asure reload` を実行してください（既存セッション中のアイテムは古い素材のままです）。

### 戻るアイテム設定（`return-item`）

アスレ計測中に配られる、通過済みチェックポイントへワープできるアイテムの設定です（**ドロッパーでは配られません**）。右クリックで通過済みCDの選択GUIが開き、選んだCPへワープします（消費されず、タイム・距離記録も減りません）。判定はギブアップ同様にPDCタグで行うため、同素材の通常アイテムには反応しません。

| キー | 既定値 | 説明 |
|---|---|---|
| `return-item.material` | `RECOVERY_COMPASS` | 戻るアイテムの素材（不正値は警告を出し `RECOVERY_COMPASS` にフォールバック） |
| `return-item.name` | `&b&l前のチェックポイントに戻る` | 表示名（`&` カラーコード対応） |
| `return-item.menu-title` | `&8チェックポイント選択` | 選択GUIのタイトル |
| `return-item.lore` | （2行） | アイテムの説明文（lore） |

!!! warning "既存サーバーは config が自動追記されません"
    本プラグインは `saveDefaultConfig()` のみのため、**旧バージョンから更新した場合 `return-item` ブロックは既存 `config.yml` に自動追記されません**。コード側に既定値（リカバリーコンパス・既定名/lore）があるため動作はしますが、素材やGUIタイトルをカスタマイズしたい場合は `return-item` ブロックを手動追記して `/asure reload` してください。

## コース・看板のセットアップ手順

OP権限を持った状態で、コース付近に立ってコマンドを実行します（実行位置が座標として保存されます）。

### 1. コースを登録する

```text title="コースを新規登録する"
/asure course create <コースID> <asure|dropper> [距離]
```

例: `/asure course create 1000m asure 1000`、`/asure course create dropper1 dropper`。距離を省略すると 1000 が使われます。

### 2. スポーン地点・スタート地点を設定する

設定したい位置に立って実行します。

```text title="ロビー（クリア・ギブアップ後の戻り先）を現在地に設定"
/asure setup spawn [コースID]
```

```text title="スタート地点を現在地に設定（距離計測の基準にもなる）"
/asure setup start [コースID]
```

コースIDを省略すると `default-course` が対象になります。

### 3. 看板を設置する

看板の **1行目** に種別、**2行目** に役割、**3行目** にコースID を書いて設置すると、AsureTimer の機能看板に変換されます。

| 1行目 | 2行目 | 3行目 | 4行目 | 役割 |
|---|---|---|---|---|
| `[asure]` | `start` | コースID | - | アスレのスタート看板 |
| `[asure]` | `goal` | コースID | - | アスレのゴール看板 |
| `[asure]` | `ranking` | コースID | `time` または `distance` | アスレのランキング看板 |
| `[dropper]` | `start` | コースID | - | ドロッパーのスタート看板 |
| `[dropper]` | `goal` | コースID | - | ドロッパーのゴール看板 |
| `[dropper]` | `ranking` | コースID | `time` | ドロッパーのランキング看板 |

!!! note "看板の仕様"
    - 3行目（コースID）を空欄にすると `default-course` が使われます。
    - ランキング看板の4行目は `time`（クリアタイム）か `distance`（到達距離）で、省略時は `time` です。
    - 看板の作成には `asuretimer.admin` 権限が必要です。
    - 設置に成功するとプラグインが看板の文面（`[AsureTimer]` `▶ START ▶` など）を自動整形します。
    - ランキング看板は `sign-update-interval`（既定30秒）ごとに自動更新され、裏面にベスト記録が表示されます。

## 管理コマンド

メインコマンドは `/asure` です。

| コマンド | 説明 |
|---|---|
| `/asure join [コースID]` | スタート看板クリックと同じ処理でコースに参加・計測開始（`asuretimer.play` 必要） |
| `/asure setup <spawn\|start> [コースID]` | 現在地をスポーン／スタート地点に設定 |
| `/asure course list` | 登録済みコースの一覧（種別・距離・設定状況）を表示 |
| `/asure course create <ID> <asure\|dropper> [距離]` | コースを新規作成 |
| `/asure course settype <ID> <asure\|dropper>` | コースの種別を変更 |
| `/asure course delete <ID>` | コースを削除 |
| `/asure ranking [コースID] [time\|distance]` | ランキングを表示 |
| `/asure reload` | config.yml・コース定義・ギブアップ／戻るアイテム素材を再読み込み |
| `/asure stats` | 自分の全コース統計を表示 |
| `/asure stats <プレイヤー>` | 指定プレイヤーの全コース統計を表示（他人指定は `asuretimer.admin` 必要） |
| `/asure stats <プレイヤー> <コースID>` | 指定プレイヤー・コースの統計を表示 |
| `/asure stats <コースID>` | 自分の指定コース統計を表示（コース名のみ指定の短縮形） |

!!! note "stats コマンドの表示内容"
    `stats` ではコースごとに以下が表示されます。

    - 種別アイコン（`[Asure]` / `[Dropper]`）／コースID
    - 挑戦回数・クリア数・クリア率（%）
    - ベストタイムとタイムランキング順位（記録がある場合）
    - 最大到達距離と距離ランキング順位（アスレのみ、記録がある場合）

    他プレイヤー名はオンライン・オフラインどちらでも検索できます。プラグインの記録テーブルに名前が残っていれば、過去にサーバーへ来たことがあるプレイヤーの統計も参照可能です（大文字小文字は区別しません）。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `asuretimer.admin` | OP | コマンド全般・看板の作成・管理 |
| `asuretimer.play` | 全員 | スタート看板からアスレ／ドロッパーをプレイする |

!!! note "サブコマンドごとの権限ガード"
    `/asure` コマンド自体には plugin.yml で `permission` が指定されていないため、コマンド自体は誰でも叩けます。サブコマンドごとに以下のチェックが入ります。

    - `setup` / `reload` / `course` … `asuretimer.admin` を実装側でチェック
    - `stats <他プレイヤー>` … `asuretimer.admin` を実装側でチェック
    - `join` … `asuretimer.play` を実装側でチェック（既定全員可）
    - `stats`（自分） / `stats <コースID>`（自分） … 権限チェックなし（誰でも実行可）
    - `ranking` … 権限チェックなし（誰でも実行可）

    そのため一般プレイヤーは `/asure ranking` や `/asure stats` を実行できます。アクションバー / 看板 / GUI と併用する運用で問題ありません。

## トラブルシューティング

??? failure "看板をクリックしても計測が始まらない"
    看板が AsureTimer の看板として認識されているか確認してください（設置時に `[AsureTimer]` 等へ自動整形されます）。また、看板3行目のコースIDが `courses` に登録され、`start` 地点が設定済みである必要があります。`/asure course list` で `[設定済]` になっているか確認してください。

??? failure "「コースが設定されていません」と表示される"
    対象コースの **スタート地点が未設定** です。スタート地点に立って `/asure setup start <コースID>` を実行してください。スポーン地点も `/asure setup spawn <コースID>` で設定しておくと、クリア・ギブアップ後に正しくロビーへ戻れます。

??? failure "コースが作成・登録できない"
    `/asure course create <ID> <asure|dropper> [距離]` の形式を確認してください。種別は `asure` か `dropper` のみ、距離は数値で指定します。実行には `asuretimer.admin` 権限が必要です。

??? failure "ランキング看板が更新されない / 1位が表示されない"
    ランキング看板はチャンクが読み込まれている範囲のみ `sign-update-interval`（既定30秒）ごとに更新されます。記録が1件も無いコースでは `---` が表示されます。看板1行目が `[Ranking]` / `[DropperRank]` に整形されているか確認してください。

??? failure "ギブアップアイテムが効かない"
    ギブアップ判定はアイテムの PersistentDataContainer タグで行われます。プラグインが配布したアイテムのみ反応するため、自分でクリエイティブ等で出した同名アイテムは機能しません。スタート看板から再度コースを開始すれば、新しい正規のギブアップアイテムが配布されます。`giveup-item.material` を変更した直後は `/asure reload` を実行し、その後コースを再スタートしてください。

??? failure "チェックポイントが機能しない（アスレ）"
    チェックポイントは **金の感圧板（軽量用感圧板 / LIGHT_WEIGHTED_PRESSURE_PLATE）** を踏むことで記録されます。コース上に金の感圧板を設置してください。なお距離は `start` 地点からの **Z方向の進行** で計算されるため、コースはZ方向に伸びるよう設計すると距離表示が正しく機能します。

??? failure "設定変更が反映されない"
    `config.yml` を編集したら `/asure reload` を実行してください。コース定義・座標・メッセージが再読み込みされます。看板自体の文面は再設置するまで変わりません。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← AsureTimer 概要へ](index.md){ .md-button }
