# Tutorial2 <span class="badge done">公開中</span>

初参加プレイヤーをサーバーの基本操作・ルールへ案内する **チュートリアルプラグイン** です。初回ログイン時に専用の「紙」を自動配布し、ルールブックの確認・資源ワールド（S1）への移動・基本ツール／装備のクラフト・最終パスワード入力までを **段階的（21ステップ）** に進行させ、各ステップ完了時にエメラルド報酬を付与します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧰 ユーティリティ・基盤</span></div>
  <div class="quick-card"><span class="label">主な機能</span><span class="value">初回案内・21ステップ進行</span></div>
  <div class="quick-card"><span class="label">報酬</span><span class="value">エメラルド（各ステップ）</span></div>
  <div class="quick-card"><span class="label">バージョン</span><span class="value">1.0</span></div>
</div>

!!! info "進行は自動で記録されます"
    プレイヤーごとの現在ステップは `userdata.yml` に保存され、サーバー再起動や再ログインをまたいでも引き継がれます。途中で切断しても、再ログイン時に現在の目標が改めて表示されます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … チュートリアルの始め方、21ステップの流れ、コマンド、再開・再配布の方法、よくある質問。
- **OP・運営向け** … 導入手順、`userdata.yml` の扱い、管理コマンド（`/tutorial skip` / `/tutorial paper`）、権限（`tutorial.admin`、既定 op）、トラブルシューティング。
