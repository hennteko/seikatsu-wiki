# MiniGameMenu（ミニゲームメニュー） <span class="badge dev">v1.0 / 新規追加</span>

専用アイテムやコマンドから開く **横断ミニゲームメニュー** です。生活鯖の各ミニゲーム・カジノへの **参加・離脱・開始** を1つのGUIにまとめ、さらにスポーン移動や倉庫アイテム入手などの便利ボタンも備えた、サーバーの入口となるプラグインです。

<div class="quick-grid">
  <div class="quick-card"><span class="label">種別</span><span class="value">🎮 横断メニュー / 導線</span></div>
  <div class="quick-card"><span class="label">開き方</span><span class="value">メニューアイテム右クリック / <code>/mgmenu</code></span></div>
  <div class="quick-card"><span class="label">収録カテゴリ</span><span class="value">ミニゲーム・カジノの2カテゴリ</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
</div>

!!! success "1つのアイテムから全ミニゲームへ"
    入室時に配布される **「生活鯖メニュー」** の紙を右クリックすると、全ミニゲームの一覧GUIが開きます。ゲームを選ぶと **参加 / 離脱 / 開始** のボタン画面が出て、ワンクリックで各ゲームのコマンドが実行されます。導線を覚えなくても、メニューを開けば遊べます。

!!! info "便利ボタンも同梱"
    一覧画面の最下段付近には、初期スポーンや個人スポーン地点への移動、共有倉庫の棒・個人倉庫の本の入手といった **ユーティリティボタン** も並びます。よく使う操作をメニューから直接実行できます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … メニューの開き方、一覧GUIの見方、参加・離脱・開始の操作、便利ボタンの使い方。
- **OP・運営向け** … 導入手順、`/mgmenu` コマンド、config（メニューアイテム・GUI・ゲーム定義・連携コマンド）、権限。

!!! note "仕組み: コマンド代行方式"
    本プラグインは各ミニゲーム本体を **改修しません**。ボタンを押すと config に書かれた既存ゲームのコマンド（例: `kakurenbo join`）を、プレイヤー本人またはコンソールとして代理実行するだけのシンプルな設計です。
