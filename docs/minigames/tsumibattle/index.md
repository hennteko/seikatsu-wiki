# どうぶつ積みバトル（TsumiBattle） <span class="badge done">公開中</span>

**順番に動物のかたちのピースを土台へ積み上げ**、自分の番にタワーを **崩した人／ピースを落とした人が負け** になる、2〜4人用のテーブル・パーティーゲームです。カメ・ウシ・パンダなどのピースが1手ごとに配られ、左右移動・回転・落下だけで慎重に積みます。最後まで残った1人が勝ち、タワーが高さの上限に届いたら引き分けです。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧩 テーブル・パーティー（2〜4人・手番制）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">最後まで崩さず残った1人（上限到達で引き分け）</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜4人（config で調整）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    手番が回ってくると、動物のピースが1つ配られてタワーの上に出現します。**左へ／右へ／回転／落とす** の操作アイテムで位置を決めて落とすだけ。ピースが着地するとタワーの **安定判定** が働き、重心が支えの範囲からはみ出すと崩れます。崩した人・自分のピースを土台の下まで落とした人はその場で負け、残りで続行します。持ち時間（既定20秒）を過ぎると、その位置で自動的に落ちます。勝ち・負け・最高タワー高さは記録され、`/tsumi top` とランキング看板で確認できます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、手番の流れ、操作アイテム、ピースの種類、崩れ・落下の負け条件、記録とランキング、参加方法、コマンド。
- **OP・運営向け** … 導入、初期スポーン・ロビー・アリーナ（原点・観戦席・幅・土台・高さ上限）の設定、看板、ピースの定義（pieces.yml）、config、権限、管理コマンド。
