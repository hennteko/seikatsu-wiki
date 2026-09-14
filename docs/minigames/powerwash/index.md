# PowerWash <span class="badge dev">開発中</span>

高圧洗浄機で汚れたステージをピカピカに磨き上げる、**協力型の洗浄ミニゲーム**（パワーウォッシュ・シミュレーター風）です。表面は細かい **セル** に分割され、洗浄機の水流を当ててセルの汚れを落としていきます。みんなで手分けして洗浄率100%を目指します。

!!! warning "現在開発中（Phase 1）です"
    PowerWash は現在 **Phase 1（v0.1.0）** で、実装済みなのは **ステージ作成・セル生成（scan）・汚れ設定・洗浄判定・BlockDisplay 表示** までです。参加・開始（`join`/`start`）やショップ・ヒント等のプレイループは **Phase 2 以降で追加予定** です。このページは現時点の実装内容にもとづく暫定版で、今後アップデートされます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧽 協力ミニゲーム（開発中）</span></div>
  <div class="quick-card"><span class="label">進捗</span><span class="value">Phase 1（セル生成・洗浄判定）</span></div>
  <div class="quick-card"><span class="label">洗浄機</span><span class="value">高圧洗浄機（アイアンクワ）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.21.x（api-version 1.21）</span></div>
</div>

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … ゲームのコンセプトと洗浄の仕組み（現状でわかっている範囲）。
- **OP・運営向け** … 現在実装済みのステージ作成・スキャン・汚れ設定コマンド、config。
