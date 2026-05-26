# BossPlugin <span class="badge done">公開中</span>

3種類の **カスタムボス**（Zombie / Skeleton / Spider）を召喚して戦える、単独のボスバトル・プラグインです。各ボスは独自のスキル・3段階のフェーズ・HPバー・ミニオン召喚・距離制限などを備えており、OPが `/bossspawn` で任意の場所に降臨させることができます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🐄 村人・モブ</span></div>
  <div class="quick-card"><span class="label">ボスの種類</span><span class="value">Zombie / Skeleton / Spider の3種</span></div>
  <div class="quick-card"><span class="label">召喚コマンド</span><span class="value"><code>/bossspawn &lt;type&gt; [hp]</code></span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper api-version 26.1.2</span></div>
</div>

!!! note "プラグイン概要"
    プラグイン名は `BossPlugin`（v1.0）、メインクラスは `com.yourname.bossplugin.BossPlugin`。3体それぞれが固有のYAML（`zombie_boss.yml` / `skeleton_boss.yml` / `spider_boss.yml`）を持ち、攻撃間隔・フェーズ係数・ミニオン数などを細かく調整できます。ボスを倒すと **経験値オーブが150ドロップ**（通常のMobドロップは消去）されます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 3種類のボスの特徴・攻撃パターン・戦い方・報酬・FAQ。
- **OP・運営向け** … 導入手順、`/bossspawn` の使い方、各ボスの既定パラメータ、config・権限・トラブルシュート。
