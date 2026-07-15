# references/11_layered_asset_generation_and_reconstruction.md

## 目的

承認済みのWEBセクション画像を参考にしながら、あとからHTML/CSSへ配置できる**クリーンな画像素材をパーツごとに再生成する**ための実行リファレンスです。

ここでいうレイヤー分けは、完成済みの複合画像を矩形で切り出したり、マスクで抜いたり、見えているピクセルを所有レイヤーへ配分したりすることではありません。それでは隠れていた背景や人物の輪郭が復元されず、隣接する文字・装飾・背景色も混入するため、移動可能な実装素材になりません。

このフェーズの主成果物は、元モックのピクセルを再配置した再構成レイヤーではなく、**必要なラスタ表現を、独立して振る舞う意味単位で再生成したproduction assets**です。

## 最重要契約

```text
1. 元モックはvisual referenceであり、production assetのpixel sourceではない。
2. 生成前に、見える物体を独立挙動で意味単位へまとめ、その単位を raster_regenerate / css_svg_rebuild / real_text / omit に分類する。
3. raster_regenerateだけを画像生成する。
4. 1回の生成につき1つの出力ビジュアル単位。無関係な素材をsprite sheetへまとめないが、一緒にしか動かない複数物体は一つの `card_artwork_plate` または `transparent_scene` にしてよい。
5. raster promptを書く前に `section_field` / `floating_scene` / `contained_artwork` / `tone_merged_object` / `source_visible_frame` のどれかをsource evidence付きで固定する。
6. セクション全体の世界観を作る主役シーンは、copy space込みの `full_field_scene_plate` として生成する。小さな不透明カードへ縮退させない。
7. 人物・商品・物体・複雑なイラストは、独立配置が必要な場合だけ透過背景で再生成する。複数物体のexploded view、コラージュ、端末群などが一緒に浮く場合は、影を含む一つの `transparent_scene` にする。静的カード内に閉じる場合は背景・影・画面・接触物を含む不透明プレートを優先する。
8. 空間・面・光・質感だけを下層として使う場合は、上層要素のないクリーンな背景プレートとして再生成する。
9. 元モックの切り抜き、マスク抽出、残差パッチをproduction assetとして納品しない。
10. 構造的な文字、CTA、機能UI、ライブデータと、独立調整が必要なCSS/SVG形状はラスター生成しない。装飾文字や非機能の画面アートは作品プレートへ含めてよい。
```

## 使う場面

- 生成済みモックを `mockup-to-code` などの実装工程へ渡したい
- 人物、商品、背景、写真、複雑なイラストを別々に動かしたい
- 文字や装飾が重なっている部分も、汚染のない素材へ戻したい
- PCとスマホで素材位置やcropを変えたい
- 元モックでは隠れていた人物・商品・背景の領域を自然に補完したい

完成画像だけが必要な場合や、まだ構図探索中のラフでは省略できます。

## 出力モード

```text
composite_only:
  セクション画像だけを生成する。

layer_plan_only:
  asset triage、個別生成プロンプト、実装仕様だけを作る。実ファイルは未生成。

layered_clean:
  実装に必要なラスタ素材を個別再生成し、テキスト仕様、CSS/SVG仕様、manifest、cleanliness reportを出力する。実装引き継ぎの既定。

layered_clean_plus_reference_proof:
  layered_cleanに加え、明示的に要求された場合だけpixel-exactな参照再構成を別ディレクトリへ出す。参照再構成物をproduction assetsへ混ぜない。
```

透過出力、ファイル保存、または画像検査ができない場合は `layer_plan_only` へ縮退し、生成済みと報告してはいけません。

## 4分類の判断

分類は物体ごとではなく、先に決めた `generation_unit` ごとに行います。

