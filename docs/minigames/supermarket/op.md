<div class="audience-banner op">🛠️ OP・運営向けページ — 運営スタッフ向けの導入・設定情報です。遊び方は <a href="player.html">👤 プレイヤー向けページ</a> をご覧ください。</div>

# Supermarket ― OP・運営ガイド { .page-op #supermarket-op }

Supermarket（協力スーパー経営ミニゲーム）の導入・地点／ステーション設定・看板・プリセット・config・権限・管理コマンドをまとめます。地点・ステーション・看板はコマンドまたは **設定ツール（`/supermarket wand`）** で登録すると自動保存されます。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | Supermarket |
| メインコマンド | `/supermarket` |
| api-version | 1.21 |
| 依存プラグイン | なし |
| 設定ファイル | `plugins/Supermarket/config.yml`（全体設定・地点・看板・ステーション）ほかプリセット・商品データ |
| 権限ノード | `supermarket.admin`（既定OP） |

## 導入手順

1. ビルドした `Supermarket` の jar をサーバーの `plugins/` フォルダに配置する。
2. サーバーを起動すると `config.yml` が自動生成される。
3. `/supermarket setlobby`・`/supermarket setstartspawn`・`/supermarket setspawn` でロビー・初期スポーン・ゲームスポーンを設定する。
4. `/supermarket setcustomerspawn` で客の出現地点、`/supermarket setfield 1`・`2` でフィールド範囲を設定する。
5. 搬入口・棚・レジ・ゴミ箱の各ステーションを登録し、看板を設置する。
6. `/supermarket status` で設定状況を確認する。

!!! tip "設定は「設定ツール」が便利です"
    `/supermarket wand` で受け取る設定ツール（右クリックでメニュー）から、**地点設定・ステーション登録・プリセット管理・商品設定** の4メニューをGUIで操作できます。ツールを持っていなくても `/supermarket menu` で同じ管理メニューを直接開けます。地点・ステーション登録はコマンドでも同じ設定ができますが、プリセットの中身と商品データ（仕入/売価/賞味期限/解放日など）の編集は設定ツール専用です。

## セットアップ手順

地点系は **実行した位置**、ステーション・看板系は対象ブロック／看板を **見ながら（6ブロック以内）** 実行します。すべて `supermarket.admin` 権限が必要です。

```text title="設定ツールを受け取る"
/supermarket wand
```

```text title="管理メニューを直接開く"
/supermarket menu
```

```text title="受付ロビー（その場に立って実行）"
/supermarket setlobby
```

```text title="初期スポーン（離脱時の戻り先）"
/supermarket setstartspawn
```

```text title="ゲームスポーン（開始時のプレイヤー配置）"
/supermarket setspawn
```

```text title="客用スポーン（客の出現地点）"
/supermarket setcustomerspawn
```

```text title="フィールド範囲の角1／角2"
/supermarket setfield 1
/supermarket setfield 2
```

```text title="ステーションを登録／解除（対象ブロックを見て実行）"
/supermarket setstation <delivery|shelf|register|trash> [商品ID]
```

!!! note "ステーションの種類"
    `delivery`（搬入口）/ `shelf`（棚）/ `register`（レジ）/ `trash`（ゴミ箱）の4種です。**棚（`shelf`）は商品IDを付けて登録** します（未解放の商品でも登録可）。同じ種類を複数ブロック登録できます。同じブロックにもう一度実行すると登録解除になります。

## 看板の設置

```text title="参加看板を登録（看板を見て実行）"
/supermarket setsign join
```

```text title="離脱看板を登録"
/supermarket setsign leave
```

```text title="開始看板を登録（プリセット識別子を指定）"
/supermarket setsign start <識別子>
```

```text title="ソロ営業看板を登録（クリックでソロメニュー）"
/supermarket setsign solo
```

```text title="視線先の看板の登録を解除"
/supermarket setsign delete
```

## プリセット（難易度・商品構成）

営業のパラメータや商品構成は **プリセット** で管理します。開始時に識別子で選びます。

```text title="プリセットを作成"
/supermarket preset create <識別子>
```

```text title="プリセットを削除"
/supermarket preset delete <識別子>
```

!!! note "プリセットの編集は設定ツールから"
    作成したプリセットの中身（**難易度・営業日数・初期資金・固定費/日・カード払いの割合(%)**、CUSTOM難易度時の最大商品数）は、`/supermarket wand`（または `/supermarket menu`）の **プリセット管理GUI** から編集します。`/supermarket start <識別子>` や開始看板でプリセットを指定して開始します。

