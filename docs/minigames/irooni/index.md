# Irooni（色鬼・改造版） <span class="badge dev">v1.0 / 新規追加</span>

鬼が選んだ **色のコンクリート床だけが残り、それ以外の床が消える** 残機制サバイバルゲームです。安全色の上に乗っていないと床が抜けて落下し、残機を失います。制限時間まで生き残ったプレイヤーは全員勝利、全員が残機0になれば鬼の完全勝利です。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">⚔️ 対戦・アクションミニゲーム</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2人以上（鬼1人＋逃走者）</span></div>
  <div class="quick-card"><span class="label">1試合の長さ</span><span class="value">既定3分（180秒）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! success "ゲームの特徴（色当て × 床消滅 × 残機制）"
    鬼はホットバーの **染料を右クリック** して「安全な色」を選びます。猶予（既定2秒・`floor-delay` で調整可）のあと、**選ばれた色以外のコンクリート床が一斉に消滅** し、その色に乗れていないプレイヤーは下へ落下します。落ちると残機が1減り、初期スポーンへ復帰。残機0で観戦モードになります。安全色は常に **鬼が選んだ1色のみ**。時間経過で床の復活間隔が短くなり、終盤は **フェイント（予告色から別色へ切替）** も解禁されます。

!!! info "操作はシンプル（看板＋コマンド）"
    参加・離脱・鬼立候補・開始は **看板クリック** で行えます。`/colortag join` / `leave` / `start` のコマンドでも参加・離脱・開始が可能（全員が使用可・権限不要）。鬼に選ばれると染料が配られ、**右クリックで色を発動** するだけ。逃走者は安全色の床へ走って乗るだけのシンプル操作です。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 役割、安全色の見方、落下と残機、鬼の色操作、勝敗、流れ、成績確認。
- **OP・運営向け** … スポーン/ステージ/フィールド設定（床は自動生成）、看板、config（パレット・床消滅時間・難易度カーブ・人数）、`/colortag` コマンド（`stop`・`setmax/min`・`stats` 含む）、権限、成績記録。
