# BoatGP <span class="badge dev">開発中 / Phase 2</span>

マリオカート風の **氷ボートレースミニゲーム**。プラグイン駆動の「カート方式」で常に前進し、**左右移動キー（A/D）でのステアリング** でチェックポイントを通過しながら規定周回を走り、順位を競います。操舵はプラグインが制御する **入力ベース操舵** で、Java版・統合版どちらも同じ操作です。コース上のアイテムボックスでアイテムを拾い、こうら・バナナ・スターなどで競り合う **アイテムバトル** に対応しました。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🏁 レース・タイムアタック</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">最大12人 (最少1〜2人・設定依存)</span></div>
  <div class="quick-card"><span class="label">1レースの長さ</span><span class="value">既定3周 (1周30〜60秒目安)</span></div>
  <div class="quick-card"><span class="label">アイテム</span><span class="value">6種 (こうら・バナナ・スター等)</span></div>
</div>

!!! warning "開発状況について"
    BoatGP は現在 **Phase 2（アイテムバトル）** まで実装済みです。カート物理・路面システム・チェックポイント判定・順位HUDに加え、**アイテムバトル（バナナ・こうら・スター・サンダー）／アイテムボックス／ロケットスタート** が動作します。**ベストタイム記録・リーダーボード・カートスキンなどは Phase 3 で実装予定** です。このWIKIは現在のソースコードに基づくガイドのため、今後のアップデートで内容が変わる場合があります。

!!! info "セットアップが刷新されました（運営向け）"
    サーキット設定コマンドが **`/race <サブ>` のフラット形式（自動保存）** になり、チェックポイント／ゴールは **A→Bの2点で引く「ライン（ゲート）」方式** に変更されました。さらに **看板での参加・離脱・開始**（`/race setsign` / `setstart`）、**初期スポーン**（`setstartspawn`）、**レース終了後の連続プレイ** に対応しています。なお **`/race join` / `leave` / `start` / `status` / `list` は全員が実行可能**（設定系コマンドのみ `boatgp.admin` が必要）で、看板はそれを補助する手段です。看板の設置・解除は **`/race setsign` コマンド専用**（看板への直接書き込みは廃止）になりました。詳細は OP・運営向けページを参照してください。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、レースのルール、アイテムの使い方、ゲームの流れ、コマンド、FAQ。
- **OP・運営向け** … 導入手順、config.yml、items.yml、サーキット作成手順、権限、管理コマンド。
