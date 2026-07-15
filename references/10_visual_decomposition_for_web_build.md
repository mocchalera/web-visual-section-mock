# references/10_visual_decomposition_for_web_build.md

## 目的

既存のデザインカンプ、参考画像、または後でHTML/CSSへ落とし込む前提のWEBビジュアルを、**実装を意識したレイヤー・幅・素材単位**に分解するためのリファレンス。

これは完成HTML/CSSを書くための手順ではありません。画像生成プロンプトやビジュアルモック指示の前段で、どの要素を再生成ラスタ素材、SVG/CSS装飾、実テキストとして扱うべきかを明確にし、あとから再現しにくい「全部が1枚に溶けた画像」を避けるために使います。

このファイルは分解計画までを担当します。承認済み画像を参照し、必要な画像パーツだけをクリーンに個別再生成する場合は、続けて `11_layered_asset_generation_and_reconstruction.md` を使います。

## 使う場面

- ユーザーがデザインカンプ、LPスクリーンショット、生成済みビジュアルを示している
- 人物、背景、巨大文字、曲線マスク、CTA、装飾線などが複雑に重なっている
- 「これをコーディングするならどう分解するか」という観点が重要
- 生成画像をそのまま使うのではなく、WEBセクションとして再構成しやすいモックにしたい
- 実装に移る前のアートディレクション、素材切り分け、プロンプト改善をしたい

純粋なムード探索や、実装再現性を問わないポスター風ビジュアルだけなら必須ではありません。

## 基本姿勢

デザインカンプを1枚の平面として見ず、次を先に決めます。

```text
1. 奥から手前へのレイヤー順
2. 全幅に敷く要素とコンテナ幅に収める要素
3. どの左端・右端・中心線・ベースラインで揃えるか
4. 独立して振る舞う意味単位と、ラスタ素材、SVG/CSS装飾、実テキストの境界
5. 装飾として裁ち落としてよいものと、欠けてはいけないもの
6. PC幅とスマホ幅で残す関係性
```

## 1. レイヤー順を言語化する

奥から手前へ、できるだけ具体的に書きます。

```text
section background color / gradient
full-bleed background photo or space plate
oversized background motif, kanji, logo, or pattern with opacity
large structural mask, blob, curve, wave, or color field
independently regenerated transparent subject: person, product, complex illustration, object
foreground decorative stroke, underline, arrow, icon
real text lockup, CTA, labels, badges, microcopy
```

重要なのは、写真・文字・装飾がどちらの上に乗り、どちらの下に潜るかです。巨大な背景文字や曲線色面は「背景っぽい」だけでなく、人物の前後関係や文字の可読性に関わるため、プロンプトでもz-indexのように指定します。

装飾的な巨大文字や背景グラフィックは画面端で一部裁ち落としてもよいですが、CTA、読ませるコピー、人物の顔、商品、カード、UIの重要部は欠けさせません。

## 2. 全幅要素とコンテナ要素を分ける

WEBセクションとして見ると、要素には「幅の所有者」があります。

### 全幅に置きやすい要素

- 背景写真、背景の室内・街・空間
- 画面端から入る大きな曲線、波形、色面
- 装飾的な巨大文字、ロゴモチーフ、パターン
- セクション下端・上端の切り替え形状

### コンテナ幅に収めやすい要素

- メインコピー、サブコピー、CTA
- セクションラベル、補足テキスト、注記
- 本文カード、料金カード、機能カード
- 読ませるアイコンやリンク

### 中間扱いにする要素

人物、商品、UIモックは、コンテナのグリッドに基準を持ちながら、視覚的には全幅背景へはみ出すことがあります。どこを基準に置き、どこを意図的に外へ出すかを決めます。

## 3. 揃える基準を決める

カンプを見たら、最初に「どの線で揃っているか」を探します。

- コピー群の左端
- CTAの左端または中央
- 写真人物の顔・目線・体の中心
- 背景の柱、窓、水平線、机の端
- 大きな色面や曲線の接点
- セクション内コンテナの左端・右端

複雑なビジュアルでも、主役コピーとCTAは同じコンテナ左端を基準にするとWEBとして安定します。背景写真、曲線マスク、巨大モチーフは全幅で動かし、読み物としての要素はコンテナへ戻します。

## 4. 先に意味単位を決める

目で区別できる物体数と、実装素材数は一致しません。分割の既定は「一物体一素材」ではなく、**一つの独立した実装挙動につき一素材**です。

