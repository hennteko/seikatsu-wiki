# 攻城戦（SiegeWar） <span class="badge done">公開中</span>

**攻撃側 vs 防御側** に分かれ、攻撃側は城門・城壁を破って城の奥にある **コア** を壊せば勝ち、防御側は制限時間までコアを守りきれば勝ちの、チーム対戦ミニゲームです。生活鯖ワールドにある既存の城をそのまま舞台に使い、試合中に自由な建築・破壊で攻防を繰り広げ、試合が終わるとステージ（城）は試合前の状態へ **自動で復元** されます。

<div class="quick-grid">
  <div class="quick-card"><span class="label">カテゴリ</span><span class="value">🏰 チーム対戦（攻撃側 vs 防御側）</span></div>
  <div class="quick-card"><span class="label">勝利条件</span><span class="value">攻撃側＝コア破壊 / 防御側＝時間まで防衛</span></div>
  <div class="quick-card"><span class="label">人数</span><span class="value">最低4人〜最大40人（config で変更可）</span></div>
  <div class="quick-card"><span class="label">メインコマンド</span><span class="value"><code>/siege</code></span></div>
  <div class="quick-card"><span class="label">対象バージョン</span><span class="value">Paper 1.26.x（api-version 26.1.2）</span></div>
  <div class="quick-card"><span class="label">統合版</span><span class="value">対応（Floodgate / Geyser-Spigot）</span></div>
</div>

!!! note "ゲームの特徴"
    試合が始まると参加者は **攻撃側（6）：防御側（4）** の比率で自動的に分かれ、まず **準備フェーズ（既定2分）** で防御側だけが城内に建築して守りを固めます（攻撃側は出撃地点から出られません）。続く **攻城フェーズ（既定15分）** で攻撃側が出撃し、城門や城壁を壊して奥へ進み、**コア** を殴り続けて HP を0にすれば攻撃側の勝ち、守りきれば防御側の勝ちです。攻撃側は役職に応じた装備で一斉出撃を繰り返し、防御側はコアの近くで **防衛ボーナス** を得たり修理兵が城門を直したりして守ります。壊された城は試合後に自動で元へ戻るため、生活鯖の既存の城をそのまま舞台にできます。

## ページを選ぶ

[👤 プレイヤー向けページ](player.md){ .md-button .md-button--primary }
[🛠️ OP・運営向けページ](op.md){ .md-button }

- **プレイヤー向け** … 遊び方、準備・攻城フェーズの流れ、コア破壊・防衛、延長戦、建築と破壊、役職（キット）、修理ハンマー、死亡とリスポーン、統合版、コマンド。
- **OP・運営向け** … 導入、ステージ範囲・スポーン・コア・看板の設定、管理ツール、ステージ復元、試合中の保護、config、権限、管理コマンド。
