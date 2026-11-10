# ナンプレ（Sudoku） <span class="badge done">公開中</span>

床に **実物大の 9×9 盤面** を敷き、ホットバーの数字を持ち替えてマスに入れていく、おなじみの **ナンプレ（数独）** です。問題は毎回自動で作られ、答えは必ず1つに決まります。**ソロ（タイムアタック）・対戦（同じ問題で早解き）・協力（1つの盤面を全員で）・陣取り（共有盤面で取り合い）** の4モードを遊べます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🧩 ロジックパズル（ソロ / 対戦 / 協力 / 陣取り）</span></div>
  <div class="quick-card"><span class="label">モード</span><span class="value">ソロ・対戦・協力・陣取り</span></div>
  <div class="quick-card"><span class="label">難易度</span><span class="value">初級・中級・上級（自動生成・解は一意）</span></div>
  <div class="quick-card"><span class="label">メインコマンド</span><span class="value"><code>/sudoku</code></span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    床に敷かれた 9×9 の盤面を歩きながら、ホットバーに配られた **1〜9 の数字** を持ち替えてマスを右クリック（統合版はタップ）で埋めていきます。各行・各列・各 3×3 ブロックに 1〜9 を1つずつ入れればクリア。問題は自動で作られ **答えは必ず1つ** に決まり、人の解き方だけで解けるよう難易度調整されています。間違えるとミスになり、モードごとに決めた回数でアウト。1人でタイムを競う **ソロ**、同じ問題を自分の盤面で解く **対戦**、1つの盤面を全員で埋める **協力**、1マスずつ取り合う **陣取り** を遊べます。数字は額縁の地図で表示し、統合版でも見え方が揃うよう作られています。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、数字の入れ方・メモ、マスの見分け方、各モードのルール、観戦、ソロのランキング、参加方法、コマンド。
- **OP・運営向け** … 導入、初期スポーン・ロビー・盤面・看板の設定、管理ツール・管理メニュー、問題の作り方、config（人数・時間・難易度・盤面）、権限、管理コマンド。
