# スロット <span class="badge done">公開中</span>

エメラルドを賭けて3つのリールを回す **目押し対応のスロットマシン** です。GUIで遊べて、ダイヤを揃えると当選確率が大幅にアップする **確変モード** に突入します。スロットは単独プラグインではなく、統合プラグイン **CasinoPlugin に含まれる slot モジュール** として動作します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎰 カジノ・ギャンブル</span></div>
  <div class="quick-card"><span class="label">所属</span><span class="value">CasinoPlugin / slot モジュール</span></div>
  <div class="quick-card"><span class="label">通貨</span><span class="value">エメラルド（銀行口座）</span></div>
  <div class="quick-card"><span class="label">遊び方</span><span class="value">[Slot] 看板を右クリック</span></div>
</div>

!!! info "CasinoPlugin の中の slot モジュール"
    スロットは専用jarではなく、エメラルド銀行と8ゲームを1つに統合した **CasinoPlugin** の中の **slot モジュール** です。導入はCasinoPlugin本体を入れるだけで、有効化は `config.yml` の `modules.slot.enabled`、個別設定は `modules/slot.yml` で行います。CasinoPlugin 全体の構成については [CasinoPlugin の概要ページ](../casino-plugin/index.md) をご覧ください。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、賭け方、配当表、目押し・確変のコツ、GUI操作。
- **OP・運営向け** … 有効化、`slot.yml` の設定項目、シンボル重み・配当の調整、権限。