次のどれかが必要な場合だけ分けます。

- 別々に動く、重なる、またはz-orderが変わる
- PCとスマホで内部配置を組み替える根拠がある
- 一方だけ再利用、差し替え、CMS更新する
- 操作可能なUI、ライブデータ、アクセシブルな構造として必要
- 別の要素を間へ挟むため、独立レイヤーが必要

逆に、同じカード内で常に一緒に移動・拡大・cropされ、分割すると光、影、接地、遠近感、質感が崩れる場合は一枚の `card_artwork_plate` にまとめます。PCとスマホを含む静的モック、石と台座を含む静物、人物とその撮影背景、文字コラージュ、質感のある抽象作品が典型です。カード枠、ラベル、構造的コピーはプレートへ焼き込まずHTML/CSSが所有します。

## 4.5. 画像生成前にWeb面との接続方法を決める

意味単位を決めただけでは不十分です。同じ複合シーンでも、セクション全体の世界として敷くのか、Web背景へ浮かせるのか、実在するカード内へ収めるのかで、生成すべき画像が変わります。**生成後にCSSでなんとなく馴染ませるのではなく、プロンプトを書く前にsource surface topologyを固定します。**

### `section_field`

主役ビジュアルがセクションの空間・光・世界観を作り、カンプ上で独立した画像カードとして見えていない場合です。

- `full_field_scene_plate` として、セクション端まで届く横長または縦長の一枚を生成する
- 主役の装置、物体、非機能の画面アート、接地影、空間を一体で含めてよい
- 見出し、本文、CTA、ナビ、機能UIは含めない
- copy spaceをpx/ratioのROIで確保し、desktop/mobileのfocal pointを決める
- 実装で小さな`<img>`カードへ閉じ込めず、section background layerまたはfull-bleed `<picture>` として所有させる

右側に複合デバイスが見えるヒーローでも、その絵がセクション全体の白いスタジオ空間を作っているなら「右カラムの画像」ではなく `section_field` です。左右を切った不透明カードへ縮退させません。

### `floating_scene`

紙の縦長オブジェクト、exploded layers、コラージュ、端末群、文具群などが、カンプ上でWebの地の上へ直接浮いて見える場合です。

- 単体なら `transparent_foreground`、複数の結合オブジェクトなら `transparent_scene` とする
- coupled members、接地影、柔らかい落ち影は一つの透過PNGへ含めてよい
- 外側の白・生成紙色・矩形パネル・余白は含めない
- 直接alphaが不安定なら、新規単色マット上で生成してそのマットだけを除去する
- 透明化が品質を壊す場合は、次善策としてcopy space込みの `full_field_scene_plate` へ設計を戻す。opaqueな小矩形へ逃げない

### `contained_artwork`

カンプにカード、写真窓、画面、ポスターなどの明確なcontent boxがあり、hard edgeが構造として見える場合です。

- 内部は `card_artwork_plate` として一体生成できる
- CSSは外側のカード枠、ラベル、角丸、paddingを所有する
- rasterはcontent boxの端までbleedし、CSSとの二重paddingを作らない
- sourceにない白枠や紙色帯を「作品の余白」として追加しない

### `tone_merged_object`

物体と局所背景が不可分だが、Web面へ溶けて見える場合です。`alpha_floating`、`opaque_masked_merge`、`opaque_tone_matched` の順に適性を比較します。tone matchは生成画像の申告ではなく、四辺のedge RGBとconsumer backgroundの距離で判定します。一辺でも差が目立つならmaskまたは再生成へ戻します。

### `source_visible_frame`

写真、紙、画面、ポスターの矩形境界そのものがカンプ上の意味ある表現である場合だけ使います。source crop証拠なしに `intentional_frame` を選びません。

### 選択順

```text
sourceに意味あるhard rectangleが見えるか
-> yes: contained_artwork または source_visible_frame
-> no: その絵がセクション全体の空間・世界観を作るか
   -> yes: section_field + full_field_scene_plate
   -> no: irregular silhouette / coupled sceneとして地の上へ浮くか
      -> yes: floating_scene + transparent_foreground / transparent_scene
      -> no: tone_merged_objectとしてalpha / mask / measured tone matchを比較
```

各辺ごとに `alpha | bleed | mask | tone_match | source_frame | not_visible` を宣言します。四辺ともhard edgeのopaque rasterをCSS背景へ置く場合は、sourceにその矩形が見えるかを再確認します。見えなければ生成設計が間違っています。

