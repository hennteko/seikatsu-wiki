# CustomRailway <span class="badge done">公開中</span>

トロッコを連結して列車を作り、駅を登録して運行できる **鉄道システムプラグイン**。鎖（IRON_CHAIN）で複数のトロッコを連結し、線路下のブロック（金/鉄/石炭/ダイヤ/エメラルド）で速度を制御。駅は現在地に作成・GUIで一覧表示・テレポート（管理者）まで対応します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🚂 移動・乗り物・テレポート</span></div>
  <div class="quick-card"><span class="label">最大連結数</span><span class="value">5 両（既定）</span></div>
  <div class="quick-card"><span class="label">速度制御</span><span class="value">線路下ブロックで切替</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x</span></div>
</div>

!!! note "このプラグインについて"
    CustomRailway は **駅・列車** を扱う独立プラグインです（plugin.yml 上は依存なし）。`/railway` コマンドは **OP 限定**（`customrailway.admin`）で、プレイヤーは `[Railway]` / `[Station]` 看板から駅一覧 GUI を開きます。トロッコ連結（鎖）・切り離し（ハサミ）は `customrailway.train.connect`（既定 true）で誰でも可能です。路線（`route`）機能は現バージョンでは「今後のアップデートで実装予定」のスタブのみで、駅は内部的に「接続路線」リストを持てる構造だけが用意されています。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … `[Railway]` / `[Station]` 看板からの駅一覧 GUI、トロッコ連結／切り離し、速度。