```text
full_field_scene_plate: セクション全体の世界観を所有する主役シーン。coupled objectsと環境を含め、構造コピー用のcopy spaceを持つ。
clean_background_plate: 下層の空間・環境だけ。上層の主役や構造要素を含めない。
transparent_foreground: 独立して移動、重なり、crop、再利用する単体の人物・商品・物体。
transparent_scene: 一緒に浮く複数物体、exploded diagram、端末群、コラージュ、接地影をまとめた透過シーン。
card_artwork_plate: 一つのカード内で常に一緒に動く写真、静物、デバイスモック、コラージュ、抽象作品。
code_native: HTML text / CSS / SVGが所有する構造。
```

### `raster_regenerate`

画像として生成し直す価値がある要素です。

- 人物、髪、衣服、肌、動物
- 商品、複雑な物体、工芸品、料理
- 写真背景、室内、風景、都市、空
- 複雑なイラスト、コラージュ、3D、質感のある抽象表現
- 光、煙、反射、布、紙、粒子など、CSS/SVGでは費用対効果が悪い表現

### `css_svg_rebuild`

画像生成しません。仕様だけを渡します。

- 単純な円、矩形、線、罫線、グラデーション
- 波、blob、曲線マスク、単純な矢印、下線、丸囲み
- 一般的なアイコン、バッジ、カード枠、ボタン形状
- 単色または少数色の幾何学パターン
- テキストで再現できる巨大英字・漢字モチーフ

独特の筆致や物質感そのものが価値の場合だけ、`raster_regenerate` へ上げます。単に「手書き風」という理由だけで画像化しません。

### `real_text`

画像生成しません。正確な文言とタイポグラフィ仕様を渡します。

- 見出し、本文、CTA、ラベル、注記
- ロゴタイプ以外の読ませるコピー
- 数字、料金、日付、ナビゲーション

### `omit`

実装上不要、重複、ノイズ、AI生成の破綻、または別要素へ統合するものです。

## フェーズ0: canonicalを参照画像として固定する

承認済みセクション画像を `reference/canonical_composite.png` として保存します。記録するもの:

```text
section_id:
canonical_file:
canonical_sha256:
canvas_width_px:
canvas_height_px:
color_space:
approval_state:
reference_only: true
```

canonicalは、人物の同一性、ポーズ、衣装、商品形状、画角、光、色、質感、構図関係を読むために使います。canonicalのピクセルを切り出してproduction assetへ保存してはいけません。

参照範囲を示すcrop画像を生成ツールへの入力に使うことはできますが、入力cropと出力assetを区別し、manifestへ `reference_input_only: true` と記録します。

## フェーズ1: asset triage ledgerを作る

見えている要素を意味単位で列挙し、4分類します。

```text
asset_id:
semantic_role:
classification: raster_regenerate | css_svg_rebuild | real_text | omit
generation_unit: full_field_scene_plate | clean_background_plate | transparent_foreground | transparent_scene | card_artwork_plate | code_native
unit_members:
independent_behavior: motion | responsive_recompose | reuse | interaction | content_update | layering | none
split_evidence:
keep_together_reason:
reason:
reference_region_px:
reference_input_only: true | false
visual_traits_to_preserve:
hidden_regions_to_complete:
forbidden_content:
output_file:
alpha_mode: transparent | opaque_plate | none
source_surface_topology: section_field | floating_scene | contained_artwork | tone_merged_object | source_visible_frame
source_topology_evidence_path:
surface_integration: alpha_floating | opaque_full_bleed | opaque_masked_merge | opaque_tone_matched | intentional_frame
field_owner: section_asset | css | shared | card_content
edge_policy: {top, right, bottom, left} = alpha | bleed | mask | tone_match | source_frame | not_visible
copy_space_rois:
responsive_focal_points:
consumer_surface_owner: css | asset | shared
outer_whitespace_owner: css | asset | none
consumer_background_color: #RRGGBB | not_applicable
mask_owner:
crop_in_use_path:
implementation_target: picture | img | css | inline_svg | html_text | none
anchor:
responsive_relationship:
```

迷った場合の順序:

```text
real_textで再現できるか
-> CSS/SVGで十分近く再現できるか
-> 画像固有の質感が価値か
-> raster_regenerate
```

