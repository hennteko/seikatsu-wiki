# DorokeiGame <span class="badge dev">v3.1 / コマンド統合・簡略化版</span>

泥棒（市民）と警察（警官）に分かれて鬼ごっこを楽しむ **ドロケイ（どろけい）ミニゲーム**。市民は制限時間まで逃げ切れば勝ち、警官は全市民を捕まえて牢獄に送り込めば勝ちです。捕まった仲間は他の市民が助け出すこともできます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜20人（ロビー既定上限20人）</span></div>
  <div class="quick-card"><span class="label">1試合の長さ</span><span class="value">既定8分（480秒）＋準備30秒</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! success "v3.1 アップデート概要"
    v3.1 では **セットアップコマンドが大幅に簡略化** されました。これまでロビー（lobby）とエリア（area）は別IDで管理し `setup link` で紐付ける必要がありましたが、v3.1 からは **「ゲーム名」1つに統合** され、`/dorokei setlobby <ゲーム名>` / `/dorokei setfield <ゲーム名> 1` / `/dorokei setfield <ゲーム名> 2` / `/dorokei setjail <ゲーム名>` を順番に実行するだけで設定が完結します（`setup link` は廃止）。`/dorokei delete <ゲーム名>` でゲーム単位の削除も可能になりました。

!!! info "実装済みの機能"
    ロビー参加・看板連携・警官の自動割り振り・逃走準備・追跡演出・捕獲／救出・ログアウト時の位置復帰・自動ロビー再参加までが実装済みです。旧方式（`start` / `setpolice` / `settime` / `setjail`）のコマンドも後方互換として残っていますが、運用ではロビー方式を推奨します。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、ルール、勝利条件、参加方法、特殊アイテム、コマンド。
- **OP・運営向け** … 導入手順、ゲーム（ロビー＋エリア）の設定、config.yml、権限、管理コマンド。
