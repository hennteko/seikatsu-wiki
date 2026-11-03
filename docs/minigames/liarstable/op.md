<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# ライアーズテーブル（LiarsTable） ― OP・運営ガイド { .page-op #liarstable-op }

ライアーズテーブルの導入・アリーナ（席・テーブル・ゾギー・モード）と看板の設定・管理ツール・config・権限・管理コマンドをまとめます。地点・看板はコマンドで登録すると即保存され、アリーナは `arenas.yml` に保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | LiarsTable |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/liarstable`（エイリアス `/lt`） |
| api-version | 26.1.2 |
| softdepend | `floodgate` / `Geyser-Spigot`（統合版対応） |
| 設定ファイル | `plugins/LiarsTable/config.yml`（数値・地点・看板） |
| アリーナファイル | `plugins/LiarsTable/arenas.yml`（席・テーブル・ゾギー・モード） |
| 権限ノード | `liarstable.admin`（既定OP） |

!!! info "設定ファイルの自動更新"
    プラグインを更新すると、起動時に `config.yml` の **足りない項目だけ** が最新版へ追記されます（設定済みの値は引き継ぎ、旧ファイルは `config.yml.bak-日時` に退避）。設定の変更は「次の試合から」反映されます（`/lt reload` で再読み込み）。

## 導入手順

1. `LiarsTable-<版>.jar` をサーバーの `plugins/` フォルダに置いてサーバーを起動する。
2. 起動すると `config.yml` が自動生成される（数値は初期値で動作）。
3. 下の「セットアップ手順」でロビー・アリーナ・看板を設定する。
4. `/lt status` で設定状況を確認する。

!!! note "試合に必須の設定"
    1つのアリーナを開始可能にするには、**ロビー・アリーナの席（2〜4）・テーブル中央・ゾギーの位置・モード** が必要です。初期スポーン（離脱時の戻り先）と参加/開始看板も設定します。

## セットアップ手順（コマンド）

OP権限（`liarstable.admin`）で実行します。地点・席・テーブル・ゾギーは **立った位置（向きも使用）** を、看板系は看板を **見ながら**（5ブロック以内）実行します。管理メニューのボタンでも同じ設定ができます。

### 1. 地点

```text title="ロビー（その場に立って実行）"
/lt setlobby
```

```text title="初期スポーン（離脱・切断時の戻り先／その場に立って実行）"
/lt setstartspawn
```

### 2. アリーナ（1アリーナ＝1テーブル）

```text title="アリーナを作成（名前に「.」や空白は不可）"
/lt create <アリーナ>
```

```text title="席を追加（各席に立ち、テーブル中央を向いて実行。2〜4回）"
/lt setseat <アリーナ>
```

```text title="テーブル中央（上面の高さ・中央に立って実行。カードと情報表示の位置）"
/lt settable <アリーナ>
```

```text title="ゾギーの位置（置きたい位置に立ち、テーブルの方を向いて実行）"
/lt setzoggy <アリーナ>
```

```text title="モードを切り替え（現在はデッキのみ）"
/lt setmode <アリーナ> deck
```

```text title="席を削除（後ろの席の番号が詰まる）"
/lt delseat <アリーナ> <番号>
```

```text title="アリーナを削除（開始看板の登録も解除）"
/lt delete <アリーナ> confirm
```

!!! note "ゾギーの配置"
    ゾギーに使うモブ（既定 RAVAGER）は約2×2ブロックあるので、テーブルから少し離して配置してください。向きもそのまま使います。

### 3. 看板（看板を見ながら実行）

```text title="参加看板を登録"
/lt setsign join
```

```text title="離脱看板を登録"
/lt setsign leave
```

```text title="開始看板を登録（アリーナごと・複数可）"
/lt setsign start <アリーナ>
```

```text title="視線先の看板の登録を解除"
/lt setsign delete
```

## 管理ツール・管理メニュー（コマンドを打たない設定）

`/lt wand` で管理ツール（ブレイズロッド）を受け取り、**右クリック（統合版はタップ/長押し）** で管理メニューを開きます。`/lt menu` でも開けます。どちらも `liarstable.admin` 権限が必要です。

```text title="管理ツール（ブレイズロッド）を受け取る"
/lt wand
```

```text title="管理メニュー（GUI）を開く"
/lt menu
```

- **数値** … ＋/−ボタンで最低開始人数・手番の制限時間・歯選びの制限時間・ゾギーの歯の本数・脱落までの噛まれ回数・開始カウントダウン・結果の表示時間・ロビーの定員を変更（即保存）。
- **地点** … ボタンを押すと立ち位置に設定（ロビー・初期スポーン、アリーナ画面で席の追加・テーブル中央・ゾギーの位置）。
- **看板** … 看板を見ながら（5ブロック以内）押すと参加／離脱／開始看板を登録、ハサミで登録解除。
- **運用** … 開始・設定状況・リロード・強制停止（強制停止は5秒以内に2回押し）。
- **アリーナ画面** … 席の全消去・アリーナの削除も5秒以内に2回押し。新しいアリーナの作成はコマンド（`/lt create`）で行います。

!!! note "管理ツールの注意"
    ライアーズテーブルに参加中は受け取れません（離脱してから実行してください）。捨てると消えるので、再度 `/lt wand` で取得します。統合版はチェストGUIで操作でき、数値は＋/−の別ボタンで増減します。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/lt setstartspawn` | 初期スポーン（離脱・切断時の戻り先）を設定 |
