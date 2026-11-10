# ネコマス（Nekomasu） <span class="badge done">公開中</span>

色分けされた床の盤面に、**ルールを守ってネコを置いていく** ロジックパズルです。N×N の盤面に N 匹のネコを、「1色エリアに1匹・1行1列に1匹・ネコ同士は隣り合わない」の3ルールを全部守って置けばクリア。答えは必ず1つに決まります。**ソロ（レベル制）・対戦（早解き）・協力（みんなで1面）** を遊べます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🐈 ロジックパズル（ソロ / 対戦 / 協力）</span></div>
  <div class="quick-card"><span class="label">ルール</span><span class="value">1色1匹・1行1列1匹・隣接禁止（周囲8マス）</span></div>
  <div class="quick-card"><span class="label">難易度</span><span class="value">かんたん 5×5 / ふつう 6〜7 / むずかしい 8 / 鬼 9</span></div>
  <div class="quick-card"><span class="label">メインコマンド</span><span class="value"><code>/nekomasu</code>（<code>/neko</code>）</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    床に色分けされた N×N の盤面があり、同じ色のマスが1つの「色エリア」です。N 匹のネコを **①1つの色エリアに1匹 ②1つの行・列に1匹 ③ネコ同士は隣り合わない（斜めも含めた周囲8マス）** の3ルールを全部守って置けばクリア。答えは必ず1つに決まります。間違ったマスに置くと即ミスでライフが減り、0で失格。メモ用の「×印」はいくら付け外ししてもミスになりません。1人でレベルを順に解く **ソロ**、同じ問題を自分の盤面で解く **対戦**、1つの盤面をライフ・ヒント共有で解く **協力** を遊べます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … ルール、遊び方、道具（ネコ・×ペン・ヒント・自動×）、各モードと決着、観戦、参加方法、コマンド。
- **OP・運営向け** … 導入、初期スポーン・ロビー・盤面・看板の設定、管理ツール・管理メニュー、ソロのレベル（puzzles.yml）、難易度、config、権限、管理コマンド。
