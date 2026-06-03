<div class="audience-banner op">🛠️ OP・運営向けページ — Effectsaidan は管理者（OP）専用のプラグインです。</div>

# Effectsaidan ― OP・運営ガイド { .page-op }

<span class="badge done">公開中</span>

ネザライトブロックで作る「ネザライトの祭壇」を起動装置として、サーバー全体に時間制バフ（ポーション効果・カスタム効果）を付与する自作プラグインです。プレイヤーがビーコンを設置した瞬間にネザライト製ピラミッドが完成していると、ピラミッドとビーコンが消滅し、代わりに専用の「ネザライトの通帳」（エンチャント本）が配布されます。通帳を右クリックすると効果選択GUIが開き、効果と秒数を入力するとCasinoPluginの **EmeraldAPI（エメラルド銀行）** から自動でエメラルドが引き落とされ、サーバー全体にバフが適用されます。OPは `/aidan open` で同じGUIを直接開くこともできます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Effectsaidan |
| バージョン | 1.0 |
| api-version | `26.1.2` |
| メインコマンド | `/aidan open` ／ `/aidan apply <ID> <秒数>` |
| 依存（depend） | **CasinoPlugin**（必須） |
| 連携API | `jp.casinoplugin.api.EmeraldAPI`（bank モジュール） |
| 設定ファイル | `plugins/Effectsaidan/config.yml` |
| 起動装置 | ネザライトブロック製ピラミッド（4段）の頂上にビーコンを設置 |
| 配布アイテム | 「ネザライトの通帳」（エンチャント本、PDC タグで識別） |

!!! info "CasinoPlugin が必須です"
    起動時に `EmeraldAPI.isReady()` を呼び、CasinoPlugin の bank モジュールが初期化済みかを検証します。CasinoPlugin が未導入、または bank モジュールが起動していない場合は、Effectsaidan は自動的に自身を `disablePlugin` してシャットダウンします。

## プラグインの仕組み

### 起動装置（ネザライトの祭壇）

- プレイヤーが **ビーコン** を設置した瞬間、`BeaconListener` がそのビーコンの真下に **4段のネザライトブロック製ピラミッド** が組まれているかを判定します（各段は `2 × layer + 1` 辺の正方形）。
- 条件を満たしていた場合は、ビーコン設置イベントをキャンセルしたうえでピラミッドとビーコンを消滅させ、設置者に専用の「ネザライトの通帳」を1冊渡します。
- 通帳は `Material.ENCHANTED_BOOK` で、`PersistentDataContainer`（NamespacedKey: `effectsaidan:effect_book`）でタグ付けされているため、表示名を変えても識別可能です。

### GUIによる効果付与

- 通帳を右クリック、または OP が `/aidan open` を実行すると、6行（54スロット）の専用GUI「ネザライトの祭壇 - 効果選択」が開きます。
- GUI上に並ぶアイテムをクリック → チャットで秒数（時間乗数）を入力すると、`(秒数) × (コスト倍率)` をエメラルドで支払い、サーバー全体にバフが適用されます。
- 「`cancel`」と入力するとキャンセルできます。

### CasinoPlugin との連携（エメラルド支払）

- 支払処理は `EmeraldAPI.getBalance(uuid)` で残高を取得し、必要額を差し引いた値を `EmeraldAPI.setBalance(uuid, newBalance)` で書き戻す方式です。
- 残高不足時の挙動は `ALLOW_NEGATIVE_BALANCE` で制御します（既定: 取引キャンセル）。

### 効果の重ね掛けとレベル

- 同じ効果を再度購入すると、`MAX_EFFECT_LEVEL` までレベルが上昇します。上限到達後は **時間のみ延長** されます。
- レベル上昇時の残時間は、`(現残時間 × 現レベル + 追加秒数) / 新レベル` の式で再計算されます（コード上の `calculateNewDuration`）。

### 効果の維持

- 1秒ごとに残時間をデクリメントし、ポーション系効果は **2秒ごと（視覚系は20秒ごと）** に全オンラインプレイヤーへ再付与してパーティクル・アイコン点滅を抑制します。
- プレイヤーログイン時には `PlayerConnectionListener` が現在有効な効果を即時適用、ログアウト時には攻撃力モディファイヤをクリーンアップします。
- 効果終了時は赤色プレフィックスで全体ブロードキャストされます。

## 導入手順

