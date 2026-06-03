<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。使い方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# EmeraldBank ― OP・運営ガイド { .page-op #emeraldbank-op }

EmeraldBank の導入・config・権限・ランキング看板の設置・管理コマンド・トラブルシューティングをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | CasinoPlugin（銀行モジュール `bank`） |
| api-version | 26.1.2 |
| メインコマンド | `/emerald`（`/eb`）、`/ed`、`/ew` |
| 稼働形態 | 統合版 **CasinoPlugin に内包** された銀行モジュールとして稼働 |
| 共通設定 | `plugins/CasinoPlugin/config.yml`（モジュール有効化フラグ等） |
| 残高データ | `plugins/CasinoPlugin/accounts.yml` |

!!! info "CasinoPlugin への内包について"
    EmeraldBank は単体プラグインとしては存在せず、統合版 **CasinoPlugin** に組み込まれた銀行モジュール（`bank`）として動作します。サーバー経済の通貨残高を一元管理する基盤であり、他モジュール（Poker / Slot / BlackJack / Tintiro / HorseRacing / Lottery / Quiz / Kart）は内部 API `EmeraldAPI` を経由してこの口座データを参照します。

## 導入手順

1. ビルドした `CasinoPlugin-*.jar` をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `plugins/CasinoPlugin/config.yml` および `plugins/CasinoPlugin/accounts.yml` が自動生成される。
3. `config.yml` の `modules.bank.enabled` は **`true` 固定で運用**（他モジュールが依存するため）。
4. 通常はそのまま利用可能。残高データは `accounts.yml` の `accounts.<uuid>` に保存される。

!!! warning "残高データのバックアップ"
    プレイヤーの口座残高は `plugins/CasinoPlugin/accounts.yml` の `accounts` セクションに保存されます。サーバー経済の根幹データのため、定期的にバックアップを取ってください。

## config.yml 主要項目（銀行モジュール関連）

銀行モジュール固有の設定は CasinoPlugin 共通の `config.yml` で扱います。

| キー | 既定値 | 説明 |
|---|---|---|
| `modules.bank.enabled` | `true` | 銀行モジュールの有効/無効。他モジュールが依存するため通常は `true` 固定 |
| `server-account-uuid` | `00000000-0000-0000-0000-000000000000` | BlackJack / Tintiro / Poker などでベットの一時保管に使用するハウス口座 UUID |
| `accounts`（`accounts.yml`） | `{}` | プレイヤー UUID ごとの残高。プラグインが自動で読み書きするため原則手動編集不要 |

!!! note "他モジュールからのアクセス"
    口座データは `EmeraldAPI` 経由で公開されます（`getBalance` / `setBalance` / `transact`）。各ゲームモジュール（Poker / Slot / BlackJack / Tintiro / HorseRacing / Lottery / Quiz / Kart）はこのAPIを通して残高を読み書きします。

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `emerald.list` | 全員（`true`） | `/eb list` での全プレイヤー残高一覧表示 |
| `emerald.admin` | OP | 管理者用コマンド（`/eb blankbook`、`/eb blankwallet`、`/eb ranking create`）、ランキング看板の破壊 |

!!! note "`emerald.list` はデフォルト全員に開放"
    v2.0 以降、`emerald.list` は既定で全プレイヤーに付与されています。残高一覧を一般プレイヤーに見せたくない場合は、権限プラグイン側で `emerald.list` を `false` に設定してください。

## ランキング看板の設置手順

看板を右クリックすると残高ランキング（トップ10）が表示される **ランキング看板** を設置できます。

1. 任意の場所に **看板** を設置する。
2. その看板に **照準を合わせた状態**（5ブロック以内）で `/eb ranking create` を実行する（`emerald.admin` 権限が必要）。
3. 看板が `[EmeraldBank] ランキング 右クリックで 表示` の表示に変換される。
4. 以降、プレイヤーが看板を右クリックするとランキングがチャットに表示される。

!!! tip "看板の保護"
    ランキング看板は `emerald.admin` 権限を持つ者だけが破壊できます。一般プレイヤーが壊そうとするとブロックされます。管理者が破壊すると登録も解除されます。