さらに、分割候補ごとに「別々に動くか」「モバイルで内部構図を組み替える根拠があるか」「別々に再利用・差し替え・更新するか」「操作可能またはアクセシブルな構造か」「別レイヤーを間へ挟むか」を確認します。すべてNoなら分けません。特に、分割で光、影、接触、遠近感、質感が壊れる場合は一つのプレートへまとめます。「後で動かせる」は参照画像にもプロダクト要件にも根拠がない限りsplit evidenceではありません。

分類後、各ラスタについてsource surface topologyを固定します。カンプ上でsection fieldやfloating sceneだったものを、生成の都合でopaqueなカード画像へ変えてはいけません。選択に迷う場合は `10_visual_decomposition_for_web_build.md` の4.5を使います。

## フェーズ2: 再生成単位を決める

### フルフィールド・シーンプレート

主役ビジュアルがセクション全体の空間や世界観を作る場合、単なる背景だけでなく、coupled objects、非機能の画面アート、接地影、周辺の素材感まで含む一枚の `full_field_scene_plate` を生成します。

- section端までbleedできるcanvas比率と周辺余白を持つ
- 構造的な見出し、本文、CTA、ナビ、機能UIは含めない
- copy spaceを `x/y/w/h` またはframe ratioで宣言し、focal subjectと重ねない
- desktop/mobileごとのfocal pointとsafe cropを記録する
- HTML/CSSではsection-owned background layerまたはfull-bleed pictureとして扱う
- sourceにない小さなimage card、白枠、角丸、外側paddingを追加しない

複合デバイスのスタジオシーンがヒーロー全体の世界観を作る場合はこの単位です。右カラムのカード画像として再生成しません。

### 透過シーン

複数の物体や面が一つの説明図・コラージュとして一緒に動き、Web背景へ直接浮く場合は `transparent_scene` にします。

- exploded layers、紙オブジェクト、端末群、planner/designer/builderの素材群などを、consumer単位ごとに一枚へまとめてよい
- soft shadow、contact shadow、半透明部をalpha内へ含める
- 外周の生成背景、紙色の矩形、白いパネル、カード枠、外側paddingを含めない
- `transparent_foreground` と同じcheckerboard/白/黒/ブランド色検査を行う
- alpha化で品質が崩れる場合は、copy space込みの `full_field_scene_plate` またはsource-evidencedなmask/tone mergeへ設計を戻す

### カード内アートワークプレート

カード枠やラベルはコードで持ちながら、その内側の絵を一枚の不透明画像として生成します。

- 人物カードは人物、撮影背景、光、影を含めてよい
- UIカードは端末、非機能の表示画面、重なり、接地影、床面を含めてよい
- OBJECTカードはオブジェクト、台座、接触影、背景面を含めてよい
- TYPEやABSTRACTは、構造コピーではない装飾文字、筆致、印刷質感、幾何学を含めてよい
- カード枠、角丸、外側padding、セクションラベル、構造コピー、CTAは含めない
- 実装ではカードのcontent boxへ `object-fit: cover` または `contain` で配置する

### 例: 編集デザイン見本を並べるセクション

```text
HTML/CSS:
  セクション見出し、縦ラベル、罫線、カード枠、PERSON / TYPE / UI / OBJECT / ABSTRACTラベル

card_artwork_plate:
  PERSON: 人物 + 青空/建築 + 斜めの光
  TYPE: 装飾文字 + ブラシ質感 + 色面
  UI: laptop + phone + 非機能の画面 + 接地影 + surface
  OBJECT: stone object + plinth + 接触影 + 背景面
  ABSTRACT: 円弧 + 曲線 + 紙/印刷質感
```

この例では、端末画面が操作可能、差し替え対象、またはライブデータではない限り、端末ハードウェアから分けません。石と台座も別々に動かす根拠がない限り一枚にします。TYPE内の文字は構造コピーではなく作品表現なのでプレートへ含められます。

### 背景プレート

背景は、元画像から文字や人物を消した加工画像ではなく、同じ世界観で新たに生成したクリーンプレートにします。