1. **CasinoPlugin を先に導入** し、bank モジュール（EmeraldAPI）を初期化しておく。
2. `Effectsaidan-1.0.jar` を `plugins/` に配置する。
3. サーバーを起動すると `plugins/Effectsaidan/config.yml` が自動生成される。
4. 起動ログに「`Effectsaidanが有効化されました。CasinoPlugin (EmeraldAPI) と連携済み。`」が出れば成功。
5. 設定を変更した場合はサーバー再起動で反映（リロードコマンドは未実装）。

!!! warning "CasinoPlugin が無いと起動しません"
    `verifyCasinoPluginAPI()` で CasinoPlugin の存在と EmeraldAPI の準備完了を検証し、失敗するとプラグインを無効化します。

## config.yml の設定項目

```yaml
# --- 経済設定 ---
MAX_DURATION: 3600              # 一度に入力できる秒数の上限
ALLOW_NEGATIVE_BALANCE: false   # false なら残高不足時に取引をキャンセル

# --- レベル上限設定 ---
MAX_EFFECT_LEVEL: 4             # 効果の最大レベル（4 = Lv.5まで上昇可）

# --- 効果コスト設定（1秒あたりのエメラルド消費倍率） ---
EFFECT_COSTS:
  EXP_BOOST: 1.5
  SATURATION: 0.5
  ATTACK_BOOST: 2.0
  # ...（17効果分）
```

| キー | 既定値 | 説明 |
|---|---|---|
| `MAX_DURATION` | `3600` | 1回のGUI入力／`apply`コマンドで指定できる秒数の上限 |
| `ALLOW_NEGATIVE_BALANCE` | `false` | `false`なら残高不足時に取引をキャンセル、`true`ならマイナス残高を許容 |
| `MAX_EFFECT_LEVEL` | `4` | 効果の最大内部レベル（表示上は `Lv.5` まで） |
| `EFFECT_COSTS.<効果キー>` | 各効果ごと | 効果キー別の `エメラルド/秒` 倍率。総額 = `ceil(秒数 × 倍率)` |

## 効果一覧（全17種）

各効果のID（`/aidan apply` で指定）・コスト倍率・初期レベルです。

### 通常効果（PotionEffectベース）

| 効果ID | 表示名 | コスト倍率 | 既定レベル | 内部処理 |
|---|---|---:|---|---|
| `REGENERATION` | ＨＰ自動回復 | 2.5 | Lv.1 | `PotionEffectType.REGENERATION` |
| `RESISTANCE` | 耐性２ | 3.0 | Lv.2 | `PotionEffectType.RESISTANCE` |
| `HASTE` | 採掘速度上昇1.5倍 | 1.8 | Lv.2 | `PotionEffectType.HASTE` |
| `SPEED_BOOST` | 移動速度上昇 | 1.5 | Lv.2 | `PotionEffectType.SPEED` |
| `NIGHT_VISION` | 暗視 | 1.2 | Lv.1 | `PotionEffectType.NIGHT_VISION`（視覚系） |
| `WATER_BREATHING` | 水中呼吸 | 1.8 | Lv.1 | `PotionEffectType.WATER_BREATHING`（視覚系） |
| `FIRE_RESISTANCE` | 火炎耐性 | 2.0 | Lv.1 | `PotionEffectType.FIRE_RESISTANCE`（視覚系） |
| `JUMP_BOOST` | 跳躍力上昇 | 1.3 | Lv.2 | `PotionEffectType.JUMP_BOOST` |
| `LUCK` | 幸運 | 3.0 | Lv.1 | `PotionEffectType.LUCK` |
| `INVISIBILITY` | 透明化 | 5.0 | Lv.1 | `PotionEffectType.INVISIBILITY` |

### カスタム効果（独自実装）

| 効果ID | 表示名 | コスト倍率 | 内部処理 |
|---|---|---:|---|
| `EXP_BOOST` | 経験値２倍 | 1.5 | `PlayerExpChangeEvent` で `(2 + level)` 倍 |
| `SATURATION` | 満腹度常時回復 | 0.5 | 1秒ごとに食料・隠し満腹度を回復 |
| `ATTACK_BOOST` | 攻撃力上昇２倍 | 2.0 | `Attribute.ATTACK_DAMAGE` に `baseDamage × (1+level)` 加算 |
| `DROP_RATE_BOOST` | ドロップ率２倍 | 2.5 | `BlockDropItemEvent` でアイテムを `(2 + level)` 倍化 |
| `MOB_LOOT_BOOST` | モブドロップ増加 | 2.8 | プレイヤーキル時のドロップと経験値を `(2 + level)` 倍化 |
| `EMERALD_FINDER` | エメラルド発見 | 3.5 | 鉱石採掘時に `5 + level×3`%でエメラルドを追加ドロップ |
| `AUTO_REPAIR` | 自動修繕 | 4.0 | 5秒ごとに装備・手持ちの耐久値を `1 + level` 回復 |

