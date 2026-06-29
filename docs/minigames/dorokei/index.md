# DorokeiGame <span class="badge dev">v3.1 / コマンド統合・簡略化版</span>

泥棒（市民）と警察（警官）に分かれて鬼ごっこを楽しむ **ドロケイ（どろけい）ミニゲーム**。市民は制限時間まで逃げ切れば勝ち、警官は全市民を捕まえて牢獄に送り込めば勝ちです。捕まった仲間は他の市民が助け出すこともできます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜20人（ロビー既定上限20人）</span></div>
  <div class="quick-card"><span class="label">1試合の長さ</span><span class="value">既定8分（480秒）＋準備30秒</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! success "最新アップデート概要（ラッキーチェスト＆逃走者支援アイテム）"
    逃走者（市民）を支援する **ラッキーチェスト** が追加されました。マップ上に設置されたチェストを右クリックすると、ランダムで強力な支援アイテムが手に入ります（警官・収監中は利用不可、チェストごとに再開封クールダウンあり）。あわせて **逃走者支援アイテム5種**（スピードポーション／跳躍のお守り／蜘蛛の巣トラップ／鈍足の罠／レーダージャマー）と、チェストを登録する **`/dorokei addchest` コマンド**（旧 `setchest` も後方互換で受理）、`config.yml` の **`chest-settings`（出現重み・クールダウン）** が実装されています。

!!! info "実装済みの機能"
    ロビー参加・看板連携・警官の自動割り振り・逃走準備・追跡演出・捕獲／救出・**ラッキーチェスト＆逃走者支援アイテム**・成績記録・ログアウト時の位置復帰・自動ロビー再参加までが実装済みです。会場は1サーバー1会場（単一会場）方式で、`/dorokei setlobby` / `setfield` / `setjail` で設定が完結します（ゲーム名の指定は不要）。**`join` / `leave` / `start` はコマンド化され全員が利用できます**。看板の登録は `setsign join` / `leave` / `start` / `delete` に統一され、数値設定コマンド（`settime` / `setmax` / `setmin` / `setcop`）と成績表示（`status` / `stats`）が利用できます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、ルール、勝利条件、参加方法、特殊アイテム、コマンド。
- **OP・運営向け** … 導入手順、ゲーム（ロビー＋エリア）の設定、config.yml、権限、管理コマンド。
