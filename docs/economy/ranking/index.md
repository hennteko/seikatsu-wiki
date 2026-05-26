# Ranking <span class="badge dev">開発中</span>

採掘・釣り・討伐などプレイヤーの行動を **7種類の統計** としてカウントし、ランキング表示とエメラルド報酬を提供する **統計・ランキングプラグイン** です。一定回数の行動ごとにエメラルドが自動で支払われ、ランキングは月初に自動リセットされます。報酬の支払いは **CasinoPlugin（EmeraldAPI）** を経由するため、CasinoPlugin の導入が必須です。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">📊 経済・ランキング</span></div>
  <div class="quick-card"><span class="label">主な機能</span><span class="value">統計カウント・ランキング・報酬</span></div>
  <div class="quick-card"><span class="label">統計の種類</span><span class="value">採掘・釣りなど7種</span></div>
  <div class="quick-card"><span class="label">バージョン</span><span class="value">1.0.0</span></div>
</div>

!!! info "CasinoPlugin への依存について"
    Ranking は単独のプラグイン（jar）ですが、エメラルド報酬の支払いに **CasinoPlugin に内包された銀行（bank）モジュール** を利用します。`plugin.yml` で `depend: [CasinoPlugin]` を宣言しているため、サーバー起動時には CasinoPlugin が先に有効化されている必要があります。CasinoPlugin が無い場合、統計カウントとランキング表示は動作しますが、報酬の支払いは行われません。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … カウントされる統計の種類、ランキングの見方、月次リセットと報酬、コマンド一覧。
- **OP・運営向け** … 導入手順、config.yml、ランキング看板の設置、管理コマンド、権限、トラブルシューティング。
