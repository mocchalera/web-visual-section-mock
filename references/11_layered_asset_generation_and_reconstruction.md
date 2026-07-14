# references/11_layered_asset_generation_and_reconstruction.md

## 目的

承認済みのWEBセクション画像を、あとから編集でき、HTML/CSS実装の素材としても再利用できる**レイヤー／パーツ素材一式**へ変換するための実行リファレンスです。

このフェーズの完了条件は、素材がそれらしく分かれていることではありません。指定された順序・座標・合成条件で全再構成レイヤーを重ねた結果が、基準画像とデコード後のRGBAピクセルで完全一致することです。

`10_visual_decomposition_for_web_build.md` が「何をどう分けるか」の設計を担当し、このファイルは「どう生成・書き出し・再合成・検証するか」を担当します。

## 使う場面

- ユーザーがレイヤー分け、パーツ分け、後編集、PSD/Figma的な扱いやすさを求めている
- 生成したカンプをHTML/CSSでコーディングするときの素材にしたい
- 背景、人物、商品、アイコン、手書き文字、装飾を別々に差し替えたい
- 素材一式を集めれば元のセクション画像を完全に再現できる必要がある
- 既存の生成画像を、単なるフラット画像ではなく実装可能なアセットパッケージへ変換したい

純粋なムード探索、未承認のラフ、完成画像だけが必要な依頼では、このフェーズを省略できます。

## 二系統を同時に持つ

完全再現と自由な編集は、同じ素材だけで両立しない場合があります。必ず次の二系統を区別します。

### A. reconstruction layers

基準画像を完全再現するための正本です。

- 基準画像と同じ幅・高さ・原点・色空間を使う
- PNGなどのロスレスRGBAで保持する
- 透明領域を含むフルキャンバス、またはmanifestで位置が一意に決まる切り抜きにする
- z-order、座標、opacity、blend mode、transformを固定する
- 基準画像から見えるピクセルの所有者を曖昧にしない
- 文字やSVG/CSS候補にも、完全再現用のラスターレイヤーを残す

### B. reusable assets

移動、差し替え、レスポンシブ再構成、HTML/CSS実装のための素材です。

- 余白をtrimした透過PNG
- 背景を除去し、移動してもハローが出にくい人物・商品切り抜き
- SVG化またはCSS化しやすい曲線、矢印、下線、アイコン
- 実テキスト化するための文言、書体候補、サイズ、行高、字間、改行仕様
- CSS background、`<img>`、inline SVG、実テキストなどの実装候補

reusable assetsだけで完全一致を主張してはいけません。完全一致の証明はreconstruction layersで行い、両者の対応をmanifestに記録します。

## 出力モード

```text
composite_only:
  セクション画像のみ。レイヤー生成なし。

layer_plan_only:
  分解計画と生成プロンプトのみ。実ファイルや完全一致は未検証。

layered_exact:
  reconstruction layers、reusable assets、manifest、再合成画像、差分レポートまで出力。
```

後編集またはHTML/CSS素材化が明示された場合の既定は `layered_exact` です。ツールが透過書き出し、ピクセル比較、ファイル保存に対応しない場合は `layer_plan_only` へ安全に縮退し、`fidelity_unproven` と明記します。

## フェーズ0: 基準合成画像を固定する

まず、承認済みのセクション画像を `canonical_composite.png` として固定します。

記録するもの:

```text
section_id:
canonical_file:
canonical_sha256:
canvas_width_px:
canvas_height_px:
color_space: sRGB
pixel_format: RGBA8 など
origin: top-left
approval_state:
```

以後のレイヤー生成中に、基準画像を再生成、拡大縮小、トリミング、色補正してはいけません。PNGファイル自体のハッシュはメタデータや圧縮方式で変わり得るため、完全一致判定はファイルバイトではなくデコード後のRGBAピクセルで行います。

## フェーズ1: layer ledgerを作る

奥から手前へ、すべての可視要素に所有者を与えます。

推奨レイヤー種別:

```text
00_base_plate
10_background_structure
20_background_motif
30_subject_or_product
40_cards_or_ui
50_decorative_raster
60_icons_and_hand_lettering
70_readable_text_snapshot
80_cta_and_labels_snapshot
90_reconstruction_residual_optional
```

