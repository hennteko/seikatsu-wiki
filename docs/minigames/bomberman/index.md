# Bomberman（ボンバーマン） <span class="badge dev">v1.0 / 新規追加</span>

格子状のフィールドで爆弾を置き、十字の爆風で軟ブロックを壊しながら相手を巻き込んで脱落させる、本家ボンバーマン風の **対戦アクションミニゲーム** です。最後の1人になれば勝ち。軟ブロックから出るパワーアップで火力・爆弾数・スピードを強化していきます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2人〜（スポーン数・最大人数で上限）</span></div>
  <div class="quick-card"><span class="label">勝敗</span><span class="value">最後の1人で勝利（一発脱落・リスポーンなし）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! success "ゲームの特徴"
    爆弾は設置後 約2.5秒で **十字に起爆**。爆風は火力のマス数だけ伸び、硬ブロックで止まり、軟ブロックを壊します。壊した軟ブロックからは一定確率で **パワーアップ**（火力／ボム数／スピード／貫通／フルファイア）が出現。被弾は **一発で脱落（観戦）** です。制限時間が来ると外周から硬ブロックがせり上がる **サドンデス** で決着を急かします。

!!! info "参加・操作はシンプル"
    参加・退出・開始は **看板のクリック** または **コマンド**（`/bomberman join`・`leave`・`start`、いずれも全員可）で行えます。爆弾は「ボム」アイテムを **右クリック** で足元の升目に設置します。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、爆弾の置き方、パワーアップ、勝敗、操作。
- **OP・運営向け** … フィールド設定、看板、`/bomberman` コマンド、config、権限。
