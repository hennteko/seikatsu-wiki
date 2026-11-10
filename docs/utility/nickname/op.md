<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Nickname ― OP・運営ガイド { .page-op #nickname-op }

あだ名（チャット・Tab・頭の上）の導入、config、表示の仕組み、権限、管理コマンドをまとめます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Nickname |
| バージョン | `${project.version}`（ビルド時に決定） |
| メインコマンド | `/nick`（別名 `/nickname`・`/あだ名`） |
| api-version | 26.1.2 |
| softdepend | `CasinoPlugin`（E 残高。有料の変更に使用） |
| 設定ファイル | `plugins/Nickname/config.yml` |
| データ | `plugins/Nickname/nicknames.yml` |
| 権限ノード | `nickname.use`（既定全員）・`nickname.color`（全員）・`nickname.free`（OP）・`nickname.admin`（OP） |

!!! note "ミニゲームではなくユーティリティ"
    Nickname はミニゲームではなく、サーバー全体の **あだ名（表示名）** を扱うユーティリティです。有料の変更の支払いは [CasinoPlugin](../../casino/casino-plugin/index.md) の **E 残高** から引き落とします（CasinoPlugin が無い・止まっている間は有料の変更だけできません）。

## 導入手順

1. `Nickname.jar` を **ミニゲームサーバー** の `plugins` に入れて起動します。
2. 有料の変更には **CasinoPlugin**（E 残高）が必要です。CasinoPlugin が止まっている間は、有料の変更ができません（無料の変更・reset はできます）。
3. 統合版で頭の上のあだ名が見えるか確認します（下の「統合版」参照）。

## 表示の仕組み

| 場所 | 仕組み |
|---|---|
| チャット | 表示名をあだ名にする（Java 版はカーソルで ID を表示、クリックで `/msg ID` を入力欄に入れる） |
| Tab リスト | Tab の名前をあだ名にする |
| 頭の上（`display`） | プレイヤーごとのチーム（`nk_...`）で **ID の名札を隠し**、あだ名の文字（TextDisplay）を頭の上に追従させる。本人には見せない |
| 頭の上（`prefix`） | チームの prefix にあだ名を入れる（`あだ名 ID` の形） |

- 頭の上の文字はプレイヤーに乗せていません（乗せると他のプラグインのテレポートが失敗するため）。毎 tick 頭の上へ動かしています。
- しゃがむと、バニラの名札と同じく薄くなり、壁越しに見えなくなります。
- 透明化・スペクテイター・死亡中は表示しません。そのプレイヤーが見えない人（かくれんぼ等で隠されている）にも見せません。
- 頭の上の文字はワールドに保存されません（再起動で残らない）。

!!! warning "ミニゲーム中"
    minigame-common の参加中フラグ（`mattari:active_game`）がある間は、ゲーム側が名前を管理するので、Nickname は何もしません（頭の上の文字も消します）。ゲームを抜けたら付け直します。

    Nickname はプレイヤーの PDC `mattari:nickname` にあだ名を書いており、**minigame-common（2026-11 版）を入れたゲーム** はこれを読んで、チャット・Tab・参加告知を「【ゲーム名】あだ名」、実況（キル等）を色なしのあだ名で表示します。頭上の名札はチームの仕組み上 ID のままです。対応済み（2026-11-07）: BedWars・PvPバトル・残機制PVP・雪合戦・スプラ（PvP / サーモンラン）・DragonRTA・MOBA・色鬼・かくれんぼ・ドロケイ・雪鬼・青鬼・シノビ鬼・DBD・人狼RPG・Overcook・PowerWash・Supermarket。人狼RPG は名前登録看板・役職公開などゲーム内の名前もあだ名になります（Discord の連携ボタンは本人が分かるよう ID）。

    **ロビー共通部品（lobby-inventory・2026-11 版）を使うゲーム** も同じく `mattari:nickname` を読み、参加告知・ゲーム内のお知らせ（勝者・脱落など）をあだ名で表示します（2026-11-07 対応）: PVP(1vs1)・TNTRAN・WindPvP・スナイパーバトロワ・バトルロアイヤル・一本柱PvP・異能バトル・雪クラフト・SPIKE・攻城戦・アモアス・人狼・弓人狼・SCRAP・鳥人間コンテスト・アスレタイマー・エリトラレース・マリカー・UNO・どうぶつ積みバトル・オセロ・ゴッドフィールド・ショットガンルーレット・ステップダンス・ナンプレ・ネコマス・ババ抜き・ブロックフォール・マインスイーパー・ライアーズテーブル・伝言しりとり・大富豪・椅子取りゲーム・釣りイベント・2D宝探し・ボンバーマン。管理用の表示（ログ・ランキング保存・OP 向けのヒント）は ID のままです。

    参加中フラグ `mattari:active_game` は、ロビー共通部品のゲームもロビーに入った時点で付けるようになりました。これで「別系統のゲームに同時に参加してアイテムが消える」ことを防ぎ、Nickname も頭の上の文字を消します。上のどちらにも入っていないゲームは、ゲーム中は ID のままです。

    **カジノ（CasinoPlugin）** も対応済み（2026-11-07）: 参加・退出の告知、ポーカー・チンチロ・ブラックジャックの実況、ポーカーのスコアボードがあだ名になります。カジノのロビーに入っている間も参加中フラグが付くので、頭の上はほかのゲームと同じく「【ゲーム名】ID」になります。

!!! note "統合版"
    統合版では、頭の上の文字（TextDisplay）の見え方が Java 版と違う・見えない場合があります。実機で確認して、見えない場合は `nametag.mode: prefix`（ID の前にあだ名）に切り替えてください。チャット・Tab は統合版でも同じです。

## config.yml

起動時に足りない項目を自動で追記します（既存の値は変えません。追記したときは `config.yml.bak` を保存）。

| キー | 既定 | 内容 |
|---|---|---|
| `nickname.min-length` / `max-length` | 1 / 10 | 文字数（色コードは数えない） |
| `nickname.allow-color` | true | 色を使えるか（`nickname.color` 権限も必要） |
| `nickname.allowed-codes` | `0123456789abcdeflmnor` | 使えるカラーコード（`k` は読めないので外している） |
| `nickname.deny-other-player-ids` | true | 他の人の ID と同じあだ名を禁止（あだ名同士の重複は OK） |
| `nickname.ng-words` | [] | 使えない言葉（含まれていたら設定できない） |
| `cost.free-changes` | 1 | 無料で変えられる回数 |
| `cost.price` | 500 | 2回目以降の費用（E） |
| `nametag.mode` | display | `display` / `prefix` / `none` |
| `nametag.format` | `{nick}` | 頭の上の文字（`{id}` も使える。2行なら `{nick}\n&7{id}`） |
| `nametag.height` | 0.3 | 頭のてっぺんからの高さ |
| `nametag.background` | true | 半透明の背景 |
| `nametag.view-range` | 0.5 | 見える距離の倍率（1.0 = 約64ブロック） |
| `chat.format` / `tab-format` | `{nick}` | チャット・Tab の名前 |
| `chat.show-id-on-hover` | true | チャットの名前にカーソルで ID を表示（Java 版） |

書き換えたら、以下で反映します。

```text title="config を再読み込み（頭の上の表示も付け直す）"
/nick reload
```

## 管理コマンド

| コマンド | 内容 |
|---|---|
| `/nick set <プレイヤー> <あだ名>` | あだ名を付ける（無料・回数に数えない。オンラインのプレイヤー） |
| `/nick clear <プレイヤー>` | あだ名を外す（本人に通知） |
| `/nick reload` | config を再読み込み（頭の上の表示も付け直す） |

## プレイヤー用コマンド

| コマンド | 内容 |
|---|---|
| `/nick <あだ名>` | あだ名を変える（有料のときは確認してから） |
| `/nick confirm` / `/nick cancel` | 有料の変更を確定／やめる |
| `/nick reset` | あだ名を外して ID に戻す（無料） |
| `/nick info` | 今のあだ名と、次の変更の費用 |
| `/nick list` | オンラインの人のあだ名と ID（全員可） |

## 権限ノード

| 権限 | 既定 | 用途 |
|---|---|---|
| `nickname.use` | 全員 | 自分のあだ名（`/nick <あだ名>`・reset・info・list） |
| `nickname.color` | 全員 | あだ名に色を使う |
| `nickname.free` | op | 変更に E がかからない |
| `nickname.admin` | op | set / clear / reload |

## データ

`plugins/Nickname/nicknames.yml` に、プレイヤーごとのあだ名（色コード付き）と変更回数を保存します（30秒ごと＋停止時）。

## トラブルシューティング

??? failure "頭の上に ID とあだ名の両方が出る"
    ほかのプラグインが自分のスコアボードを使っている人には、ID を隠すチームが効かないことがあります（1秒ごとに付け直しますが、その人が別のチームに入っている場合は触りません）。気になる場合は `nametag.mode: prefix` にしてください。

??? failure "頭の上のあだ名が見えない"
    統合版の場合は「統合版」の説明を見てください。Java 版で見えない場合は、`nametag.view-range` を大きくしてください。

??? failure "有料の変更ができない"
    CasinoPlugin が起動しているか確認してください。E が足りない場合は「E が足りません」と表示されます。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Nickname 概要へ](index.md){ .md-button }
