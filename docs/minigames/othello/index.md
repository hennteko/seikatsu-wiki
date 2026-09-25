# オセロ（Othello） <span class="badge done">公開中</span>

ブロックで作られた **実物大のオセロ盤** で対局する、定番のボードゲームです。盤面を見て置きたいマスをクリックするだけで石を置け、はさんだ相手の石が自動でひっくり返ります。**対人戦（勝ち残り）** と **CPU戦（かんたん／ふつう／むずかしい）** に対応。マス数・石の色・盤の演出まで設定できます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">♟️ ボード・テーブルゲーム</span></div>
  <div class="quick-card"><span class="label">形式</span><span class="value">対人戦（1対1・勝ち残り）／CPU戦</span></div>
  <div class="quick-card"><span class="label">1手の制限時間</span><span class="value">既定30秒</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! note "ゲームの特徴"
    ブロックで敷かれた **実物大オセロ盤** で対局します。石を置ける距離（既定32ブロック）から **マスをクリック** すると石を置け、はさんだ相手の石が **アニメーションでめくれます**。置けるマスは自分にだけ緑の粒で表示（ヒント）、直前の一手は光って分かりやすくなっています。1手ごとに制限時間（既定30秒）があり、時間切れは「ランダムに置く」か「負け」を選べます。**対人戦は勝者が続けて出場（勝ち残り）**、**CPU戦は3段階の強さ** から選べます。成績・勝利数のランキングも記録されます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、石の置き方、対人戦とCPU戦、勝敗、成績、参加方法、コマンド。
- **OP・運営向け** … 導入、盤・スポーン・看板設定、盤の敷設（buildboard）、config（盤面・演出・CPU）、権限、管理コマンド。
