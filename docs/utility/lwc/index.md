# LWC（チェスト保護） <span class="badge done">公開プラグイン</span>

チェストやかまど・ドアなどに **ロック（保護）をかけられる** プラグインです。チェストを置くと自動で自分専用にロックされ、他のプレイヤーは開けられなくなります。フレンドへの共有、パスワード保護、寄付用チェストなど多彩な保護タイプがあります。導入しているのは LWC の後継版 **LWCX**（LWC Extended）です。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧰 ユーティリティ・基盤</span></div>
  <div class="quick-card"><span class="label">主な機能</span><span class="value">チェスト・ドア等の保護</span></div>
  <div class="quick-card"><span class="label">自動保護</span><span class="value">チェストは置くだけでロック</span></div>
  <div class="quick-card"><span class="label">メインコマンド</span><span class="value">/lwc（/cprivate ほか）</span></div>
</div>

!!! info "保存形式について"
    保護データは既定で `plugins/LWC/lwc.db`（SQLite）に保存されます。`core.yml` の設定で MySQL に変更することもできます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 自動ロックのしくみ、保護タイプ（private / public / password など）、フレンドとの共有、フラグ・モード、FAQ。
- **OP・運営向け** … 導入手順、`core.yml` の設定、管理コマンド（`/lwc admin`）、権限ノード、トラブルシューティング。
