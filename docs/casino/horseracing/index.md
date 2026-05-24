# 競馬 <span class="badge done">公開中</span>

出走する馬の能力を見て馬券を買い、レース結果を予想して当てるパリミュチュエル方式のレースギャンブルです。出走馬それぞれに「脚力・スタミナ・賢さ」のステータスが自動生成され、その能力をもとにオッズが決まります。単勝から3連単まで **7種類の馬券** が用意されており、的中すれば賭けたエメラルドにオッズ倍率を掛けた配当を受け取れます。生活鯖では統合プラグイン **CasinoPlugin に含まれる horse モジュール** として動作します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎰 カジノ・ギャンブル</span></div>
  <div class="quick-card"><span class="label">馬券</span><span class="value">7種類（単勝〜3連単）</span></div>
  <div class="quick-card"><span class="label">通貨</span><span class="value">エメラルド</span></div>
  <div class="quick-card"><span class="label">配当方式</span><span class="value">パリミュチュエル（オッズ変動）</span></div>
</div>

!!! info "CasinoPlugin のモジュールです"
    競馬は単独のプラグインではなく、エメラルド銀行と8つのゲームを1つに統合した **CasinoPlugin** の中の **horse モジュール** です。専用 jar の導入は不要で、有効化は `config.yml` の `modules.horse.enabled` で行います。馬券に使うエメラルドは銀行（bank モジュール）の口座残高で、すべてのゲーム共通です。CasinoPlugin 全体の構成は [CasinoPlugin 概要ページ](../casino-plugin/index.md) を参照してください。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 馬券の種類と買い方、レースの流れ、オッズと配当の仕組み、ベット用コマンド、FAQ。
- **OP・運営向け** … 有効化、`horse.yml` の設定、コース・地点のセットアップ、看板の作り方、権限、管理コマンド。