!!! tip "視覚系効果のちらつき対策"
    `NIGHT_VISION` / `WATER_BREATHING` / `FIRE_RESISTANCE` の3つは「視覚系」として、20秒ごとの再付与・アイコン非表示・パーティクル非表示で扱われるため、画面の点滅が発生しません。

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/aidan open` | 効果選択GUI「ネザライトの祭壇 - 効果選択」を開く（プレイヤー専用） |
| `/aidan apply <効果ID> <秒数>` | GUIを介さずサーバー全体に効果を直接適用（**エメラルド消費なし**、コマンドブロック対応） |

!!! note "`/aidan apply` はコマンドブロック向けの無料適用"
    `apply` サブコマンドは `EffectManager.applyServerWideEffect()` を直接呼ぶため、**エメラルド支払はスキップ** されます。イベント開放・テスト用途を想定したコマンドであり、通常はGUIから運用してください。

タブ補完は `open` / `apply` と、`apply` 後の効果ID（`EXP_BOOST` 等）・秒数候補（`60` / `300` / `3600`）に対応しています。

## 権限ノード

| ノード | 既定 | 効果 |
|---|---|---|
| `effectsaidan.command.use` | OP | `/aidan` 各サブコマンドの使用権限（実装側で `sender.hasPermission(...)` を判定） |

!!! note "コマンドそのものの `permission: op`"
    `plugin.yml` で `aidan` コマンドの `permission` には `op` 文字列が指定されています（Bukkit の一般的な権限ノードではなく、`PluginCommand#testPermissionSilent` で OP のみ通過する扱い）。実装側のサブコマンド判定は `effectsaidan.command.use` を見るため、権限プラグインで非OPに `effectsaidan.command.use` を付与しただけでは `/aidan` 自体が弾かれます。コマンドを一般プレイヤーに開放したい場合は、権限プラグイン側で `effectsaidan.command.use` を付与しつつ、`plugin.yml` の `aidan` コマンドの `permission: op` を見直す必要があります。

## トラブルシューティング

??? failure "起動時に `CasinoPlugin (EmeraldAPI) が見つからない / 未初期化のため、シャットダウンします。` と出る"
    CasinoPlugin 本体が読み込まれていない、または bank モジュール（EmeraldAPI）が起動していません。CasinoPlugin の jar が `plugins/` にあること、CasinoPlugin 側のログで bank モジュールが有効化されていることを確認してください。

??? failure "ビーコンを置いてもネザライトの祭壇が起動しない"
    判定は **ビーコンの真下に4段のピラミッド**（各段 3×3 / 5×5 / 7×7 / 9×9 が **すべてネザライトブロック**）で、`BlockPlaceEvent` 発火の瞬間に成立している必要があります。1ブロックでも別素材があると無効です。途中まで組んで最後にビーコンを置く運用にしてください。

??? warning "残高不足で効果が付かない"
    `ALLOW_NEGATIVE_BALANCE: false` の状態では `EmeraldAPI.getBalance()` の残高が必要額（`ceil(秒数 × 倍率)`）未満だと取引がキャンセルされます。CasinoPlugin の銀行残高を確認するか、`true` に変更して再起動してください。

??? warning "最大入力時間を超えました と出る"
    `MAX_DURATION`（既定 3600 秒）を超える秒数は受け付けません。`config.yml` で上限を引き上げてサーバーを再起動してください。

??? question "通帳（エンチャント本）を紛失した／2冊目を入手したい"
    通帳はネザライトピラミッド + ビーコン設置で再度入手できます。OP は `/aidan open` で同等のGUIを開けるため、通帳が無くてもGUI操作は可能です。

??? question "効果のレベルが上がらず時間だけ伸びる"
    `MAX_EFFECT_LEVEL`（既定 4 = 内部レベル0〜4、表示上 Lv.1〜Lv.5）に達した効果は、それ以上レベル上昇せず時間延長のみになります。仕様通りの動作です。

??? question "config を編集しても反映されない"
    リロードコマンドは実装されていません。`config.yml` の変更後はサーバー再起動が必要です。
