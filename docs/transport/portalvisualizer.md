<div class="audience-banner op">🛠️ OP・運営向けページ — PortalVisualizer は Multiverse-Portals のポータルをパーティクル表示する自作プラグインです。</div>

# PortalVisualizer ― OP・運営ガイド { .page-op }

<span class="badge custom">自作プラグイン</span>

[Multiverse-Portals](../utility/multiverse-portals.md) が作成したポータルの **範囲（領域）をパーティクルで可視化** する、生活鯖の自作プラグインです。透明なポータル枠を見える化することで、設置位置の確認・通行ミス防止・運営時の管理を容易にします。

!!! info "Multiverse-Portals 必須・セット運用"
    本プラグインは [Multiverse-Portals](../utility/multiverse-portals.md) の API を利用してポータル情報を取得します。Multiverse-Portals が未導入の場合、PortalVisualizer は自動で無効化されます。**2つはセットで運用** してください。

## 基本情報

| 項目 | 値 |
|---|---|
| プラグイン名 | PortalVisualizer |
| バージョン | `${project.version}`（ビルド時に置換） |
| api-version | 26.1.2 |
| 種別 | 生活鯖の自作プラグイン |
| メインコマンド | `/portalvisualizer`（alias `/pv`） |
| 依存 | **Multiverse-Portals**（必須・`depend`） |
| 設定ファイル | `plugins/PortalVisualizer/config.yml` |
| 作者 | MattariMinecraft |

## 導入手順

1. [Multiverse-Portals](../utility/multiverse-portals.md) を先に導入しておく（必須依存）。
2. `PortalVisualizer-x.x.x.jar` を `plugins/` フォルダに配置する。
3. サーバーを再起動する。
4. 初回起動時に `plugins/PortalVisualizer/config.yml` が生成される。設定変更後は `/pv reload` で反映できる。

!!! warning "Multiverse-Portals が無いと起動しない"
    起動時に Multiverse-Portals が見つからない場合、`Multiverse-Portals が見つかりません！プラグインを無効化します。` というログを出して自身を無効化します。

## config.yml 設定項目

| キー | 既定値 | 説明 |
|---|---|---|
| `render-mode` | `outline` | 描画モード（後述）。`outline` / `corners` / `fill` から選択 |
| `particle-interval` | `15` | パーティクル描画間隔（tick単位、20tick = 1秒）。大きいほど軽量 |
| `view-distance` | `30` | ポータルが見える距離（ブロック単位）。プレイヤーがこの距離内にいるポータルだけ描画される |
| `particle-color.red` | `128` | パーティクル色 R 成分（0–255） |
| `particle-color.green` | `0` | パーティクル色 G 成分（0–255） |
| `particle-color.blue` | `255` | パーティクル色 B 成分（0–255） |
| `particle-density` | `0.5` | パーティクル密度（ブロック間隔）。値が小さいほど密、大きいほど軽量 |
| `particle-size` | `1.2` | パーティクルのサイズ（0.5–3.0） |
| `default-enabled` | `true` | プレイヤーがログインした時に可視化をデフォルトで有効にするか |

### 描画モード（`render-mode`）

| 値 | 説明 |
|---|---|
| `outline` | **枠線のみ** 描画（既定・推奨）。ポータル領域の12辺を描画する |
| `corners` | 8つのコーナーに **L字マーカー** のみ描画する **最軽量** モード |
| `fill` | 平面ポータル（厚さ1ブロック）を **塗りつぶし** 描画。立体ポータルは枠線にフォールバック。**高負荷・統合版非推奨** |

### ポータルごとの個別カラー設定

`portals` セクションで、ポータル名をキーに **個別の色とサイズ** を指定できます。既定値の例は config.yml にコメントで記載されています。

```yaml
portals:
  my_portal:
    color:
      red: 255
      green: 215
      blue: 0
    size: 1.5
```

未指定のポータルには `particle-color` / `particle-size` の既定値が使われます。

## コマンド一覧

メインコマンドは `/portalvisualizer`、エイリアスは `/pv` です。

| コマンド | 説明 | 必要権限 |
|---|---|---|
| `/pv toggle` | 自分の可視化表示を ON/OFF 切り替える | `portalvisualizer.use` |
| `/pv on` | 自分の可視化表示を **ON** にする | `portalvisualizer.use` |
| `/pv off` | 自分の可視化表示を **OFF** にする | `portalvisualizer.use` |
| `/pv info` | 登録ポータル数・各ポータルの座標・現在の描画モード／間隔／視認距離／密度を表示 | `portalvisualizer.admin` |
| `/pv reload` | `config.yml` を再読み込みし、描画タスクを再起動する | `portalvisualizer.admin` |

!!! tip "ON/OFF はプレイヤー単位"
    可視化のON/OFFはプレイヤーごとに管理されます。パーティクルが邪魔なときは `/pv off` で自分だけ非表示にできます。`default-enabled: false` にすると、ログイン時はOFFがデフォルトになります。

## 権限ノード

| 権限 | 既定 | 効果 |
|---|---|---|
| `portalvisualizer.use` | `true`（全員） | 自分の可視化の ON/OFF 切り替え（`toggle` / `on` / `off`） |
| `portalvisualizer.admin` | `op` | `reload`・`info` の実行 |

## 注意点・トラブルシューティング

??? failure "プラグインが起動しない / すぐ無効化される"
    Multiverse-Portals が未導入・未起動の可能性があります。Multiverse-Portals を先に導入し、サーバーを再起動してください。ログに `Multiverse-Portals が見つかりません！` と出ていれば原因はこれです。

??? failure "パーティクルが見えない"
    1. `/pv off` で自分が非表示にしていないか確認（`/pv on` で再表示）。
    2. ポータルから **`view-distance`（既定 30 ブロック）** より遠い場合は描画されません。近づいてみてください。
    3. `/pv info` でポータルが登録されているか確認してください（登録数 0 なら Multiverse-Portals 側で `/mvp create` 未実施）。
    4. クライアントのパーティクル設定が「最小」や「無効」になっていないか確認してください。

??? failure "描画が重い・サーバー負荷が高い"
    - `render-mode` を `corners` に変更すると **最軽量** になります。
    - `particle-interval` を大きくする（例: 20–30）と描画頻度が下がり軽量化します。
    - `particle-density` を大きくする（例: 1.0）と粒の数が減ります。
    - `view-distance` を小さくする（例: 15–20）と対象プレイヤーが減ります。
    - `fill` モードは特に重く、**統合版プレイヤーには非推奨** です。

??? failure "新しく作ったポータルが表示されない"
    `/pv reload` を実行してください。`portals` セクションの個別色設定を変更した場合も同様にリロードが必要です。

??? failure "info コマンドで「MultiversePortalsApi が見つかりません」と出る"
    Multiverse-Portals が正しく起動していない可能性があります。サーバーログで Multiverse-Portals 側のエラーを確認し、必要なら再起動してください。

## 関連ページ

- [Multiverse-Portals](../utility/multiverse-portals.md) ― ポータル本体を作成・管理するプラグイン（本プラグインの必須依存）