- 人物、商品、文字、CTA、カード、アイコン、前景装飾を含めない
- 隠れていた床、壁、空、机、窓、光を自然に連続させる
- 実装時にcover/cropできる余白を持たせる
- 必要ならdesktopとmobileで別のart-directed cropまたは別生成を用意する

### 透過foreground asset

人物、商品、物体、複雑なイラストは、単体で完結した透過素材として再生成します。

- 元モックで文字や装飾に隠れていた輪郭を自然に補完する
- 頭、髪、手、指、服、商品の端を途中で切らない
- 元背景の色、影、文字、隣接パーツを含めない
- 接地影が本体と常に一緒に動くなら同じ素材へ含める。別方向へ動かす根拠がある場合だけ分ける
- 余白を含む生成キャンバスからtrimし、元サイズとtrim bboxの両方を記録する

複数人物が一体の写真表現として不可分、または同じカード内で常に一緒に動く場合は1assetにまとめます。人物ごとに分けるのは、個別モーション、responsive recompose、再利用、差し替え、独立layeringが必要な場合です。

## フェーズ3: 1出力ビジュアル単位ずつ生成する

`one_output_unit_per_generation: true` を必須にします。無関係な出力素材を同じ生成へ詰め込みません。一方、`full_field_scene_plate`、`transparent_scene`、`card_artwork_plate` の宣言済み `unit_members` は一つの構図として同時に生成します。asset ledgerの `raster_regenerate` 1行につき原則1回以上の生成を行います。

### カード内プレートのプロンプト骨格

```text
参照画像の[card_id]カード内の作品だけを、一つの新しい不透明アートワークプレートとして再生成する。
含めるもの: [unit_members]。元の相対位置、重なり、接地、遠近感、光、影、質感を一体として保つ。
含めないもの: カード枠、角丸、外側padding、セクションラベル、構造コピー、CTA、操作可能UI、隣接カード。
カード内content boxでcover/cropできる安全余白を持たせる。
元画像をcrop、mask、trace、pixel extractionしない。
```

### 透過素材プロンプト骨格

```text
参照画像の[asset_id]だけを、同一の人物/商品/画風/光/カメラ感を保って独立素材として再生成する。
出力にはこの宣言済み出力単位だけを含める。
元画像で他要素に隠れていた部分は自然に補完し、輪郭を完全にする。
背景は完全透過。文字、CTA、カード、アイコン、線、波形、隣接要素、元背景、床面、壁面を含めない。
キャンバス端で重要な輪郭を切らない。
中央に十分な透明余白を持って配置する。
```

### 透過シーンのプロンプト骨格

```text
参照画像の[scene_id]を、一緒に動く一つの透過シーンとして新規生成する。
含めるもの: [coupled objects / exploded panels / devices / collage pieces / local soft shadows]。
元の相対位置、重なり、遠近感、接地、光、影を一体として保つ。
背景は完全透過。外側の白、生成紙色、矩形パネル、カード枠、外側padding、構造コピー、CTA、隣接sceneを含めない。
すべての影と半透明部をalpha内に保持し、キャンバス端で輪郭や影を切らない。
```

### フルフィールド・シーンプレートのプロンプト骨格

```text
参照画像と同じ世界観を持ち、WEBセクション全体を所有する一枚のfull-field scene plateを新規生成する。
含めるもの: [coupled main visual, non-functional screen art, environment, contact shadows, material atmosphere]。
含めないもの: 見出し、本文、CTA、ナビ、機能UI、カード枠、画像としての外側余白。
[copy_space_roi] は低情報量で空け、主役や強い線を侵入させない。
desktop/mobileのcover cropに耐える周辺余白を持たせ、セクション端まで自然にbleedさせる。
小さな独立画像カードとして構図化しない。
```

### 背景プレートプロンプト骨格

