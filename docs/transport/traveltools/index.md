# TravelTools <span class="badge done">公開中 / 統合プラグイン</span>

旧 **Spawn / Playercompass / PortalVisualizer / Netherfortress** の4つの自作プラグインを1つにまとめた、生活鯖の **移動・テレポート系統合プラグイン** です。スポーン帰還・プレイヤー追跡コンパス・ポータルの可視化・初回ネザー入場時の要塞通知が、1本の jar とモジュール式 config で動作します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🚂 移動・乗り物・テレポート</span></div>
  <div class="quick-card"><span class="label">構成</span><span class="value">4モジュール（spawn / compass / portalvis / nether）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 26.1.2</span></div>
  <div class="quick-card"><span class="label">依存</span><span class="value">Multiverse-Portals（softdepend）</span></div>
</div>

!!! info "TravelTools の構成"
    TravelTools は **spawn / compass / portalvis / nether の4モジュール** を1つの jar に統合したプラグインです。`ModuleRegistry` が `config.yml` の `modules.<id>.enabled` を見て、有効なモジュールだけを起動します。`portalvis` モジュールが必要とする **Multiverse-Portals は softdepend** 扱いで、未導入環境では `portalvis` だけが自動で無効化され、残り3モジュール（spawn / compass / nether）は通常稼働します。既存の `/spawn` `/compass` `/pv` コマンドや既存プレイヤーの初回ネザー入場フラグもそのまま引き継がれるため、運用方法を変える必要はありません。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 4モジュールでできることの概要、`/spawn` `/compass` `/pv` などの使い方、よくある質問。
- **OP・運営向け** … 導入手順、`config.yml` とモジュール別 yml、統合管理コマンド `/travel`、権限ノード、既存サーバーからの移行手順とトラブルシュート。
