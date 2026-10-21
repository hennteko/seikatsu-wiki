# 椅子取りゲーム（MusicChair） <span class="badge done">公開中</span>

音楽が流れている間はステージを歩き回り、**曲が止まったら椅子に座る** 定番の椅子取りゲームです。椅子は毎ラウンド **生存者よりも1脚少なく** 置かれ、座れなかった人が脱落。最後に残った1人が優勝です。曲は **.nbs（Note Block Studio）ファイル** を自由に追加でき、ラウンドが進むほどテンポが速くなったり、曲が一瞬だけ止まる **フェイクストップ** で引っかけたりと、本家さながらの駆け引きが楽しめます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎵 パーティー（サバイバル勝ち抜き）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">最後まで座り続けて残った1人</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">3〜16人（既定。下限2／上限50）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    各ラウンドは **曲が流れるフェーズ → 曲が止まって座るフェーズ** の順で進みます。曲の間は椅子の近くに **立入禁止エリア** ができ、椅子のそばで待ち伏せしようとすると弾き飛ばされます。立ち止まりすぎると警告・強制移動が入るので、ちゃんと歩き回る必要があります。曲が止まったら、置かれた **椅子ブロックの上に乗る** と座れます。座れなかった人は脱落し、生存者の視点で観戦します。椅子は毎ラウンド「生存者 − 1脚」。誰も座れなかったラウンドはやり直し、生存者が1人になれば優勝です。曲は `.nbs` を入れるだけで増やせ、ラウンドごとの加速・フェイクストップ・曲名表示・一番乗り演出なども設定で切り替えられます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、ラウンドの流れ、椅子への座り方、立入禁止エリアと立ち止まり対策、フェイクストップと加速、脱落と観戦、参加方法、コマンド。
- **OP・運営向け** … 導入、`.nbs` 曲の追加、ステージ・開始位置・椅子・フィールド・看板の設定、config（人数・時間・椅子・演出・報酬）、権限、管理コマンド。
