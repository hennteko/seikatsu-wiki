# ルーレット <span class="badge done">公開中</span>

0〜36の**ヨーロピアン盤（37マス）** で回す定番のカジノゲームです。数字・赤黒・奇偶・ハイロー・ダース・コラムに賭け、当たればベット額×配当が払い戻されます。1人用のGUIでサッと遊ぶ **ソロ** と、みんなで同じ盤を囲んでベット時間内に賭ける **マルチ卓** の両方に対応。ルーレットは単独プラグインではなく、統合プラグイン **CasinoPlugin に含まれる roulette モジュール** として動作します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎲 カジノ・ギャンブル</span></div>
  <div class="quick-card"><span class="label">盤面</span><span class="value">ヨーロピアン（0〜36・37マス）</span></div>
  <div class="quick-card"><span class="label">通貨</span><span class="value">エメラルド</span></div>
  <div class="quick-card"><span class="label">遊び方</span><span class="value">ソロGUI／マルチ卓（看板・`/roulette`）</span></div>
</div>

!!! info "CasinoPlugin のモジュールです"
    ルーレットは単体のjarではなく、エメラルド銀行と各ゲームを1つに統合した **CasinoPlugin** の中の **roulette モジュール** として提供されています。賭け金や配当はすべて銀行口座のエメラルドでやり取りされ、他のゲームと残高は共通です。CasinoPlugin 全体の構成は [CasinoPlugin の概要ページ](../casino-plugin/index.md) をご覧ください。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、賭けの種類と配当、ソロ／マルチの流れ、参加方法、コマンド、FAQ。
- **OP・運営向け** … 有効化、`roulette.yml` 設定、盤の設置、看板、管理コマンド、権限。
