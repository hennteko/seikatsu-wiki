# BedWarsPlugin <span class="badge dev">開発中</span>

Minecraft上で **ベッドウォーズ（BedWars／ベッドウォーズ）** を再現した **チーム戦PvGミニゲーム** です。最大8色のチームに分かれ、各チームは自分の **ベッド** を守りながら、資源で装備・チーム強化を整えて敵チームを攻めます。**自分のベッドが破壊されると復活できなくなり、全滅したチームから敗退** します。最後まで生き残ったチームの勝利です。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム（チーム戦）</span></div>
  <div class="quick-card"><span class="label">チーム</span><span class="value">2〜8チーム（有効化した色ぶん・自動振り分け）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">敵チームのベッドを壊し、全チームを全滅させる</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! success "ベッドウォーズの特徴（資源集め＋建築＋チーム強化）"
    各チームには **拠点・スポーン・ベッド** があり、拠点の **ジェネレーター** から鉄・金が、フィールド中央の **ダイヤ／エメラルドジェネレーター** から上位資源が自動で湧きます。集めた資源を **アイテムショップ**（村人NPC）で装備・ブロック・消耗品に、**ダイヤ** を **チームアップグレード**（剣・防具・採掘速度・鍛冶場・回復プール・トラップ）に交換して戦います。**自分のベッドがある間は何度でも復活**できますが、破壊されると次にやられたとき脱落（ファイナルキル）です。試合が長引くと **ダイヤ/エメラルドのTier上昇 → 全ベッド崩壊 → サドンデス（ドラゴン出現）** の順にイベントが進行します。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、ルール、勝利条件、資源とショップ、ベッド防衛、参加方法、コマンド。
- **OP・運営向け** … 導入手順、チーム/ジェネレーター/ショップ/看板設定、config.yml、権限、管理コマンド。
