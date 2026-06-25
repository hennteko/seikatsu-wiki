# MobBall <span class="badge done">公開中 / 統合プラグイン</span>

旧 **VillagerBall** と **Monsterball** を 1 つの jar に統合し、さらに **全モブ対応の単一捕獲ボール** へと一新した「モブ捕獲」プラグインです。`config.yml` のフラグで起動する単一の **capture モジュール** が、`ENDER_PEARL` ベースの 1 種類のボールであらゆるモブ（ボス・プレイヤーを除く）を捕獲・解放します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🐄 村人・モブ</span></div>
  <div class="quick-card"><span class="label">構成</span><span class="value">1 モジュール（capture）</span></div>
  <div class="quick-card"><span class="label">対象モブ</span><span class="value">あらゆるモブ（Mob）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! info "MobBall の構成 ― 単一の capture モジュール"
    MobBall は **capture モジュール** 1 つを 1 つの jar にまとめた統合プラグインです。`ModuleRegistry` が `config.yml` の `modules.capture.enabled` を見て、有効ならモジュールを起動します（TerrainTools / CasinoPlugin と同じ ModuleRegistry パターン）。捕獲時に **エンティティの全 NBT をバイト列で保存** するため、村人の取引データ・装備・体力・カスタム名・年齢などをすべてそのまま保持できます（旧 Monsterball で必要だった手動 restore は不要）。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 捕獲ボールの入手・捕獲・解放の流れ、捕獲できるモブ、コマンド、FAQ。
- **OP・運営向け** … 導入手順、`config.yml` と capture モジュール設定（`modules/capture.yml`）、コマンド、権限、トラブルシュート。