## 4.6. 実装フィードバックでtopologyが変わったとき

`contained_artwork` として引き渡した後に「本来はセクション全体の世界だった」、またはopaque plateを「Web背景へ浮くsceneにすべきだった」と判明した場合、CSS調整として処理しません。これはasset design revisionです。

次をrevision ledgerへ残します。

```text
revision_id:
affected_unit_ids:
previous_topology:
new_topology:
reason_or_user_feedback:
source_evidence_path:
invalidated_evidence: prompt receipt | asset preflight | crop-in-use | bbox | section review | pixel QA
bbox_rebaseline: remeasure_from_source
```

影響unitをフェーズ1の分類とフェーズ3の生成へ戻し、new topologyに合う素材を再生成します。bboxは現在のDOMRectから逆算せず、source cropまたは新たに承認されたreferenceから測り直します。旧bboxと旧crop-in-useは履歴として残せますが、現行passの根拠にはしません。

## 5. 素材の持ち方を決める

### 写真・ラスタ画像に向くもの

- 人物、商品、空間などの質感が重要な主役
- 背景の室内、窓、植物、遠景の人物などの雰囲気
- 複雑な光、布、肌、髪、反射

背景写真は、文字やCTAを焼き込まず「クリーンな背景プレート」として新規生成します。人物や商品が装飾や文字と重なる場合は、元モックから切り抜かず、隠れた輪郭まで補完した透過背景素材として個別再生成します。

### SVG/CSSに向くもの

- 大きな曲線、波形、blob、色面
- 手書き風の下線、矢印、丸囲み
- シンプルなアイコン、ライン、境界線
- 背景の巨大文字やロゴモチーフのベクター表現
- 透明度を持つ装飾レイヤー

形がシンプルで、レスポンシブに**独立して**位置・サイズ調整したいものは、画像に焼き込まずSVG/CSS候補として扱います。カード内作品の一部としてしか使わない円弧、装飾文字、画面アートは、質感や構図を守るためプレートへ含めても構いません。

### 実テキストにすべきもの

- メインコピー
- サブコピー
- CTAラベル
- セクションラベル
- 読ませる注記、補足文

日本語タイポグラフィのニュアンスは画像生成に任せきらず、ジャンプ率、改行、漢字/かな/助詞のサイズ差を明示します。大きな装飾漢字はベクターまたはテキストレイヤーとして扱い、可読コピーとは別レイヤーにします。

## 6. プロンプトへ足す分解メモ

参照画像や実装前提のビジュアルでは、通常のセクションプロンプトに次を追加します。

```text
実装分解メモ:
- レイヤー順:
- 全幅要素:
- コンテナ基準要素:
- 揃える基準:
- `raster_regenerate` する写真/透過素材:
- source surface topology: section_field / floating_scene / contained_artwork / tone_merged_object / source_visible_frame
- generation unit: full_field_scene_plate / clean_background_plate / transparent_foreground / transparent_scene / card_artwork_plate
- field owner:
- copy space ROIs / responsive focal points:
- top/right/bottom/left edge policy:
- source topology evidence:
- `card_artwork_plate` として一体生成する範囲:
- 分割する各素材の独立挙動:
- まとめる理由（光・影・接地・遠近感・質感）:
- SVG/CSS候補の装飾:
- 実テキストとして保持する要素:
- omitする要素:
- 装飾として裁ち落としてよい要素:
- 欠けてはいけない要素:
- PC/スマホで変えてよい関係:
```

このメモはコードではなく、ビジュアル指示の精度を上げるための設計メモです。

## 7. 例: 人物CTAヒーローの分解

明るい室内背景、スマホを持つ女性、薄い巨大な漢字モチーフ、左側の淡い曲線マスク、大きな日本語コピー、緑のLINE CTAが重なるヒーローでは、次のように分けます。

