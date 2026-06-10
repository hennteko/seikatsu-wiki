# 宝くじ <span class="badge done">公開中</span>

1枚10エメラルドで **抽選券** を購入し、右クリックで使うとカテゴリーごとの景品アイテムが当たる宝くじです。CasinoPlugin に含まれる **lottery モジュール** として動作します。種類（カテゴリー）を選んでまとめ買いし、好きなタイミングで1枚ずつ引けます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎰 カジノ・ギャンブル</span></div>
  <div class="quick-card"><span class="label">価格</span><span class="value">抽選券1枚 10エメラルド</span></div>
  <div class="quick-card"><span class="label">買い方</span><span class="value">[宝くじ] 看板を右クリック</span></div>
  <div class="quick-card"><span class="label">景品</span><span class="value">5カテゴリーのアイテム抽選</span></div>
</div>

!!! info "CasinoPlugin のモジュールです"
    宝くじは単独のプラグインではなく、エメラルド銀行と8ゲームを1つのjarに統合した **CasinoPlugin** の中の **lottery モジュール** です。通貨はサーバー共通の「エメラルド」（銀行口座の残高）で、有効化や全体の仕組みは CasinoPlugin 側の設定で管理されます。CasinoPlugin 全体については [CasinoPlugin 概要ページ](../casino-plugin/index.md) を参照してください。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 看板での抽選券の買い方、右クリックでの抽選、カテゴリーごとの景品。
- **OP・運営向け** … 有効化、`lottery.yml` の景品テーブル設定、確率（重み）の仕組み、管理コマンド。