各行で決めるもの:

```text
layer_id:
semantic_role:
source_strategy: extract | inpaint_underlay | regenerate | vector_rebuild | real_text_spec
reconstruction_file:
reusable_asset_files:
z_index:
bbox_px: [x, y, width, height]
anchor:
opacity:
blend_mode:
transform:
responsive_owner:
must_be_editable:
must_match_exactly:
```

`source_strategy` を曖昧にしません。完全一致が必要なアイコン、手書き文字、ロゴ風文字、人物の見えている部分は、独立に似たものを再生成するのではなく、基準画像から抽出またはピクセル所有レイヤーとして派生させます。

## フェーズ2: 下層素材から生成する

生成順は、原則として奥から手前です。

1. 上層の人物、商品、文字、アイコン、手書き装飾を含まないクリーンな背景プレートを生成またはinpaintする。
2. 背景の色面、巨大モチーフ、曲線、パターンを分ける。
3. 人物、商品、UI、カードを透過素材として分ける。
4. アイコン、手書き文字、矢印、下線、スタンプ、バッジを独立素材として分ける。
5. 読ませる文字とCTAは、完全再現用ラスタースナップショットと編集用テキスト仕様を両方持つ。
6. 再合成して差分を検査する。

背景プレートの、上層素材に隠れる領域はinpaintして構いません。ただし、上層素材を重ねた最終可視ピクセルは基準画像と一致しなければなりません。

## フェーズ3: 再構成用と再利用用を書き出す

### 再構成用

- もっとも安全なのは、基準画像と同じサイズの透明フルキャンバスPNGです。
- trimする場合は、整数ピクセルの `x`, `y`, `width`, `height` をmanifestへ記録します。
- 既定の合成は `normal`, opacity `1`, transformなしです。別条件が必要なら必ず明記します。
- resampling、サブピクセル配置、自動色変換を禁止します。

### 再利用用

- 人物、商品、アイコン、手書き文字は、余白をtrimした透過素材も用意します。
- 移動利用する素材は、元背景色が縁に残らないようalpha edgeを確認します。
- SVG/CSS候補は、完全再現用ラスターレイヤーを削除せず、代替実装として添えます。
- 実テキスト候補は、正確な文言、改行、フォント候補、weight、size、line-height、letter-spacing、alignmentを記録します。

## フェーズ4: assembly manifestを作る

最小manifest例:

```json
{
  "contract_version": "layered-section-assets/v1",
  "section_id": "section_01_hero",
  "canonical": {
    "file": "composite/canonical_composite.png",
    "width": 1440,
    "height": 900,
    "color_space": "sRGB",
    "pixel_format": "RGBA8",
    "origin": "top-left"
  },
  "reconstruction_layers": [
    {
      "id": "L-00-base",
      "file": "layers/reconstruction/00_base_plate.png",
      "z_index": 0,
      "x": 0,
      "y": 0,
      "opacity": 1,
      "blend_mode": "normal",
      "reusable_asset_ids": ["A-background"]
    }
  ],
  "reusable_assets": [
    {
      "id": "A-background",
      "file": "assets/reusable/background_plate.png",
      "role": "css-background",
      "source_layer_ids": ["L-00-base"]
    }
  ],
  "verification": {
    "comparison": "decoded-rgba",
    "pixel_diff_count": 0,
    "max_channel_delta": 0,
    "status": "complete_exact"
  }
}
```

manifestは実装コードではなく、素材を誰がどこへ置けば元画像になるかを一意にする契約です。

## フェーズ5: 完全一致を検証する

検証順:

```text
1. canonicalと全reconstruction layerを同じ色空間のRGBAへデコードする
2. manifestのz-order、整数座標、opacity、blend modeで合成する
3. canonicalとrecomposedの幅・高さを比較する
4. 全ピクセル・全RGBAチャンネルを比較する
5. pixel_diff_countとmax_channel_deltaを記録する
6. recomposed.png、diff.png、fidelity-report.jsonを保存する
```

完了条件:

```text
same_dimensions = true
pixel_diff_count = 0
max_channel_delta = 0
```

