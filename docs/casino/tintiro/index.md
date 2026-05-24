# チンチロ <span class="badge done">公開中</span>

3つのサイコロを振って役の強さを競う **日本式のサイコロ賭博** です。親（ディーラー）と子（参加者）に分かれ、ピンゾロ・シゴロ・ゾロ目などの役と出目の強さで勝敗が決まります。賭け金はエメラルドで、役の格差に応じて配当倍率が変わります。チンチロは単独プラグインではなく、統合プラグイン **CasinoPlugin に含まれる tintiro モジュール** として動作します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎲 カジノ・ギャンブル</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">親1人＋子（最大6人）</span></div>
  <div class="quick-card"><span class="label">通貨</span><span class="value">エメラルド</span></div>
  <div class="quick-card"><span class="label">参加方法</span><span class="value">看板 または `/tintiro`</span></div>
</div>

!!! info "CasinoPlugin のモジュールです"
    チンチロは単体のjarではなく、エメラルド銀行と8ゲームを1つに統合した **CasinoPlugin** の中の **tintiro モジュール** として提供されています。賭け金や配当はすべて銀行口座のエメラルドでやり取りされ、他のゲームと残高は共通です。CasinoPlugin 全体の構成は [CasinoPlugin の概要ページ](../casino-plugin/index.md) をご覧ください。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、役一覧と配当、賭け方、参加方法、コマンド、FAQ。
- **OP・運営向け** … 有効化、`tintiro.yml` 設定、セットアップ手順、管理コマンド、権限、トラブルシューティング。
