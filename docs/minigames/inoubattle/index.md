# 異能バトル（Inoubattle） <span class="badge done">公開中</span>

弓と、プレイヤーごとにランダムで配られる **異能** を使って戦う **バトルロイヤル** です。体力はハート3つと少なく、弓の被弾は常に固定1ダメージ。縮小する安全地帯の中で異能を活かして立ち回り、最後の1人を目指します。矢を当てるほど基礎能力が強化され、上空にはケアパケも投下されます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム（個人戦）</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">1〜24人（config で変更可・v1はソロ）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">最後の1人になる</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! note "ゲームの特徴"
    全員が弓を持ち、開始時に **異能がランダムで1つ** 配られる個人戦バトロワです。体力は **ハート3**（弓の被弾は常に固定1ダメージ）で、0になると即脱落。時間経過で **安全地帯（WorldBorder）が段階的に縮小**（全6フェーズ＋最終フェーズは中心が移動）します。相手に **矢を2ヒットさせるごと** にリロード短縮／矢の最大所持数増加がランダムで強化。フェーズ1〜3では上空から **ケアパケ**（先着3名が回復アイテムを回収）が投下されます。統合版（Bedrock）プレイヤーも参加可能です。

!!! info "v1（ソロモード）です"
    現在は **v1・ソロモード** で、実装済みの異能は **疾走・隠密・速射・探知・鑑定・追跡・強靭** です。他の多くの異能やデュオ・トリオは今後のアップデートで追加予定です（config でこれから増える異能の有効/無効を切り替えられます）。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、弓と異能、強化、ケアパケ、フェーズ、勝利条件、参加方法、コマンド。
- **OP・運営向け** … 導入、設定ツール、地点・フェーズ・看板設定、config（体力・弓・フェーズ・異能ON/OFF・ケアパケ・イベント）、権限、管理コマンド。
