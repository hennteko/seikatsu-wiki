# Kakurenbo（かくれんぼ） <span class="badge dev">v1.0 / 新規追加</span>

隠れる側が **ブロックに擬態** して隠れ、鬼が探し出す **ブロック擬態かくれんぼ（増え鬼）ミニゲーム** です。捕まった隠れ側は鬼に転向して増えていき、制限時間まで1人でも隠れ残れば隠れ側の勝ち、全員見つかれば鬼の勝ちです。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜16人</span></div>
  <div class="quick-card"><span class="label">1試合の長さ</span><span class="value">既定5分（300秒）＋隠れ時間30秒</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! success "ゲームの特徴（擬態 × 増え鬼）"
    隠れる側は透明化し、自分に追従する表示ブロックで **任意のブロックに化けます**。**静止すると升目にスナップして完全に同化**し、動くとズレて気配が出ます。鬼は通常の姿で探し、**化けた隠れ側を殴ると捕獲**。捕まった人は数秒後に鬼へ転向して追う側に回ります（増え鬼）。

!!! info "参加・操作はシンプル"
    参加・退出・開始は **看板** または **コマンド**（`/kakurenbo join`・`leave`・`start`、いずれも全員可）で行えます。隠れ側は **ブロック選択看板** から化けるブロックを選べます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 役割、擬態の仕方、鬼の探索・捕獲、勝敗、流れ。
- **OP・運営向け** … スポーン/フィールド設定、看板、擬態ブロックパレット、`/kakurenbo` コマンド、config、権限。