| `/lt setlobby` | ロビー地点を設定 |
| `/lt create <アリーナ>` | アリーナを作成 |
| `/lt delete <アリーナ> confirm` | アリーナを削除（開始看板の登録も解除） |
| `/lt setseat <アリーナ>` | 席を追加（立った位置と向き・2〜4） |
| `/lt delseat <アリーナ> <番号>` | 席を削除 |
| `/lt settable <アリーナ>` | テーブル中央を設定 |
| `/lt setzoggy <アリーナ>` | ゾギーの位置を設定 |
| `/lt setmode <アリーナ> deck` | モードを設定（現在はデッキのみ） |
| `/lt setsign <join\|leave\|start <アリーナ>\|delete>` | 看板を登録／解除 |
| `/lt wand` | 管理ツール（ブレイズロッド）を受け取る |
| `/lt menu` | 管理メニュー（GUI）を開く |
| `/lt stop [アリーナ]` | 試合を強制停止（勝敗なし・在籍者をロビーへ。試合中のアリーナが1つなら省略可） |
| `/lt reload` | 設定ファイル（config.yml・arenas.yml）の再読み込み |
| `/lt lobbyfix <プレイヤー> [force]` | 持ち物の手動復元（`force` で退避データ破棄・最終手段） |
| `/lt status` | 設定状況・現在の状況を確認（全員可） |

## プレイヤー用コマンド

| コマンド | 説明 |
|---|---|
| `/lt join` | ロビーに参加（参加看板と同等） |
| `/lt leave` | ロビーから離脱（離脱看板と同等。試合中は脱落扱い） |
| `/lt start [アリーナ]` | 試合を開始（開始看板と同等・コンソール/コマンドブロックも可） |
| `/lt hand` | 手札の画面を開き直す |
| `/lt status` | 設定・状況を確認（読み取り専用） |

## config.yml 設定項目

コマンドで登録する地点・看板（`default-spawn`・`lobby-spawn`・`signs`）は自動保存され、アリーナは `arenas.yml` に保存されます。先頭の `config-version` は設定ファイルの版で、**手で変えないでください**。以下は数値などの設定項目です。

### 人数・時間

| キー | 既定値 | 説明 |
|---|---|---|
| `min-players` | 2 | 開始に必要な人数（2〜4） |
| `max-lobby-players` | 0 | ロビーの定員（0＝登録済みアリーナの席数の合計×2） |
| `countdown-seconds` | 10 | 開始前のカウントダウン（秒） |
| `result-seconds` | 8 | 試合終了後、結果を表示する秒数 |
| `turn-time-limit-seconds` | 30 | 手番の制限時間（秒。時間切れはランダムに1枚出す） |
| `tooth-time-limit-seconds` | 15 | 歯を選ぶ制限時間（秒。時間切れはランダムな歯） |
| `reveal-delay-ticks` | 40 | めくってから結果を出すまでの間（20＝1秒） |
| `round-gap-ticks` | 40 | 罰のあと次のラウンドを配るまでの間（20＝1秒） |

### ゾギー（罰・全モード共通）

| キー | 既定値 | 説明 |
|---|---|---|
| `bites-to-eliminate` | 2 | 何回噛まれたら脱落か（1〜3） |
| `zoggy.teeth` | 6 | 歯の本数（2〜12）。うち1本がハズレ |
| `zoggy.shared` | true | true＝テーブル全員で1つの口を共有 / false＝1人1つの口 |
| `zoggy.entity-type` | RAVAGER | ゾギーに使うモブ（生き物ならどれでも可） |
| `zoggy.name` | ゾギー | ネームタグの名前 |

### デッキモード（`deck`）・カードの見た目（`items`）

