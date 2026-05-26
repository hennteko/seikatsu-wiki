# CustomVillagerTrade <span class="badge custom">自作プラグイン</span>

村人の取引内容を **自由にカスタマイズ** できる管理者向けプラグインです。GUIで取引を編集し、プリセットとして保存・配布し、任意の場所にプリセット村人を **スポーン** できます。CasinoPlugin が導入されていれば、エメラルド残高を使った取引（EmeraldAPI連携）にも対応します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🐄 村人・モブ</span></div>
  <div class="quick-card"><span class="label">対象モブ</span><span class="value">村人（Villager）</span></div>
  <div class="quick-card"><span class="label">主な機能</span><span class="value">取引編集／プリセット／スポーン</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper api-version 26.1.2</span></div>
</div>

!!! info "CasinoPlugin との連携（softdepend）"
    本プラグインは **CasinoPlugin** を `softdepend`（任意依存）として宣言しています。CasinoPlugin が読み込まれている場合、内蔵の **EmeraldAPI** を介してエメラルド残高を参照・出金できます。CasinoPlugin が無くてもプラグイン本体は通常どおり動作し、取引機能はバニラのインベントリベースで完結します。`config.yml` の `emeraldbank.enabled` で連携の有効・無効を切り替えられます（旧 EmeraldBank との後方互換のためキー名は据え置き）。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … カスタム村人との取引方法、見分け方、よくある質問。
- **OP・運営向け** … 導入、`config.yml`、プリセットファイル構成、`/vtrade` 各サブコマンド、権限、トラブルシュート。
