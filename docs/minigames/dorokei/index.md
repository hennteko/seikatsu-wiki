# DorokeiGame <span class="badge dev">v3.1 / コマンド統合・簡略化版</span>

泥棒（市民）と警察（警官）に分かれて鬼ごっこを楽しむ **ドロケイ（どろけい）ミニゲーム**。市民は制限時間まで逃げ切れば勝ち、警官は全市民を捕まえて牢獄に送り込めば勝ちです。捕まった仲間は他の市民が助け出すこともできます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜20人（ロビー既定上限20人）</span></div>
  <div class="quick-card"><span class="label">1試合の長さ</span><span class="value">既定8分（480秒）＋準備30秒</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! success "最新アップデート概要（単一会場化）"
    会場を「ゲーム名」で複数管理する方式から、**1サーバー1会場（単一会場）方式に簡略化** されました。`/dorokei setlobby` / `setfield 1` / `setfield 2` / `setjail` を順番に実行するだけで設定が完結し、**ゲーム名の指定は不要** です。あわせて **牢屋の複数登録**（`setjail` / `setjail clear`）、**人数別の警官数設定**（`setcop`）、**設定状況の確認**（`status`）が追加されました。

!!! info "実装済みの機能"
    ロビー参加・看板連携・警官の自動割り振り・逃走準備・追跡演出・捕獲／救出・ログアウト時の位置復帰・自動ロビー再参加までが実装済みです。旧コマンド（`start` / `setpolice` / `settime`）は廃止され、`gamestart` / `setcop` 等の新方式に統一されています。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、ルール、勝利条件、参加方法、特殊アイテム、コマンド。
- **OP・運営向け** … 導入手順、ゲーム（ロビー＋エリア）の設定、config.yml、権限、管理コマンド。