```text
参照画像と同じ空間、画角、光、色、素材感を持つクリーンな背景プレートを新規生成する。
人物、商品、文字、ロゴ、CTA、カード、アイコン、線、前景装飾を一切含めない。
元画像で隠れていた背景領域も自然に連続させる。
WEBセクションのcover/cropに耐える周辺余白を確保する。
```

参照画像の見た目を保つとは、canonicalのピクセルをコピーすることではありません。同一性とアートディレクションを保ちながら、独立した完全な素材として新規生成します。

## フェーズ4: 透過出力のフォールバック

優先順位:

```text
1. 生成ツールから直接RGBA透過PNGを得る
2. 直接透過が不安定なら、新規に生成した単色マット上へ素材だけを生成する
3. その新規に生成した単色マットだけを背景除去する
4. alpha edgeと色かぶりを検査し、必要なら再生成する
```

マットは素材内に存在しない高コントラスト色を選び、均一、無影、無地にします。元モックの背景を背景除去して作った透過PNGはこのフォールバックに該当しません。

半透明の髪、ガラス、煙、布端、反射がマット色を抱いた場合、色補正で無理に救済せず再生成を優先します。

## フェーズ5: cleanliness verification

production assetごとに、少なくとも次を検査します。

```text
origin_kind: regenerated
cropped_from_canonical: false
masked_from_canonical: false
one_output_unit_only: true
generation_unit: full_field_scene_plate | clean_background_plate | transparent_foreground | transparent_scene | card_artwork_plate
declared_members_present: true
undeclared_neighbor_present: false
structural_text_present: false
functional_ui_present: false
background_contamination: none
alpha_present: true | not_applicable
alpha_edge_contamination: none
important_contour_clipped: false
occluded_regions_completed: true | not_applicable
dimensions_sufficient: true
style_match_to_reference: pass | needs_review
identity_match_to_reference: pass | needs_review | not_applicable
```

チェック方法:

- 透明assetをcheckerboard、白、黒、ブランド主色の4背景へ重ねる
- card artwork plateは、宣言したmembersの位置関係、重なり、接地影、遠近感、光、crop安全域をcanonical cropと比較する
- 100%と200%で髪、指、商品端、半透明部を見る
- assetの外周に元背景色、文字片、カード片、線がないか確認する
- 背景プレートに人物、文字、商品、CTA、アイコンの残像がないか確認する
- 元モックで隠れていた箇所が、不自然な穴や直線で終わっていないか確認する
- CSS/SVG/real_text対象が誤ってPNGへ焼き込まれていないか確認する

pixel-exact一致はclean assetの合否基準ではありません。独立再生成では、元モックとピクセル一致しないことが正常です。見るべきなのは、同一性、画風、光、質感、完全な輪郭、汚染のなさ、実装適性です。

## フェーズ5.5: web-surface integration verification

`generation_unit` は何を一緒に生成するかを決めます。`surface_integration` は、その生成物をWebの背景・カード・マスクへどう接続するかを決めます。この二つを混同しません。たとえば一枚の `card_artwork_plate` は正しい生成単位でも、外周に紙色の余白を焼き込んだままCSSの白いカードへ入れれば、二重paddingと色の継ぎ目を作ります。

さらに、`source_surface_topology` は「source compでその絵がどのような面として読まれていたか」を固定します。この契約は生成後に都合よく変更しません。

```text
section_field -> full_field_scene_plate + opaque_full_bleed / opaque_masked_merge
floating_scene -> transparent_foreground / transparent_scene + alpha_floating
contained_artwork -> card_artwork_plate + content boxへのfull bleed / measured mask / measured tone match
tone_merged_object -> alpha_floating / opaque_masked_merge / opaque_tone_matched
source_visible_frame -> intentional_frame
```

各辺について `alpha | bleed | mask | tone_match | source_frame | not_visible` を記録します。`section_field` を四辺hard edgeの小さな`<img>`へ入れたり、`floating_scene` をopaqueな白い矩形のままCSS背景へ置いたりした場合は、clean assetであっても `needs_surface_redesign` です。

各ラスタへ次のどれか一つを宣言します。