SSIM、PSNR、見た目、目視レビュー、低解像度サムネイル一致は、完全一致の代替になりません。

## residual correction layerの制限

アンチエイリアス境界などの最終補正に `90_reconstruction_residual_optional` を使えますが、これは編集可能性を偽装する逃げ道ではありません。

- フルフレームの基準画像コピーを入れない
- 人物、商品、カード、読ませる文字、完全なアイコンなど意味のある要素全体を隠さない
- 補正対象ピクセル数と対象領域をレポートする
- 意味のある領域がresidualに残る場合は、該当レイヤーの抽出をやり直す
- residualを含めたゼロ差分と、residualが非意味的な境界補正だけであることの両方を満たす

## 文字、アイコン、手書き素材の扱い

### 読ませる文字

- 完全再現用: 元画像の見た目を保つラスターレイヤー
- 編集用: 実テキスト仕様
- HTML/CSS用: font stack、改行、size、weight、line-height、letter-spacing、color、position

### 手書き文字、矢印、下線、スタンプ

- 完全再現用: 基準画像から派生した透過ラスターレイヤー
- 編集用: trim済み透過PNG
- 実装候補: SVGトレースまたはCSS。近似しかできない場合は完全再現用ラスターレイヤーを残す

### アイコン

- 単純形状はSVG/CSS候補にする
- 独特の質感や手描き感がある場合は透過PNGも残す
- 再生成した「似たアイコン」を完全再現素材として扱わない

## HTML/CSS実装への引き渡し

素材ごとに推奨実装先を記録します。

```text
full-bleed background plate -> CSS background-image / picture
person or product cutout -> img / picture
simple curve or wave -> SVG / CSS mask / pseudo-element
hand lettering -> transparent PNG or traced SVG
readable copy and CTA -> real HTML text
UI or card snapshot -> img for fidelity, HTML/CSS rebuild as a separate implementation task
```

レスポンシブでは座標そのものより、次の関係を引き渡します。

- コンテナ左端に追従
- セクション中央線に追従
- 人物の顔をコピーの右端へ合わせる
- CTAと見出しのベースライン関係を維持
- 全幅背景はcover、主役切り抜きはcontain
- 装飾だけは画面端でcrop可

## 推奨パッケージ構造

```text
section_01_hero/
  composite/
    canonical_composite.png
  layers/
    reconstruction/
      00_base_plate.png
      30_subject.png
      60_hand_lettering.png
      70_text_snapshot.png
  assets/
    reusable/
      background_plate.png
      subject_cutout.png
      hand_lettering.png
      icon.svg
  editable/
    text-spec.json
    svg-css-candidates.md
    responsive-relationships.md
  assembly-manifest.json
  proof/
    recomposed.png
    diff.png
    fidelity-report.json
```

## ステータス契約

```text
complete_exact_editable:
  ゼロ差分を証明し、意味のある各要素が独立レイヤーまたは編集仕様を持つ。

fidelity_unproven:
  素材はあるが、RGBA比較を実行できていない。

reconstruction_mismatch:
  再合成結果に1ピクセル以上の差分がある。

editability_failed:
  ゼロ差分でも、フル画像コピーや大きなresidualで実質的に編集できない。

blocked_tooling:
  透過書き出し、ファイル保存、ピクセル比較など必要なツール能力がない。
```

`complete` という曖昧な語だけで終えません。完全一致と編集可能性の両方を別々に判定します。

## セルフチェック

```text
承認済みcanonical compositeを固定したか:
[yes/no]

すべての可視要素にlayer ownerがあるか:
[yes/no]

背景等の下層素材から順に生成したか:
[yes/no]

アイコン、手書き文字、人物、商品を独立配置できるか:
[yes/no]

reconstruction layersとreusable assetsを区別したか:
[yes/no]

manifestで座標、z-order、opacity、blend modeが一意か:
[yes/no]

decoded RGBAでpixel_diff_count = 0か:
[yes/no]

max_channel_delta = 0か:
[yes/no]

residualが意味のある要素を隠していないか:
[yes/no]

実テキスト、SVG/CSS候補、レスポンシブ関係を引き渡したか:
[yes/no]
```
