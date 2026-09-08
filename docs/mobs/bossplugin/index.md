# BossPlugin <span class="badge done">公開中</span>

3種類の **カスタムボス**（Zombie / Skeleton / Spider）を召喚して戦えるボスバトル・プラグインです。各ボスは独自のスキル・3段階のフェーズ・HPバー・ミニオン召喚・距離制限などを備えており、OPが `/bossspawn` で任意の場所に降臨させることができます。さらに、複数ウェーブを順に殲滅していく **襲撃（WAVE）モード**（`/bossraid`）を搭載し、難易度プリセット・レーティング（掛け金）・雑魚／オオモノ／最終ボス・特殊イベント・変異・スコア＆ランキングまで含む協力型コンテンツとして遊べます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🐄 村人・モブ</span></div>
  <div class="quick-card"><span class="label">ボスの種類</span><span class="value">Zombie / Skeleton / Spider の3種</span></div>
  <div class="quick-card"><span class="label">召喚コマンド</span><span class="value"><code>/bossspawn &lt;type&gt; [hp]</code></span></div>
  <div class="quick-card"><span class="label">襲撃モード</span><span class="value"><code>/bossraid</code>（別名 <code>/raid</code>）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! note "プラグイン概要"
    プラグイン名は `BossPlugin`（v1.0）、メインクラスは `com.yourname.bossplugin.BossPlugin`。3体のボスはそれぞれ固有のYAML（`zombie_boss.yml` / `skeleton_boss.yml` / `spider_boss.yml`）を持ち、攻撃間隔・フェーズ係数・ミニオン数などを細かく調整できます。単体のボスを倒すと **経験値オーブが150ドロップ**（通常のMobドロップは消去）されます。襲撃モードは `config.yml` / `waves.yml` / `difficulty.yml` / `rewards.yml` と、自動生成される `arenas.yml` / `records.yml` で構成されます。

## 2つの遊び方

- **単体ボス召喚（`/bossspawn`）** … OPが任意地点に1体のカスタムボスを降臨させる、いつものボス戦。
- **襲撃（WAVE）モード（`/bossraid`）** … 専用アリーナで、雑魚→オオモノ→最終ボスのウェーブを難易度を選んで殲滅する協力コンテンツ。クリアで経験値報酬とスコア／ランキングが付く。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 3種類のボスの特徴・攻撃パターン・戦い方・襲撃モードの遊び方／コマンド・報酬・FAQ。
- **OP・運営向け** … 導入手順、`/bossspawn`・`/bossraid` の使い方、各ボスの既定パラメータ、襲撃アリーナ構築手順、config・権限・トラブルシュート。
