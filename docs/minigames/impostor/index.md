# アモアス（Impostor） <span class="badge done">公開中</span>

宇宙船を舞台にした **人狼系の推理ゲーム**（Among Us 風）です。多くの **クルー（船員）** の中に隠れた少数の **インポスター（偽物）** がいて、クルーはマップ各所の **タスク** をこなし、インポスターは **こっそりキル** してバレないように立ち回ります。死体を見つけたら通報、怪しいときは緊急ボタンで **会議** を開き、話し合って投票で追放。インポスターは **サボタージュ** や **ベント（通気口）移動** で妨害・移動します。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🕵️ 人狼・推理（宇宙船人狼）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">クルー＝全タスク完了 or 偽物を全員追放／インポスター＝人数で上回る or 原子炉妨害</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">4〜15 人（偽物は AUTO で1〜3人）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    試合が始まると、全員が **クルー** か **インポスター** に分かれます（偽物の数は人数で自動調整、既定で〜7人:1人・8〜11人:2人・12人〜:3人）。クルーは宇宙船の各所に配られた **タスク**（カードを通す・配線をつなぐ・スキャンなど、チェスト画面やしゃがみ・右クリックで行う作業）を全部終わらせれば勝ち。インポスターは **キル**（クールタイムつき）でクルーを減らし、人数で並べば勝ちです。死体を見つけたら **通報**、怪しければ **緊急ボタン** で会議を開き、**話し合い → 投票** で1人を追放します。インポスターは **サボタージュ**（停電・原子炉の緊急事態・ドア封鎖）で妨害したり、**ベント（通気口）** で素早く移動して疑いをかわします。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、役職、タスクのやり方、キルと通報、緊急ボタンと会議・投票、サボタージュ、ベント移動、参加方法、コマンド。
- **OP・運営向け** … 導入、マップ（開始地点・会議席・追放先・緊急ボタン・部屋・タスク・ベント・ドア・修理）の設定、看板、config（人数・役職・タスク・キル・サボタージュ・会議・色）、権限、管理コマンド。
