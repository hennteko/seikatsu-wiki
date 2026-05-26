# DorokeiGame <span class="badge dev">開発中 / Phase 1-2</span>

泥棒（市民）と警察（警官）に分かれて鬼ごっこを楽しむ **ドロケイ（どろけい）ミニゲーム**。市民は制限時間まで逃げ切れば勝ち、警官は全市民を捕まえて牢獄に送り込めば勝ちです。捕まった仲間は他の市民が助け出すこともできます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜20人（ロビー既定上限20人）</span></div>
  <div class="quick-card"><span class="label">1試合の長さ</span><span class="value">既定8分（480秒）＋準備30秒</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Spigot/Paper 1.21.x</span></div>
</div>

!!! warning "開発状況について"
    DorokeiGame は現在 **Phase 1-2（ロビーシステム＋ログアウト処理対応）** です。ロビー参加・看板連携・警官の自動割り振り・逃走準備・追跡演出・捕獲／救出・自動ロビー再参加までが実装済みです。一部に旧方式（後方互換）のコマンドが残っており、本格運用ではロビー方式の利用を推奨します。このWIKIは実装内容に基づくガイドですが、今後のアップデートで挙動が変わる場合があります。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、ルール、勝利条件、参加方法、特殊アイテム、コマンド。
- **OP・運営向け** … 導入手順、ロビー／エリア設定、config.yml、権限、管理コマンド。