!!! info "難易度と最大商品数"
    難易度は `EASY`（最大10商品）/ `NORMAL`（15）/ `HARD`（20）/ `CUSTOM`（5〜20で個別指定）の4種です。商品の解放日（何日目に販売解禁になるか）は各商品ごとの設定で、`/supermarket wand` の **商品設定GUI**（または `products.yml`）で調整します。

## config.yml 設定項目（`settings`）

| キー | 既定値 | 説明 |
|---|---|---|
| `settings.max-players` | 8 | 最大参加人数 |
| `settings.countdown-seconds` | 3 | 開始カウントダウン |
| `settings.prepare-seconds` | 60 | 準備フェーズの長さ（秒） |
| `settings.business-seconds` | 300 | 営業フェーズの長さ（秒） |
| `settings.result-seconds` | 8 | 結果表示の秒数 |
| `settings.shelf-visual-max` | 6 | 棚に見た目として並ぶ最大数 |
| `settings.customer-move-speed` | 0.16 | 客の移動速度 |
| `settings.customer-spawn-interval-seconds` | 8 | 客の出現間隔（秒） |
| `settings.register-wait-warning-seconds` | 30 | レジ待ちの警告を出す秒数 |
| `settings.expire-check-interval-seconds` | 5 | （傷み等の）期限チェック間隔（秒） |
| `settings.coin-rounding` | 10 | 金額の丸め単位 |
| `settings.order-delivery-seconds` | 20 | 発注してから搬入口に届くまでの秒数 |
| `settings.price-change-step` | 10 | 価格変更の刻み幅 |
| `settings.not-carried-satisfaction` | 0.5 | 欲しい商品がどの棚にも無かった（取り扱いなし）客の満足度（0〜1。品切れ0.1・満足1.0の中間） |

!!! note "自動生成される領域（手動編集不要）"
    `lobby-spawn` / `default-spawn` / `game-spawn` / `customer-spawn` / `field` / `signs` / `stations` はコマンド・ツールで自動保存されます。プリセット・商品データも専用ファイルで管理されます。

## ソロ営業（`solo`）

1人でも遊べる **ソロ営業** の設定です。ロビーで配られるメニューアイテム・ソロ看板・`/supermarket solo` から起動し、店舗は通常営業と共用です（誰かが営業中は使えません）。モードごとに自己ベスト・前回結果が `solo-records.yml` に記録されます。

| キー | 既定値 | 説明 |
|---|---|---|
| `solo.enabled` | `true` | ソロ営業（コマンド・看板・メニューアイテム）を使えるか。`false` で無効化 |
| `solo.menu-delay` | 3 | 営業後ロビーに戻ってからソロメニューを開くまでの秒数 |
| `solo.modes.<id>.enabled` | `true` | そのモードを表示するか（使わないモードは削除せず `false` 推奨） |
| `solo.modes.<id>.name` | — | モード表示名（`&` カラーコード可） |
| `solo.modes.<id>.icon` | — | メニューのアイコン（Material 名） |
| `solo.modes.<id>.preset` | `solo_<id>` | 使うプリセット。無ければ起動時に `initial` の値で作成（以後は管理メニューで編集） |
| `solo.modes.<id>.initial.*` | — | 初回作成時の難易度・営業日数・初期資金・固定費/日・`card-ratio`（カード払いの客の割合%） |
| `solo.modes.<id>.customer-interval-multiplier` | — | 客の湧き間隔の倍率（大きいほど客が少ない。通常戦は1.0相当） |
| `solo.modes.<id>.max-customers` | — | 同時に店内にいる客の上限（通常戦は12） |
| `solo.modes.<id>.box-quantity-multiplier` | — | 1箱あたりの個数の倍率（仕入れ値も個数に合わせて増える） |
| `solo.modes.<id>.delivery-seconds` | — | 発注から搬入までの秒数 |
| `solo.modes.<id>.prepare-seconds` / `business-seconds` | — | 準備・営業フェーズの秒数 |

!!! note "既定のソロモード"
    既定で **easy（かんたん）/ normal（ふつう）/ hard（むずかしい）** の3モードが用意されています。モードは最大7個まで。モード名・プリセット名に「`.`」は使えません。`/supermarket status` でモードの設定状況（プリセットの有無・客の量・上限・箱倍率）を確認できます。

## 雇用（`hire`）

準備フェーズ中に搬入口の発注画面から雇える **レジ係** の設定です。雇うと営業中、列が一番長いレジの先頭客を一定秒数ごとに自動会計します。雇用費は1回、日給は閉店時に固定費と一緒に引かれ、状態は1営業ごとにリセットされます。

