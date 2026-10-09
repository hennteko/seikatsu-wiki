# PvPバトル <span class="badge done">公開中</span>

**FFA（全員対戦）・チーム戦（先取ラウンド制）・大将戦（勝ち抜き）** の3モードを1つのアリーナで切り替えて遊べる、PvP ミニゲームです。参加者はロビーに集まり、開始看板やコマンドでモードを選んで開戦。装備は config のローダウトから自動で配られ、持ち物は参加時に安全に預かられます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ PvP（個人戦・チーム戦の両対応）</span></div>
  <div class="quick-card"><span class="label">モード</span><span class="value">FFA / チーム戦 / 大将戦 の3種</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">最低2人〜最大24人（config で変更可）</span></div>
  <div class="quick-card"><span class="label">メインコマンド</span><span class="value"><code>/pvpbattle</code>（別名 <code>pvpb</code>）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! note "ゲームの特徴"
    1つのアリーナで3つのモードを切り替えて遊べます。**FFA** は全員が敵の勝ち抜きデスマッチで、フィールド上のランダム地点にリスポーンしながら制限時間内のキル数を競います（同率トップはサドンデス）。**チーム戦** は赤・青に分かれ、各ラウンドで相手チームを全滅させて先取ラウンド数に達したチームの勝ち（リスポーンなし）。**大将戦** は各チームが出場順を決めて1対1で戦う勝ち抜き戦で、相手の大将まで倒し切ったチームの勝ちです。参加時に元の持ち物はファイルに退避され、離脱・終了で元どおり戻ります。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 3モードの遊び方、参加・開始方法、装備、サドンデス、大将戦の手順、コマンド。
- **OP・運営向け** … 導入、ロビー・スポーン・フィールド・看板の設定、モード別の設定差分、config、権限、管理コマンド。
