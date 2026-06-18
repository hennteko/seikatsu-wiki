# PersonalStorage（個人ストレージ） <span class="badge done">公開中</span>

プレイヤー **一人ひとり専用** の無限スタック倉庫を提供するユーティリティプラグインです。配布された **「個人ストレージ」の本** を右クリックすると自分専用の倉庫GUIが開き、アイテムを種類ごとに上限なく保管できます。エンダーチェストの大容量版のような感覚で使えます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧰 ユーティリティ・基盤</span></div>
  <div class="quick-card"><span class="label">対象</span><span class="value">プレイヤー個人（完全分離）</span></div>
  <div class="quick-card"><span class="label">容量</span><span class="value">1種類あたり実質無制限のスタック</span></div>
  <div class="quick-card"><span class="label">アクセス</span><span class="value">「個人ストレージ」の本を右クリック</span></div>
</div>

!!! info "GlobalStorage との違い"
    サーバー全体で共有する **GlobalStorage（共有倉庫）** に対し、PersonalStorage は **プレイヤーごとに独立した個人倉庫** です。中身は他プレイヤーから見えず、UUID ごとに JSON ファイルへ保存されます。機能は **収納・取り出し** に絞られており（カテゴリ分け・検索・自動収納などは非搭載）、シンプルな個人倉庫として動作します。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 本の開き方、預け入れ・取り出し、ページ送り。
- **OP・運営向け** … `/ps` コマンド・config・データ保存・権限。