| キー | 既定値 | 説明 |
|---|---|---|
| `hire.solo` | `true` | ソロ営業で雇用を使えるか |
| `hire.team` | `false` | 通常の営業（複数人）で雇用を使えるか |
| `hire.cashier.name` | `店員` | レジ係の表示名（レジ上の名札に出る） |
| `hire.cashier.levels.<lv>.hire-cost` | Lv1:3000 / Lv2:4000 | 雇用費（1回） |
| `hire.cashier.levels.<lv>.wage-per-day` | Lv1:1500 / Lv2:2500 | 日給（閉店時に引く） |
| `hire.cashier.levels.<lv>.serve-interval-seconds` | Lv1:8 / Lv2:5 | 会計の間隔（この秒数に1人） |

!!! info "既定では雇用はソロ営業のみ"
    `hire.team` は既定 `false` のため、通常の複数人営業では雇用できません。通常営業でも使いたい場合は `true` にします。レベル数は `levels` のキー数で決まります（既定 Lv1・Lv2）。

## レジ会計（`register`）

レジを右クリックしたときの会計方式の設定です。`register.gui: true`（既定）で、スキャン → カード払い（請求額を数字で入力）／現金払い（金種ボタンでおつりを積んで渡す）の **会計画面** が開きます。`false` にすると右クリックだけで会計が済む簡易方式（v0.1 の挙動）に戻ります。カードで払うか現金で払うかは、プリセットの `card-ratio`（カード払いの割合%・既定60）で客ごとに抽選されます。

| キー | 既定値 | 説明 |
|---|---|---|
| `register.gui` | `true` | 会計画面を使う。`false` で右クリックのみの簡易会計 |
| `register.card-mistake-penalty` | 0.34 | カードの打ち間違い1回ごとに下がるレジ操作の評価（1回目で0.66・3回で0） |
| `register.cash-over-satisfaction` | 0.7 | おつりを多く渡したときのレジ操作の評価（多い分は店の損） |
| `register.cash-short-satisfaction` | 0.1 | おつりが足りなかったときのレジ操作の評価 |
| `register.exact-cash-chance` | 30 | 現金客がぴったり払う確率(%)。残りは1000円単位・5000円札・1万円札で払う |

## お客様（`customers`）

| キー | 既定値 | 説明 |
|---|---|---|
| `customers.basket-size-weights` | `1:55 / 2:30 / 3:12 / 4:3` | 1人が買う個数（個数:重み）の抽選。1〜9個まで指定可。未設定なら1個 |

## 評価（`rating`）

終了時・1日ごとの総合★評価は、4つの内訳（待ち時間・価格・品揃え・レジ操作）の満足度を集め、`rating.weights` で重み付け平均して ★1.0〜5.0 にします。重みを0にするとその内訳を評価に入れません。

| キー | 既定値 | 説明 |
|---|---|---|
| `rating.weights.wait` | 1.0 | レジ待ちの長さ（`settings.register-wait-warning-seconds` を超えると下がる） |
| `rating.weights.price` | 1.0 | 値段（標準価格より高いと下がる・高すぎて買わなかった客も含む） |
| `rating.weights.stock` | 1.0 | 品揃え（品切れ・取り扱いなしで下がる） |
| `rating.weights.register` | 1.0 | レジ操作（カードの打ち間違い・おつりの過不足で下がる） |

## 値段と購入（`pricing`）

客は棚の前で値段を見て、タイプ（太っ腹／標準／シビア）ごとの許容範囲を超えると確率で買うのをやめます。

| キー | 既定値 | 説明 |
|---|---|---|
| `pricing.refuse-strength` | 1.0 | 高くて買うのをやめる強さ（1.0=標準／0=値段で買うのをやめない／2.0=厳しめ） |
| `pricing.refused-satisfaction` | 0.2 | 高すぎて買わなかったときの価格の評価（0〜1） |

## 店の評判（`reputation`）

1日の★評価が翌日の **店の評判**（★1.0〜5.0）に混ぜられ、評判が高いほど翌日の客が増えます（★3.0で等倍）。

| キー | 既定値 | 説明 |
|---|---|---|
| `reputation.start` | 3.0 | 営業開始時の評判（★1.0〜5.0） |
| `reputation.weight` | 0.5 | 閉店時にその日の★評価をどれだけ評判に混ぜるか（0〜1） |
| `reputation.customer-effect` | 0.15 | 評判★1つごとに増減する客の多さ（0.15なら★5.0で+30%／★1.0で-30%） |

## 通常営業の人数補正（`team-scaling`）

通常戦（複数人）の客の多さは「ソロ1人分 × 参加人数」で決まります。基準にするソロモードの客設定を人数ぶんスケールさせます。

