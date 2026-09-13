# Overcook <span class="badge done">公開中</span>

みんなで協力して注文をさばく、**オーバークック風の協力調理ミニゲーム** です。食材を取り、切って・焼いて・皿に盛り付け、次々に入る注文を制限時間内に提供してスコアを稼ぎます。手が足りないキッチンで役割分担と声かけがカギ。到達したスコアで星評価（3段階）が決まり、パーティごとのハイスコアが記録されます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🍳 協力・アクションミニゲーム</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">最大8人（config で変更可）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">制限時間内に目標スコアを達成（星評価）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.21.x（api-version 1.21）</span></div>
</div>

!!! note "ゲームの特徴"
    全員で1つのキッチンを回す協力ゲームです。**食材チェスト**から食材を取り、**まな板**で切り、**コンロ**で焼き、**皿**に盛り付けて **提供口** に出します。注文は時間ごとに入り、期限切れや誤納品は減点。連続で正しく提供すると **コンボ倍率（最大x3）** が上がります。制限時間内の合計スコアで **星1〜3** が決まり、パーティ別のハイスコアに残ります。料理・食材は `recipes.yml` で自由に追加でき、ステージ（キッチン）も複数作れます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、調理の流れ、ステーション、料理、スコアと星評価、参加方法、コマンド。
- **OP・運営向け** … 導入、設定ツール、ステージ・ステーション設定、看板、config／recipes.yml、権限、管理コマンド。
