# ステップダンス（StepDance） <span class="badge done">公開中</span>

座席に座り、壁を流れてくる **4色のノーツ** が一番下の光る判定ラインに来た瞬間に、**ホットバーの 1〜4 番** を押してタイミングを合わせるリズム（音ゲー）ミニゲームです。曲は `.nbs`（Note Block Studio）・`.mid`（MIDI）ファイルから自動で譜面を生成し、最大4人で同時に **点数を競います**。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🎵 リズムゲーム（スコアアタック）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">曲が終わったときの合計点が高い人が上位</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">1〜4人（ブースの数まで・ロビー最大12人）</span></div>
  <div class="quick-card"><span class="label">操作</span><span class="value">ホットバーの 1・2・3・4 番キー</span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    参加者はブース（座席）に座り、目の前の壁を上から流れてくる **ノーツ**（赤・青・黄緑・黄の4レーン）を見ます。ノーツが一番下の **判定ライン** に重なった瞬間に、同じ色のレーンに対応する **ホットバー 1〜4 番** を押すと、ずれの小ささに応じて **PERFECT / GREAT / GOOD / MISS** の判定が出て点数が入ります。曲は `plugins/StepDance/songs/` に置いた `.nbs`・`.mid` から **自動で譜面** が作られ、難易度（EASY / NORMAL / HARD）でノーツの密度が変わります。開始時には **選曲画面** が開き、曲と難易度を選べます（初回起動時にスターター曲7曲が同梱）。曲が終わると点数ランキングと判定の内訳が出て、**ALL PERFECT / FULL COMBO** も表示されます。1人でも遊べます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、操作（1〜4キー）、判定と点数、譜面と難易度、結果の見方、参加・離脱、コマンド。
- **OP・運営向け** … 導入、アリーナ・ブース（座席と壁）・看板の設定、曲（.nbs）の追加、config（人数・時間・壁・判定・ping 補正）、権限、管理コマンド。