| キー | 既定値 | 説明 |
|---|---|---|
| `team-scaling.per-player-mode` | normal | 基準にするソロモードのID（その `customer-interval-multiplier` を人数で割り、`max-customers` を人数倍にする） |
| `team-scaling.fixed-cost-per-player` | 0.0 | 1人増えるごとに増える固定費の割合（0なら人数に関係なくプリセットのまま。0.10なら4人で+30%） |

## 管理コマンド

| コマンド | 説明 |
|---|---|
| `/supermarket wand` | 設定ツールを入手（右クリックで管理メニュー） |
| `/supermarket menu` | 管理メニューを直接開く |
| `/supermarket setlobby` / `setstartspawn` / `setspawn` / `setcustomerspawn` | 各地点を設定 |
| `/supermarket setfield <1\|2>` | フィールド範囲の角を設定 |
| `/supermarket setstation <種類> [商品ID]` | ステーションを登録／解除 |
| `/supermarket setsign <join\|leave\|start <識別子>\|solo\|delete>` | 看板を設定／解除（`solo`＝ソロ営業看板） |
| `/supermarket preset <create\|delete> <識別子>` | プリセットの作成／削除 |
| `/supermarket reload` | 設定ファイルの再読み込み |
| `/supermarket stop` | 営業を強制終了（在籍者全員を解散） |
| `/supermarket forceunlock <プレイヤー> [confirm]` | 参加中フラグの強制解除（コンソール可） |
| `/supermarket status` | 設定状況・現在の状況を確認（全員可） |

## 権限ノード

| 権限ノード | 既定 | 用途 |
|---|---|---|
| `supermarket.admin` | OP | 設定ツール・地点／ステーション設定・看板設置・プリセット管理・強制終了など管理系すべて |

!!! info "権限不要で全員が使えるコマンド"
    `/supermarket join`・`/supermarket leave`・`/supermarket ready`・`/supermarket start <識別子>`・`/supermarket solo [モード]`・`/supermarket status` は権限チェックがなく、全プレイヤーが使えます。

## トラブルシューティング

??? failure "ゲームが開始できない"
    `/supermarket status` を確認してください。開始には **ロビー・初期スポーン・ゲームスポーン・客用スポーン、および各ステーション（搬入口・棚・レジ）** が設定されている必要があります（フィールド範囲は任意・未設定でも開始可）。開始にはプリセットの指定（`start <識別子>`）と、ロビーに参加者がいることも必要です。

??? failure "客が商品を買わない・棚から取らない"
    棚（`shelf`）に商品IDが正しく割り当てられ、搬入口から補充できているか確認してください。棚が空だと客は購入できません。

??? failure "設定を変えたのに反映されない"
    営業パラメータは `config.yml` の `settings`、難易度・商品構成はプリセットで管理されます。プリセットは `/supermarket wand`（または `/supermarket menu`）のGUIから編集してください。

---

## 全ゲーム共通の改修（2026-09）

全ミニゲーム共通の改修が入り、本ゲームにも適用されています。

- **名前表示** … 参加中はチャット名・Tabリスト名・頭上の名札が「【ゲーム名】名前」になり、離脱で元に戻ります（表示名は config の `display-name`）。
- **参加/離脱の全体告知** … 参加・離脱時にサーバー全体へ「【ゲーム名】名前 が参加しました (N人)」等を通知します。
- **同時参加は1ゲームまで** … 他ゲームに参加中は参加が拒否されます（「【○○】に参加中です」）。異常で参加ロックが残った場合はOPが `/<コマンド> forceunlock <プレイヤー>` で解除できます。
- **退避データのファイル保存** … ロビー入場時に退避した所持品を `plugins/<プラグイン>/vault/<UUID>.yml` に保存し、**サーバークラッシュ後の再ログインでも復元** します（退避・復元は各1回、試合終了時は復元しません）。

### 追加された config キー

| キー | 説明 |
|---|---|
| `display-name` | ゲーム表示名（「【…】」の中身） |
| `messages.join-broadcast` | 参加の全体告知文 |
| `messages.leave-broadcast` | 離脱の全体告知文 |
| `messages.already-in-other-game` | 他ゲーム参加中に拒否したときの文言 |
| `messages.vault-save-failed` | 持ち物の退避に失敗して参加を中止したときの文言 |

!!! note "config は自動で追記されるようになりました"
    起動時（`reload` 対応プラグインは reload 時も）に、`config.yml` へ不足している項目を既定値＋コメント付きで自動追記します（既存の値は変更しません／更新時は `config.yml.bak` を保存）。**このため、当ページ内の「既存サーバーは config が自動追記されません」という旧記載は本改修で解消されています。** 座標・看板・会場・ステージなどのデータ領域は補完対象外です。

---

[← 👤 プレイヤー向けページへ](player.md){ .md-button }
[← Supermarket 概要へ](index.md){ .md-button }