- `alpha_floating`: 人物、商品、複雑な物体など。実PNGに有意な透明pixelがあり、CSS背景の上へ直接置く。
- `opaque_full_bleed`: 写真環境や全面アート。assetがsurfaceを所有し、対象領域の端までbleedする。CSSと背景所有を二重化しない。
- `opaque_masked_merge`: opaque素材の端をCSS/SVG maskまたはgradientでWeb面へ溶かす。`mask_owner` を必須にする。
- `opaque_tone_matched`: asset外周色とconsumer背景色を実測で一致させる。見た目の申告ではなくRGB距離とcrop-in-useで確認する。
- `intentional_frame`: source compが明確に写真・紙・画面の矩形frameを見せている場合だけ、そのhard edgeを保持する。source crop証拠を必須にする。

cleanliness reviewは孤立素材だけで終えません。実際のconsumerへ置いたcropを保存し、少なくとも次を機械計測します。

```text
alpha_channel_present
transparent_pixel_fraction
edge_mean_rgb / edge_mean_deviation
uniform_outer_band_fraction / per-edge depth
estimated_content_bounds
consumer_background_rgb
edge_color_distance
css_owns_background / css_owns_padding / css_owns_frame
crop_in_use_path
```

次はblockedです。

- `alpha_floating` なのに透明pixelがほぼない
- CSSが背景・paddingを所有するのに、opaque assetが四辺へ均一な背景帯を持つ
- `opaque_full_bleed` とCSSの双方が背景を所有する
- `opaque_masked_merge` にmask ownerがない
- `opaque_tone_matched` の外周色がconsumer背景色の許容差を超える
- sourceに見えないframeを `intentional_frame` と自己承認する
- isolated assetだけを見て、最終consumer cropを保存していない
- `section_field` が `alpha_floating` / `intentional_frame` またはinvented cardとして実装されている
- `floating_scene` が有意なalphaを持たず、sourceにないopaque rectangleを含む
- source surface topologyとgeneration unit / integration mode / four-edge policyが矛盾する
- full-field sceneのcopy spaceが未定義、またはfocal subjectと重なる

## フェーズ5.75: consumer-truth gateとrevision re-entry

isolated PNGのalpha・edge計測だけでは、Webへ統合したときの所有関係を証明できません。handoff前に、実装予定と同じsurface targetへ最小配置してdesktop/mobileのcropを保存します。

- `section_field`: section-owned `<picture>` またはbackground ownerとして四辺へbleedし、copy spaceとfocal pointを確認する
- `floating_scene`: CSS背景の上へ直接置き、opaque rectangle、生成紙色、二重余白がないことを確認する
- `contained_artwork`: 実card content boxへedge-to-edgeで入れ、外側shell/paddingを二重化しないことを確認する
- `tone_merged_object`: 宣言したmaskまたはtone-matchを実consumer色で確認する

このproofで測るDOM/asset ownerは、実際にpixelを描画するvisible ownerでなければなりません。`opacity:0` の重複`<img>`、display:noneの計測用コピー、別要素のbackgroundを代理する透明DOMはhandoff proofになりません。manifestにはoutput SHA-256とcrop-in-use pathを一緒に記録し、asset bytesが変わればproofを再作成します。

実装レビューでsource topologyの誤分類が判明した場合は、CSS maskやカード化で取り繕わず、次を記録してフェーズ1へ戻ります。

```text
revision_id
affected_unit_ids
previous_topology -> new_topology
source_evidence_path
reason_or_user_feedback
invalidated: prompt receipt, asset preflight, crop-in-use, bbox, section review, pixel QA
bbox_policy: remeasure_from_source
```

旧実装が存在していても、最初のpre-CSS passを取得済みならrevision gateへ再入場できます。ファイル時刻を合わせるためにHTML/CSSをtouchしたり、旧実装を一時的に隠したりしません。new topologyの素材、hash、crop、source-based measurementを作り直し、その後のQAだけを新しいrevisionへbindします。

## フェーズ6: asset manifest

最小例:

