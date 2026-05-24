# ポーカー <span class="badge done">公開中</span>

トランプの定番ゲーム **テキサスホールデム** を Minecraft 上で遊べるゲームです。看板をクリックして参加し、配られた手札と公開カードで一番強い役を作って、賭け金（ポット）の獲得を目指します。賭け金はすべてエメラルド（銀行の口座残高）でやり取りされます。

ポーカーは単独のプラグインではなく、統合プラグイン **CasinoPlugin に含まれる poker モジュール** です。専用 jar の導入は不要で、CasinoPlugin が動いていれば運営側で有効化するだけで遊べます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎰 カジノ・ギャンブル</span></div>
  <div class="quick-card"><span class="label">通貨</span><span class="value">エメラルド</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜6人</span></div>
  <div class="quick-card"><span class="label">ゲーム種別</span><span class="value">テキサスホールデム</span></div>
</div>

!!! info "CasinoPlugin のモジュールです"
    ポーカーは、エメラルド銀行と8つのゲームを1つの jar に統合した **CasinoPlugin** の中の poker モジュールとして動いています。有効化は `plugins/CasinoPlugin/config.yml` の `modules.poker.enabled`、個別設定は `plugins/CasinoPlugin/modules/poker.yml` で行います。プラグイン全体の構成は [CasinoPlugin の概要ページ](../casino-plugin/index.md) をご覧ください。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 参加方法、ゲームの流れ、アクション（CALL / RAISE / FOLD）の使い方、役の強さ、コマンド一覧。
- **OP・運営向け** … 有効化、`poker.yml` の設定、看板・座標のセットアップ手順、管理コマンド、権限、トラブルシューティング。
