# ババ抜き（BabaNuki） <span class="badge done">公開中</span>

トランプ **52枚＋ジョーカー1枚** を配り、同じ数字のペアを捨てながら手札を減らしていく、おなじみの **ババ抜き** です。手札が早く0枚になった人から上がり、最後まで **ジョーカー（ババ）** を持っていた人が負け。**2〜6人** で、対人戦と **CPU練習** を遊べます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🃏 テーブルゲーム（対人戦 / CPU練習）</span></div>
  <div class="quick-card"><span class="label">勝敗</span><span class="value">早く上がった順に順位・最後にジョーカーが残った人が負け</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜6人（CPUで人数を補充可）</span></div>
  <div class="quick-card"><span class="label">メインコマンド</span><span class="value"><code>/babanuki</code>（<code>/baba</code>）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    標準ルールのババ抜きです。カードが配られると **同じ数字のペアは自動で捨て**、手番の人は次の人（席順で隣）の **裏向きの手札から1枚引きます**。ペアができたら自動で捨て、手札が0枚になったら上がり。相手の手札の並びは手番ごとにシャッフルして見せるので、並び順からジョーカーの位置は分かりません。人数が足りないときは **CPU** を入れて練習でき、統合版でも遊べるようスート（マーク）はアイテムの種類と色で表されます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、配り・ペア捨て、カードの引き方、上がりと順位、ジョーカー、形式（対人戦・CPU練習）、参加方法、コマンド。
- **OP・運営向け** … 導入、席・ロビー・看板の設定、管理ツール・管理メニュー、config（人数・時間・CPU）、権限、管理コマンド。
