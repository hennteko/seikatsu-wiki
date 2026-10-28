# マインスイーパー（Minesweeper） <span class="badge done">公開中</span>

マスの地面に敷かれた **実物大のマインスイーパー** を、シャベルで開き、旗を立てて攻略するミニゲームです。ソロでタイムを競うほか、**対戦（よーいドンで別々の盤面を競う）**・**協力（みんなで1つの盤面を攻略）**・**交代制（順番に1マスずつ）** の4モードを1つのステージ単位で遊べます。地雷を踏まないよう、数字を手がかりに安全マスを読み解きましょう。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧩 テーブル・パズル（ソロ / 対戦 / 協力 / 交代制）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">安全マスを全部開けたら成功（地雷を踏むと失敗・脱落）</span></div>
  <div class="quick-card"><span class="label">難易度</span><span class="value">初級 9×9 / 中級 16×16 / 上級 30×16</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    地面がマインスイーパーの盤面になっていて、**シャベル** でマスを開き、**旗** で地雷の場所に印を付けます。開いたマスには、周囲8マスの地雷の数が数字（色ブロック＋数字表示）で現れ、`0` のマスは連鎖して自動で開いていきます（波紋の演出つき）。**最初に開けたマスは必ず安全**（周囲も空く）なので、運任せの初手になりません。数字マスをシャベルで押すと、周りに立てた旗の数が数字と同じときに残りを一括で開く **コード開き** も使えます。モードは **ソロ（タイム記録）/ 対戦（人数分の盤面を競う）/ 協力（共有盤面をライフ制でみんなで）/ 交代制（順番に1マスずつ）** の4種類です。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、シャベルと旗の操作、コード開き、4モードの違い、難易度、ソロメニュー、ランキング、参加方法、コマンド。
- **OP・運営向け** … 導入、ステージ（モード＋難易度）と盤面の設置、看板、config（難易度・モード・進行・ブロック）、権限、管理コマンド。