```text
レイヤー順:
1. 明るい室内の全幅背景写真。遠景人物や窓は含めるが、文字やCTAは入れない。
2. 右側に薄い巨大漢字モチーフを置く。低 opacity の背景装飾。
3. 左から大きく入る白〜水色の曲線マスクを置き、背景写真を柔らかく覆う。
4. 女性だけを隠れた輪郭まで補完した透過背景素材として個別再生成し、背景より上、巨大漢字モチーフより上へ配置する。
5. メインコピー、縦ラベル、サブコピー、CTAをコンテナ左端基準で配置する。
6. 手書き風の下線、矢印、アイコンはSVG/CSS装飾候補として別管理する。

全幅要素:
背景写真、巨大漢字モチーフ、曲線マスク、下端の色面カーブ。

コンテナ基準要素:
メインコピー、サブコピー、CTA、縦ラベル、補足文。

`raster_regenerate` する素材:
背景プレート、個別再生成した人物透過素材。

SVG/CSS候補:
曲線マスク、手書き風下線、矢印、CTA内アイコン、背景巨大漢字。
```

## 8. よくある失敗

### すべてを1枚の背景画像として扱う

文字修正、CTA変更、レスポンシブ調整ができず、WEBセクションとして弱くなります。重要なテキストとCTAは実テキスト扱いに戻します。

### 人物を背景写真に溶かす

人物が巨大文字、曲線マスク、CTAとの前後関係を作れません。主役人物は元モックから切り抜かず、透過素材として個別再生成し、前後関係を明示します。

### 完成モックを切り出してレイヤーと呼ぶ

元背景や隣接要素が輪郭へ残り、隠れていた部分も欠けたままなので、動かせる実装素材になりません。完成モックは参照専用にし、必要なラスタ表現だけを宣言した出力ビジュアル単位ごとに再生成します。

### 全要素をコンテナに閉じ込める

背景や曲線が小さくなり、カンプの伸びやかな広告感が消えます。背景、曲線、巨大モチーフは全幅で持たせます。

### 全要素を全幅に流す

コピーとCTAの基準線がなくなり、視線が迷います。読ませる要素はコンテナ左端や中央線へ戻します。

### 日本語タイポグラフィを一律サイズにする

カンプのニュアンスが消えます。漢字、かな、助詞、句読点、欧文ラベルの大きさ・太さ・間隔を分解して指定します。

### SVGで済む装飾まで画像に焼き込む

手書き線、矢印、曲線、色面が微調整できません。レスポンシブで動かしたい装飾はSVG/CSS候補にします。

### 見える物体をすべて別素材にする

PCとスマホ、石と台座、人物と撮影背景などを、独立挙動がないのに分けると、影、接触、遠近感、光の連続性をコードで再構築する必要が生まれます。静的なカード内作品は一枚の `card_artwork_plate` とし、カードの枠・ラベル・構造コピーだけをコードへ戻します。

## 9. セルフチェック

```text
奥から手前へのレイヤー順は説明できるか:
[yes/no]

全幅要素とコンテナ基準要素は分かれているか:
[yes/no]

揃える基準線は明確か:
[yes/no]

背景写真と人物/商品素材を個別再生成すべきか判断したか:
[yes/no]

全要素を raster_regenerate / css_svg_rebuild / real_text / omit に分類したか:
[yes/no]

各ラスタのsource surface topologyを、画像生成前にsource evidence付きで決めたか:
[yes/no]

section_fieldをinvented cardへ閉じ込めず、full_field_scene_plateとcopy spaceで設計したか:
[yes/no]

floating_sceneを外側背景なしの透明sceneとして設計し、影もalpha内へ含めたか:
[yes/no]

四辺のalpha / bleed / mask / tone match / source frame方針を宣言したか:
[yes/no]

topology変更時にrevision ledgerとsource-based bbox rebaselineを作り、古いproofを無効化したか:
[yes/no]

分類前に、独立して振る舞う意味単位へまとめたか:
[yes/no]

各splitにmotion / responsive / reuse / interaction / content update / accessibility / layeringの根拠があるか:
[yes/no]

静的カード内の複数物体を、構図と質感を守る一枚のartwork plateにできないか検討したか:
[yes/no]

SVG/CSSで持つべき曲線、下線、矢印、色面を画像に焼き込みすぎていないか:
[yes/no]

読ませる文字とCTAを実テキスト扱いにできる設計か:
[yes/no]

装飾として裁ち落としてよいものと、欠けてはいけないものを分けたか:
[yes/no]

PCとスマホで崩してよい関係、守る関係を決めたか:
[yes/no]
```

## 次のフェーズ

レイヤー出力が必要な場合、この分解メモだけで完了にしません。`11_layered_asset_generation_and_reconstruction.md` で、canonicalの参照固定、意味単位の4分類、1出力単位1生成、透過foreground／クリーン背景／カード内アートワークプレートの再生成、asset manifest、cleanliness verificationまで行います。
