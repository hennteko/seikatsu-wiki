# CustomRailway <span class="badge done">公開中</span>

トロッコを連結して列車を作り、駅を登録して運行できる **鉄道システムプラグイン**。鎖（IRON_CHAIN）で複数のトロッコを連結し、線路下のブロック（金/鉄/石炭/ダイヤ/エメラルド）で速度を制御。駅は現在地に作成・GUIで一覧表示・テレポート（管理者）まで対応します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🚂 移動・乗り物・テレポート</span></div>
  <div class="quick-card"><span class="label">最大連結数</span><span class="value">5 両（既定）</span></div>
  <div class="quick-card"><span class="label">速度制御</span><span class="value">線路下ブロックで切替</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x</span></div>
</div>

!!! note "このプラグインについて"
    CustomRailway は **駅・列車** を扱う独立プラグインです（plugin.yml 上は依存なし）。プレイヤーは `customrailway.station.create`・`customrailway.train.connect`（どちらも既定 true）で **駅作成・列車連結** が可能です。路線（`route`）機能は現バージョンのコマンド処理には未実装で、駅は内部的に「接続路線」リストを持てる構造のみ用意されています。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 駅の作成・削除・GUI、トロッコ連結／切り離し、速度制御ブロック、`/railway` コマンド、FAQ。
- **OP・運営向け** … 導入手順、`config.yml`、管理コマンド、権限ノード、トラブルシュート、ソースとの差異。
