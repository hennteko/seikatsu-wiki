# ヨット（ヤッツィー） <span class="badge done">公開中</span>

5個のサイコロを振って役を作る **ヨット（ヤッツィー）風のサイコロ賭博** です。賭け金を置いてサイコロを振り、できた役に応じて配当が払い戻されます。5個ゾロ目の「ヨット」を出すと **ジャックポット** を総取り。ヨットは単独プラグインではなく、統合プラグイン **CasinoPlugin に含まれる yacht モジュール** として動作します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎲 カジノ・ギャンブル</span></div>
  <div class="quick-card"><span class="label">形式</span><span class="value">クイックヨット（1人用GUI）＋ジャックポット</span></div>
  <div class="quick-card"><span class="label">通貨</span><span class="value">エメラルド</span></div>
  <div class="quick-card"><span class="label">遊び方</span><span class="value">看板 または `/yacht quick`</span></div>
</div>

!!! info "CasinoPlugin のモジュールです"
    ヨットは統合プラグイン **CasinoPlugin** の **yacht モジュール** として提供されています。賭け金・配当はエメラルド銀行の残高でやり取りされ、他のゲームと残高は共通です。CasinoPlugin 全体の構成は [CasinoPlugin の概要ページ](../casino-plugin/index.md) をご覧ください。

!!! note "現フェーズはクイックヨット＋ジャックポット"
    現在は **クイックヨット**（サッと1回勝負のGUI）と **ジャックポット** に対応しています。ソロのスコアアタックや対戦ポットは今後のフェーズで追加予定です。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、役と配当、ジャックポット、参加方法、コマンド、FAQ。
- **OP・運営向け** … 有効化、`yacht.yml` 設定、看板、管理コマンド、権限。