```json
{
  "contract_version": "clean-section-assets/v4",
  "section_id": "section_01_hero",
  "reference": {
    "file": "reference/canonical_composite.png",
    "reference_only": true
  },
  "assets": [
    {
      "id": "A-hero-field",
      "classification": "raster_regenerate",
      "file": "assets/raster/hero-full-field-scene.png",
      "origin_kind": "regenerated",
      "generation_unit": "full_field_scene_plate",
      "unit_members": ["device-scene", "studio-environment", "contact-shadows"],
      "source_surface_topology": "section_field",
      "surface_integration": "opaque_full_bleed",
      "field_owner": "section_asset",
      "edge_policy": {"top": "bleed", "right": "bleed", "bottom": "bleed", "left": "bleed"},
      "copy_space_rois": [{"for": "heading-and-cta", "x": 0.04, "y": 0.12, "w": 0.38, "h": 0.58}],
      "crop_in_use_path": "proof/crops/hero-field-in-use.png",
      "implementation_target": "picture"
    },
    {
      "id": "A-subject",
      "classification": "raster_regenerate",
      "file": "assets/raster/subject-transparent.png",
      "origin_kind": "regenerated",
      "one_output_unit_per_generation": true,
      "generation_unit": "transparent_foreground",
      "unit_members": ["person"],
      "alpha_mode": "transparent",
      "surface_integration": "alpha_floating",
      "consumer_surface_owner": "css",
      "outer_whitespace_owner": "css",
      "crop_in_use_path": "proof/crops/subject-in-hero.png",
      "implementation_target": "picture",
      "anchor": "right center",
      "responsive_relationship": "copyの右側を維持し、mobileでは下段へ移動"
    },
    {
      "id": "A-device-card-art",
      "classification": "raster_regenerate",
      "file": "assets/raster/device-card-artwork.png",
      "origin_kind": "regenerated",
      "one_output_unit_per_generation": true,
      "generation_unit": "card_artwork_plate",
      "unit_members": ["laptop", "phone", "screen-art", "contact-shadows", "surface"],
      "independent_behavior": "none",
      "keep_together_reason": "static illustrative composition; light, perspective, and overlap move together",
      "alpha_mode": "opaque_plate",
      "surface_integration": "opaque_tone_matched",
      "consumer_surface_owner": "shared",
      "outer_whitespace_owner": "css",
      "consumer_background_color": "#F3EFE7",
      "crop_in_use_path": "proof/crops/device-card-in-use.png",
      "implementation_target": "picture"
    },
    {
      "id": "A-heading",
      "classification": "real_text",
      "file": "editable/text-spec.json",
      "implementation_target": "html_text"
    },
    {
      "id": "A-wave",
      "classification": "css_svg_rebuild",
      "file": "editable/svg-css-spec.md",
      "implementation_target": "inline_svg"
    }
  ],
  "verification": {
    "report": "proof/asset-cleanliness-report.json",
    "status": "complete_clean_assets"
  }
}
```

各ラスタassetには、参照ファイル、参照領域、生成プロンプト、生成日時、生成モデルまたはツール、出力SHA-256、元キャンバス、trim bbox、alpha mode、推奨表示サイズも記録します。
採用assetのSHA-256はpreflight reportにも含めます。manifestだけが同じでもPNG bytesが変わった場合、そのpreflightとconsumer cropはstaleです。

## HTML/CSS実装への引き渡し

```text
full-field scene plate -> section-owned picture / CSS background layer。copy spaceとresponsive focal pointを維持
clean background plate -> picture / CSS background-image
regenerated person, product, object, complex illustration -> img / picture
transparent scene -> img / picture。影を含むalpha assetとしてWeb背景へ直接配置
card artwork plate -> card content box内のimg / picture。CSSが枠・ラベル・paddingを所有
simple curve, wave, blob, line, icon, badge -> SVG / CSS / pseudo-element
readable copy, CTA, labels, numbers -> real HTML text
```

座標だけでなく関係性を引き渡します。

