# 一本柱PvP <span class="badge done">公開中</span>

**細い柱の上** で、一定間隔で配られる **ランダムアイテムだけ** を使って戦う、バトロワ形式の個人戦ミニゲームです。参加者ぶんの柱が自動生成され、全員が自分の柱の頂上からスタート。落ちたら（または倒されたら）脱落で、最後の1人、または時間切れ時に最多キルの生存者が勝者です。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 個人戦・バトロワ（FFA）</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜16人（config で変更可）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">最後の1人、または時間切れ時に最多キルの生存者</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser）</span></div>
</div>

!!! note "ゲームの特徴"
    参加人数ぶんの **細い1マスの柱** がフィールド中心のまわりに自動生成され、全員が自分の柱の頂上からスタートします。開始後は **一定間隔（既定3秒）** で生存者全員にそれぞれ別々の **ランダムアイテム** が1個ずつ配られ、プレイヤーはそれだけで戦います。相手を **柱から落とす・倒す** と撃破、自分が落ちると脱落（観戦）。柱の頂上から一定の高さまでしかブロックを積めず、フィールドの外には出られません。最後の1人、または **制限時間（既定300秒）** 切れ時に最多キルの生存者が勝ちます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、ランダムアイテム、柱と落下判定、積み上げ、勝利条件、参加方法、コマンド。
- **OP・運営向け** … 導入、スポーン・ロビー・フィールド設定、看板、config（柱・アイテム抽選・戦闘）、権限、管理コマンド。