| キー | 既定値 | 説明 |
|---|---|---|
| `deck.hand-size` | 5 | 配る枚数 |
| `deck.queen` / `king` / `ace` | 6 / 6 / 6 | Q・K・A の枚数 |
| `deck.joker` | 2 | ジョーカー（何にでもなる）の枚数 |
| `deck.max-play` | 3 | 1回に出せる最大枚数（1〜3） |
| `items.queen` / `king` / `ace` / `joker` / `back` | PINK_TULIP / GOLDEN_HELMET / DIAMOND / CREEPER_HEAD / PAPER | 各カード・伏せカードの見た目アイテム |

### 卓・環境・表示

| キー | 既定値 | 説明 |
|---|---|---|
| `table.seat-y-offset` | 0.3 | 着席用アーマースタンドの高さ補正 |
| `keep-food` | true | 在籍中は満腹度を減らさない |
| `protect-lobby` | true | 在籍中はダメージを受けない |
| `messages.prefix` | `&8[&cライアーズテーブル&8] &r` | メッセージの接頭辞 |
| `debug` | false | デバッグログを出す |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `liarstable.admin` | OP | セットアップ・看板・管理ツール・管理メニュー・`create`・`delete`・`stop`・`reload`・`lobbyfix` など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/lt join`・`/lt leave`・`/lt start`・`/lt hand`・`/lt status` は権限チェックがなく、全プレイヤーが使えます。`start` はコンソール・コマンドブロックからも実行できます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/lt status` を確認してください。アリーナに席（2〜4）・テーブル中央・ゾギー・モードがすべて設定され、ロビーに2人以上いる必要があります。席に入れなかった人はロビーで次の試合を待ちます。

??? failure "ロビーに入る前の持ち物が戻らない"
    対象がオンラインで参加していないときに `/lt lobbyfix <プレイヤー>` で復旧できます。どうしても戻らない場合のみ `force` を付けると退避データを破棄します（最終手段）。

---

## 全ゲーム共通の改修

全ミニゲーム共通の改修が組み込まれており、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ライアーズテーブル】名前」になり、離脱で元に戻ります（`name-tag.display-name` / `name-tag.color`）。
- **参加/離脱の全体告知** … 参加時にサーバー全体へ通知します（`name-tag.announce-join` で切替）。
- **退避データのファイル保存（LobbyInventory）** … ロビー入場時に退避した所持品を保存し、**サーバークラッシュ後の再ログインでも復元** します。異常時はOPが `/lt lobbyfix <プレイヤー> [force]` で復旧できます。
- **config の自動追記（ConfigUpdater）** … 起動時に不足項目を既定値＋コメント付きで自動追記します（既存の値は変更せず、更新時は旧ファイルを `config.yml.bak-日時` に退避）。
- **統合版対応（BedrockCompat）** … Floodgate / Geyser-Spigot 経由の統合版プレイヤーに対応します。空中タップ（腕振り）でも「手札を見る」・管理ツールを使え、登録看板は蝋引きして編集画面が開かないようにします。
- **観戦（GhostSpectator）** … 脱落者は生存者の視点を観戦します（スニークで対象切り替え）。統合版は観戦方式を自動で切り替えます（`bedrock.spectator-style`）。

### ロビー・名前表示・統合版の config

| キー | 既定値 | 説明 |
|---|---|---|
| `lobby-inventory.enabled` | true | ロビー入退室時の持ち物退避／復元（true 推奨） |
| `lobby-inventory.gamemode` | ADVENTURE | ロビー滞在中のゲームモード（ADVENTURE/SURVIVAL/CREATIVE/KEEP） |
| `lobby-inventory.clear-effects` | true | ロビー入場時にポーション効果を消す |
| `lobby-inventory.reset-health` | true | ロビー入場時に体力を既定へ戻す |
| `lobby-inventory.reset-exp` | true | ロビー入場時に経験値・レベルを0に |
| `lobby-inventory.expire-days` | 30 | 未復元の退避データの保持日数 |
| `name-tag.enabled` | true | 参加中の名前表示を付ける |
| `name-tag.display-name` | ライアーズテーブル | 「【】」に入れる正式名 |
| `name-tag.color` | RED | ゲーム名の色 |
| `name-tag.announce-join` | true | サーバー全体への参加通知 |
| `bedrock.wax-signs` | true | 登録看板を蝋引き（統合版でタップ時に編集画面を開かせない） |
| `bedrock.swing-to-use` | true | 統合版は空中タップ（腕振り）でも「手札を見る」・管理ツールを使える |
| `bedrock.spectator-style` | AUTO | 観戦方式（AUTO＝統合版だけゴースト／GHOST＝全員ゴースト／VANILLA＝全員観戦モード） |
| `bedrock.name-prefix` | `.` | Floodgate 未導入時に統合版と見なす名前の接頭辞 |

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← ライアーズテーブル（LiarsTable） 概要へ](index.md){ .md-button }
