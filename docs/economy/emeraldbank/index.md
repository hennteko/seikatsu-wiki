# EmeraldBank <span class="badge done">公開中</span>

エメラルドを **預け入れ・引き出し・送金** できる、生活鯖の経済の基盤となる **銀行プラグイン** です。所持エメラルドを口座に預けてアイテムとして持ち歩く必要をなくし、ウォレットや通帳、ランキング看板などの便利機能も備えています。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">💰 経済・ランキング</span></div>
  <div class="quick-card"><span class="label">主な機能</span><span class="value">預入・引出・送金</span></div>
  <div class="quick-card"><span class="label">便利アイテム</span><span class="value">ウォレット / 通帳 / ATM看板</span></div>
  <div class="quick-card"><span class="label">稼働形態</span><span class="value">CasinoPlugin 内包モジュール</span></div>
</div>

!!! info "サーバーでの稼働形態について"
    EmeraldBank は統合版 **CasinoPlugin に内包された銀行モジュール（`bank`）** として動作します。サーバー経済の通貨（エメラルド残高）を一元管理する基盤であり、他モジュール（Poker / Slot / BlackJack / Tintiro / HorseRacing / Lottery / Quiz / Kart）は内部 API `EmeraldAPI` を通してこの口座データを参照します。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 預け入れ・引き出し・送金・残高確認・ランキング、ウォレットと通帳の使い方、コマンド一覧。
- **OP・運営向け** … 導入手順、config.yml、権限、ランキング看板の設置、管理コマンド、トラブルシューティング。
