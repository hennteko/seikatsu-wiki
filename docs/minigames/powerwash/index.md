# PowerWash <span class="badge done">公開中</span>

高圧洗浄機で汚れたステージをピカピカに磨き上げる、**協力型の洗浄ミニゲーム**（パワーウォッシュ・シミュレーター風）です。表面は細かい **セル** に分割され、洗浄機の水流を当ててセルの汚れを落としていきます。みんなで手分けして、制限時間内に洗浄率100%を目指します。

!!! note "実装状況（Phase 2）"
    現在 **参加・開始のプレイループ**（`join`/`leave`/`start`）・**制限時間**・**看板** に対応しています。**お金・アップグレード・ショップ・洗剤・ヒント（自動ハイライト）** などの要素は今後のフェーズで追加予定で、config にキーだけ先行して用意されています。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧽 協力ミニゲーム</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">制限時間内に洗浄率100%を目指す</span></div>
  <div class="quick-card"><span class="label">洗浄機</span><span class="value">高圧洗浄機（アイアンクワ）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.21.x（api-version 1.21）</span></div>
</div>

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、洗浄の仕組み、参加方法、コマンド。
- **OP・運営向け** … ステージ作成・スキャン・汚れ設定、ロビー・看板設定、config。
