# MobBall <span class="badge done">公開中 / 統合プラグイン</span>

旧 **VillagerBall** と **Monsterball** を 1 つの jar に統合した「生物ボール化」プラグインです。村人をエンダーパール／エンダーアイ風のボールに収納する **villager モジュール** と、スライムボール風のモンスターボールに収納する **monster モジュール** を、`config.yml` のフラグで個別に ON / OFF できます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🐄 村人・モブ</span></div>
  <div class="quick-card"><span class="label">構成</span><span class="value">2 モジュール（villager / monster）</span></div>
  <div class="quick-card"><span class="label">対象モブ</span><span class="value">村人 ＆ モンスター</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 26.1.2</span></div>
</div>

!!! info "MobBall の構成 ― 2 モジュール式"
    MobBall は **villager モジュール** と **monster モジュール** の 2 つを 1 つの jar にまとめた統合プラグインです。`ModuleRegistry` が `config.yml` の `modules.<id>.enabled` を見て、有効なモジュールだけを起動します。あるモジュールが起動に失敗しても、もう一方のモジュールは動き続ける設計です。旧 VillagerBall / Monsterball を別々に入れていたサーバーも、**配布済みのボールアイテムをそのまま継続利用できます**（PDC owner の legacy fallback あり）。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 2 モジュールの違い、ボールの入手・捕獲・解放の流れ、コマンド、FAQ。
- **OP・運営向け** … 導入手順、`config.yml` とモジュール別設定、コマンド、権限、旧プラグインからの移行手順、トラブルシュート。