!!! warning "ランキング看板の登録はメモリ上で保持されます"
    ランキング看板の位置データはサーバー稼働中のメモリ上で管理されます。サーバー再起動後はクリック判定が看板の文面（1〜2行目）から再認識される設計です。看板の文面を変更すると認識されなくなるため、文面は変更しないでください。

## ATM 看板の設置手順

`[EmeraldBank]` 看板を設置することで、コマンドを打たずに右クリックで銀行機能を呼び出せます。設置には `emerald.admin` 権限が必要です。

1. 看板を設置し、1行目に `[EmeraldBank]`、2行目に下表のいずれかを入力する。
2. 設置に成功すると2行目が緑色に変換される。
3. プレイヤーが右クリックすると指定の機能が実行される。

| 2行目 | 機能 | 備考 |
|---|---|---|
| `wallet` | エメラルドウォレットを発行 | 既に所持済みの場合は重複発行に注意 |
| `book` | エメラルド通帳を発行 | 同上 |
| `gui` | バンクGUIを開く | コマンド `/eb gui` 相当 |
| `money` | 自分の残高をチャットに表示 | コマンド `/eb money` 相当 |

!!! note "ATM 看板の設置権限"
    `[EmeraldBank]` 看板の設置には `emerald.admin` 権限が必要です。一般プレイヤーは設置できません。

## 管理コマンド

| コマンド | 権限 | 説明 |
|---|---|---|
| `/eb blankbook` | `emerald.admin` | 所有者未設定の「空の通帳」を発行。取引プラグイン等での配布・販売用 |
| `/eb blankwallet` | `emerald.admin` | 所有者未設定の「空のウォレット」を発行。取引プラグイン等での配布・販売用 |
| `/eb ranking create` | `emerald.admin` | 照準先の看板をランキング看板に変換 |

!!! note "空のアイテムの使い方"
    `/eb blankbook` / `/eb blankwallet` で発行した空アイテムは所有者が未設定です。取引プラグインなどでプレイヤーに配布し、受け取ったプレイヤーが **右クリックで所有者登録** すると、通常の通帳・ウォレットとして機能するようになります。

!!! warning "残高の直接操作コマンドはありません"
    プレイヤー残高を任意の値に直接 set / give / take する管理コマンドは EmeraldBank に存在しません。残高の調整が必要な場合は、外部API（`AccountRepository`）を利用するか、config.yml の `accounts` を慎重に編集してサーバーを再起動してください。

## トラブルシューティング

??? failure "ランキング看板が反応しない"
    看板の1行目が `[EmeraldBank]`、2行目が `ランキング` になっているか確認してください。これらの文面でランキング看板を判別しているため、文面を編集すると認識されなくなります。再設置する場合は新しい看板に `/eb ranking create` を実行してください。

??? failure "`/eb ranking create` で「看板に向けて実行してください」と出る"
    プレイヤーの視線の先5ブロック以内に看板（SIGN系ブロック）がない状態です。看板に近づき、しっかり照準を合わせてから実行してください。

??? failure "プレイヤーがエメラルドを預け入れできない"
    名前・Lore・エンチャント・カスタムモデルが付いた細工済みエメラルドがインベントリにあると預け入れが全面的にブロックされます。プレイヤーに通常のエメラルドのみを所持させて再実行してもらってください。

??? failure "残高がマイナスになっている"
    EmeraldBank はマイナス残高（借金）を許容する設計です。送金や外部プラグインの処理で残高がマイナスになることがあります。意図しないマイナスが多発する場合は、連携プラグイン側の処理を確認してください。

??? failure "他プラグインが残高を取得できない"
    連携プラグインは `EmeraldBank` プラグインインスタンスから `getAccountRepository()` を呼び出して `AccountRepository` を取得します。EmeraldBank（または内包元の CasinoPlugin）が正しくロードされ、依存関係・ロード順が適切かを確認してください。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← EmeraldBank 概要へ](index.md){ .md-button }
