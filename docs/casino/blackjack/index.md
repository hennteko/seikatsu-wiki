# ブラックジャック <span class="badge done">公開中</span>

ディーラー側（サーバー）にエメラルドを賭け、配られたカードの合計を **21 に近づける** ことを目指す定番のカードゲームです。複数人が同じ卓に参加し、各自が手札を引くか止めるかを選択。最も高いスコア（バーストしないこと）を出したプレイヤーがポット（賭け金の合計）を獲得します。生活鯖では統合プラグイン **CasinoPlugin に含まれる blackjack モジュール** として動作します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎰 カジノ・ギャンブル</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">1〜6人（看板参加時）</span></div>
  <div class="quick-card"><span class="label">通貨</span><span class="value">エメラルド</span></div>
  <div class="quick-card"><span class="label">勝敗</span><span class="value">21 に最も近いプレイヤーがポット獲得</span></div>
</div>

!!! info "CasinoPlugin のモジュールです"
    ブラックジャックは単独のプラグインではなく、エメラルド銀行と8つのゲームを1つに統合した **CasinoPlugin** の中の **blackjack モジュール** です。専用 jar の導入は不要で、有効化は `config.yml` の `modules.blackjack.enabled` で行います。賭け金に使うエメラルドは銀行（bank モジュール）の口座残高で、すべてのゲーム共通です。CasinoPlugin 全体の構成は [CasinoPlugin 概要ページ](../casino-plugin/index.md) を参照してください。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、ルール、賭け方、参加方法、ヒット／スタンドの操作、コマンド、FAQ。
- **OP・運営向け** … 有効化、`blackjack.yml` の設定、地点セットアップ、看板の作り方、権限、管理コマンド。
