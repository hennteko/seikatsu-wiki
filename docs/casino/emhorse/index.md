# EmeraldHorse（育成馬） <span class="badge done">公開中</span>

CasinoPlugin の **育成馬システム** です。子馬券から自分専用の愛馬を手に入れ、エサで育てて強くし、既存の **競馬（HorseRacing）** に出走させて賞金を狙います。ウマ娘風に「素質グレード＋育成レベル＋専門化＋体調」で馬を育てるのが特徴です。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎰 カジノ・ギャンブル（CasinoPlugin モジュール）</span></div>
  <div class="quick-card"><span class="label">所持数</span><span class="value">1人最大5頭（既定 / config で変更可）</span></div>
  <div class="quick-card"><span class="label">共通通貨</span><span class="value">エメラルド（銀行口座）</span></div>
  <div class="quick-card"><span class="label">前提</span><span class="value">CasinoPlugin の bank・horse モジュール</span></div>
</div>

!!! info "育成馬とは"
    マイクラの馬エンティティではなく **永続データ** として愛馬を保持します（召喚された馬は表示用の使い捨てで、性能はデータ側に保存。馬が消えても育成内容は失われません）。子馬券で馬を入手 → エサで育成 → 競馬に出走、という流れで遊びます。**複数頭（既定で最大5頭）を所有でき**、厩舎で「選択中の馬」を切り替えて育成・出走させます。**交配などの一部機能は将来対応予定** です。

!!! warning "銀行モジュールが必須"
    育成馬は通貨にエメラルド（CasinoPlugin の銀行＝EmeraldAPI）を使います。bank モジュールが有効でないと起動しません。出走は競馬（horse）モジュールと連携します。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 子馬券・育成（エサ）・召喚と騎乗・体調・複数頭の切り替え・競馬への出走の流れ。
- **OP・運営向け** … 看板設置・`/emhorse` コマンド・`emhorse.yml` 設定・権限。
