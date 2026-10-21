# RedstoneMover <span class="badge done">公開中</span>

**レッドストーン回路（音ブロック演奏）の速さに合わせて、乗り物を経路に沿って動かす** ギミックプラグイン。プレイヤーを乗せた乗り物（可動体）が、回路のクロックや音ブロックの演奏テンポに同期して進みます。音楽が速ければ速く、ゆっくりならゆっくり動く「演奏ライド」を、看板・ボタンひとつで誰でも体験できます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎢 乗り物・演奏連動ギミック</span></div>
  <div class="quick-card"><span class="label">動力</span><span class="value">レッドストーン回路（クロック / 音ブロック演奏）</span></div>
  <div class="quick-card"><span class="label">乗車方法</span><span class="value">乗り場の看板・ボタンを右クリック</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.2）</span></div>
</div>

!!! note "このギミックについて"
    RedstoneMover は、運営が設置した **可動体（乗り物）** を、プレイヤーが乗り場から体験するプラグインです。ミニゲームではなく、勝敗や得点はありません。乗り物は **レッドストーン回路の動き** に同期して経路を進みます。センサーには、クロック（信号のON/OFF）に合わせて進む **CLOCK** と、経路沿いの **音ブロックの演奏** に合わせて進む **NOTE** の2方式があります。`/rmover` 系コマンドはすべて **OP 専用**（`redstonemover.admin`）で、プレイヤーは乗り場の看板・ボタンから乗車します（`redstonemover.ride`・既定 true）。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 乗り物の乗り方、出発までの流れ、運行中の動き、降車、よくある質問。
- **OP・運営向け** … 導入、可動体の作成・経路・センサー（CLOCK / NOTE）・見た目・座席・乗り場・開始ブロックの設定、config、権限、管理コマンド。