- コンテナ左端に追従
- セクション中央線に追従
- 人物の顔と見出しの右端を関連づける
- 背景はcover、透過主役はcontain
- desktopでは重なり、mobileでは縦積みにする
- 装飾だけviewport端でcrop可能

## 推奨パッケージ構造

```text
section_01_hero/
  reference/
    canonical_composite.png
  assets/
    raster/
      hero-full-field-scene.png
      background-plate.png
      subject-transparent.png
      exploded-transparent-scene.png
      product-transparent.png
      card-artwork-plate.png
  editable/
    text-spec.json
    svg-css-spec.md
    responsive-relationships.md
  prompts/
    A-background.md
    A-subject.md
    A-product.md
  asset-triage-ledger.json
  asset-manifest.json
  proof/
    alpha-preview-white.png
    alpha-preview-black.png
    alpha-preview-brand.png
    asset-cleanliness-report.json
```

Optionalなpixel-exact参照再構成は `reference-proof/` に隔離し、`assets/raster/` から参照してはいけません。

## ステータス契約

```text
complete_clean_assets:
  必要なラスタ素材が意味単位で再生成され、コード再現要素と実テキストが分離され、cleanliness verificationを通過した。

needs_regeneration:
  背景汚染、隣接要素、文字片、輪郭欠損、同一性崩れ、alpha edge汚染のいずれかがある。

needs_surface_redesign:
  個別assetはクリーンでも、source surface topologyとgeneration unit / integrationが矛盾する。HTML/CSSへ進まず、full-field sceneまたはtransparent sceneの生成設計へ戻る。

plan_only:
  asset triageとプロンプトはあるが、画像ファイルをまだ生成していない。

blocked_transparency:
  透過生成または新規マット除去を安全に実行・検査できない。

reference_proof_only_not_implementation_ready:
  元モックの再構成レイヤーしかなく、clean production assetsがない。
```

`complete` という曖昧な語だけで終えません。pixel差分0だけで `complete_clean_assets` にしてはいけません。

## セルフチェック

```text
canonicalをvisual referenceとして固定したか:
[yes/no]

全要素を raster_regenerate / css_svg_rebuild / real_text / omit に分類したか:
[yes/no]

分類前に独立挙動でsemantic visual unitを決めたか:
[yes/no]

すべてのsplitにmotion / responsive / reuse / interaction / content update / layeringの根拠があるか:
[yes/no]

同じカード内で一緒にしか動かない人物+背景、端末+画面、物体+台座、コラージュ、抽象作品をartwork plateとして検討したか:
[yes/no]

section field / floating scene / contained artwork / tone merged object / source-visible frameを生成前に決めたか:
[yes/no]

section fieldはcopy space付きfull_field_scene_plateとして、Web面の端まで所有するか:
[yes/no]

exploded layers、紙オブジェクト、端末群、コラージュは必要に応じて影込みtransparent_sceneになっているか:
[yes/no]

sourceにないopaque rectangleやimage cardを生成・追加していないか:
[yes/no]

production raster assetはすべて独立再生成か:
[yes/no]

元モックのcrop、mask、residualをproduction assetへ混ぜていないか:
[yes/no]

1回の生成につき1つの出力ビジュアル単位か:
[yes/no]

透過foregroundは完全な輪郭とクリーンなalpha edgeを持つか:
[yes/no]

背景プレートに人物、商品、文字、CTA、装飾が残っていないか:
[yes/no]

CSS/SVGと実テキストで再現すべきものをPNG化していないか:
[yes/no]

manifestにprovenance、prompt、alpha mode、anchor、responsive relationshipがあるか:
[yes/no]

generation unitとsurface integrationを別々に宣言したか:
[yes/no]

実PNGのalpha、外周均一帯、content bounds、edge colorを検査したか:
[yes/no]

最終consumer cropで二重padding、意図しないframe、背景色の継ぎ目がないか:
[yes/no]

source surface topology、generation unit、integration mode、四辺policyが一致するか:
[yes/no]

cleanliness reportを保存したか:
[yes/no]
```
