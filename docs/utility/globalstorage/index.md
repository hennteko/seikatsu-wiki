# GlobalStorage2 <span class="badge done">公開中</span>

サーバー全体で共有できる **共有倉庫プラグイン** です。アイテムは個数の上限なく無限にスタックでき、JSON ファイルとして永続保存されます。専用の **GS スティック** や `/gs` コマンドから倉庫 GUI を開き、アイテムの預け入れ・取り出し・カテゴリ分類・検索が行えます。さらに、ピックアップしたアイテムを自動で倉庫へ送る **自動転送フィルター** と、チェスト内の中身を自動で吸い上げる **自動収納チェスト** にも対応しています。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧰 ユーティリティ・基盤</span></div>
  <div class="quick-card"><span class="label">主な機能</span><span class="value">共有倉庫 / 無限スタック</span></div>
  <div class="quick-card"><span class="label">便利機能</span><span class="value">自動転送 / 自動収納チェスト</span></div>
  <div class="quick-card"><span class="label">メインコマンド</span><span class="value">/gs（/globalstorage）</span></div>
</div>

!!! info "保存形式について"
    倉庫の中身は `plugins/GlobalStorage2/storage.json`（Base64 シリアライズされた ItemStack ＋ 数量）、自動収納チェストは `auto_chests.json`、自動転送フィルターは `tensou_filters.json` にそれぞれ JSON 形式で保存されます。5 分ごとの自動保存と、シャットダウン時の保存に対応しています。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … GS スティックの使い方、倉庫 GUI の操作（取り出し・収納・カテゴリ・ページ送り）、自動転送フィルター、`/gs` のサブコマンド、FAQ。
- **OP・運営向け** … 導入手順、`config.yml`（NG ワールド設定）、自動収納チェスト、管理コマンド、権限ノード、トラブルシューティング。
