# ブロックフォール（BlockFall） <span class="badge done">公開中</span>

ワールドに建てた大きな盤面の前に座り、上から落ちてくる **7種類のピース** を回して並べ、**横一列をそろえて消していく** 落ち物パズルのミニゲームです。1人で遊べる **マラソン**（スコアアタック）と **スプリント**（40ラインのタイムアタック）があり、記録はランキングに残ります。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧩 パズル（スコアアタック・タイムアタック）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">マラソン＝高いスコア／スプリント＝40ラインを早く消す</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">1人（ブースの数だけ同時に遊べる）</span></div>
  <div class="quick-card"><span class="label">操作</span><span class="value">ホットバーの 1〜9 番キー（Java 版は移動キーも可）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    開始看板を押すと、空いている **ブース** の座席に座ります。目の前の **横10×縦20の盤面** にピースが落ちてくるので、ホットバーのキーで **左右に動かす・回す・落とす** をして並べます。横一列がそろうとその列が消え、一度にたくさん消すほど高得点です。10ライン消すごとに **レベル** が上がって落ちるのが速くなります。落下先の目安（ゴースト）、次に来るピース（ネクスト）、1つ取っておける **ホールド** もあります。

!!! info "対戦モードは今後追加予定"
    2〜4人でお邪魔ラインを送り合う対戦モードは、第2段階で追加する予定です。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、操作（ホットバー・移動キー）、モード、スコア、一時停止、ランキング、参加・離脱、コマンド。
- **OP・運営向け** … 導入、ブース（プリセット）の建て方、看板、管理ツール、config、権限、管理コマンド、トラブルシューティング。
