# ゴッドフィールド（GodField） <span class="badge done">公開中</span>

HP・MP・¥（お金）を持ち寄り、毎ターン **神器（カード）を授かって** 武器・奇跡・雑貨を使い合う、ゴッドフィールド風のターン制カードバトルです。最後まで生き残った人（チーム戦なら最後まで残ったチーム）が勝ち。**最大9人** で、**個人戦・チーム戦・CPU戦** を遊べます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🃏 ターン制カードバトル（個人戦 / チーム戦 / CPU戦）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">最後まで生き残った人（チーム戦は最後まで残ったチーム）</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">2〜9人（CPUで人数を補充可）</span></div>
  <div class="quick-card"><span class="label">メインコマンド</span><span class="value"><code>/godfield</code>（<code>/gf</code>）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    各プレイヤーは **HP・MP・¥** を持ち、自分の番が来るたびに **神器** を授かります。番では **攻撃（武器）・奇跡（MPを使う）・雑貨・祈る** のどれか1つを行い、攻撃された人は制限時間内に **防具** を選んで受けます。神器には **属性（火・水・木・土・光・闇）** があり、相性で防げたり防げなかったりします。病（風邪→熱病→地獄病→天国病）や霧・閃光・夢・暗雲といった **災い**、¥での **買う・売る・質入れ・両替** など、かけひきの要素が満載です。所定のラウンドを過ぎると **終末の時** が訪れ、神器を授かるたびに悪魔などが現れるようになります。人数が足りないときは **CPU** を入れて1人でも遊べます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、番の流れ、HP/MP/¥、神器の種類と属性、攻撃と防御、奇跡・雑貨、買う・売る・質入れ・両替、災い、終末の時、形式（個人戦・チーム戦・CPU戦）、参加方法、コマンド。
- **OP・運営向け** … 導入、席・ロビー・看板の設定、管理ツール・管理メニュー、config（人数・時間・ルール・終末・CPU）、`cards.yml`、権限、管理コマンド。
