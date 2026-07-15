# references/08_prompt_templates.md

## 目的

分析結果とリファレンス選択を、画像生成AI向けの具体的なプロンプトへ変換するためのテンプレート集。

## 共通ルール

- WEBページは必ずセクションごとに生成する
- 1画像1セクション
- 1画像1メッセージ
- 見切れ禁止
- 詰め込み禁止
- 迷ったら余白
- 画像内テキストは短く、大きく、読めるようにする
- 日本語タイポグラフィをロゴのように扱う
- 主役ビジュアルの型を1つ選ぶ。人物写真は強い初期候補とし、別モチーフは意図を持って選ぶ
- 参考カンプや実装前提がある場合は、レイヤー順、全幅/コンテナ、素材分解を明示する
- レイヤー出力時は、基準画像を参照専用にし、独立挙動で意味単位を決めてから `raster_regenerate` / `css_svg_rebuild` / `real_text` / `omit` に分類し、必要なラスタ単位だけを再生成する

---

## 01. Section Prompt Compiler

```text
<generate_image section_id="[section_id]">

目的:
[このセクションの役割]

主メッセージ:
[このセクションで伝える1つのメッセージ]

画面内テキスト:
- ブランド名: [brand_name]
- メインコピー: [main_copy]
- サブコピー: [sub_copy]
- CTA: [cta]
- 小さな英字ラベル: [english_label]

主役ビジュアルの型:
[references/09_main_visual_archetypes.md から1つ]

人物 / 別モチーフの判断:
[人物を主役 / 人物を補助 / 別モチーフを主役。別モチーフの場合は理由も書く]

主役ビジュアル:
[選んだ型に基づく具体的な主役。人物 / 商品 / UI / 空間 / タイポグラフィ / 抽象グラフィック / データ / 証拠物 / その他]

WEBレイアウト型:
[references/02_web_layout_patterns.md から1つ]

グラフィック構図型:
[references/03_graphic_composition_patterns.md から1つ]

日本語タイポグラフィ型:
[references/04_japanese_typography_patterns.md から1〜2つ]

配色・光・写真:
[references/05_color_lighting_fashion_patterns.md から1つ]

業種別トーン:
[references/06_industry_tone_patterns.md から1つ]

素材出力モード:
[composite_only / layer_plan_only / layered_clean]

実装分解メモ（参考カンプや実装前提がある場合のみ）:
- レイヤー順: [背景から手前まで]
- 全幅要素: [背景写真、曲線、波形、巨大モチーフなど]
- コンテナ基準要素: [コピー、CTA、カード、読ませる要素など]
- 揃える基準: [コンテナ左端、中央線、人物の顔、CTAベースラインなど]
- semantic visual units: [独立して動く・並べ替わる・再利用される・更新される単位]
- raster_regenerate: [新規生成するfull-field scene、クリーン背景、透過foreground/scene、カード内アートワークプレートなど]
- source_surface_topology: [section_field / floating_scene / contained_artwork / tone_merged_object / source_visible_frame]
- source_topology_evidence: [その絵がsource上でどの面として読まれるかを示すcrop]
- generation_unit: [full_field_scene_plate / clean_background_plate / transparent_foreground / transparent_scene / card_artwork_plate]
- field_owner: [section_asset / css / shared / card_content]
- edge_policy: [top/right/bottom/leftごとに alpha / bleed / mask / tone_match / source_frame / not_visible]
- copy_space_rois / responsive_focal_points: [section_fieldの場合は必須]
- unit_members: [同じ出力単位に含める人物、背景、光、端末、画面、影など]
- independent_behavior: [motion / responsive_recompose / reuse / interaction / content_update / layering]
- split_evidence: [分割する場合の具体的根拠。根拠がなければ空]
- keep_together_reason: [静的一体構図としてまとめる理由]
- SVG/CSS候補の装飾: [曲線、手書き下線、矢印、アイコン、色面など]
- 実テキストとして保持する要素: [メインコピー、CTA、ラベル、補足文など]
- omit: [実装時に除外するノイズ、重複、破綻要素など]
- 装飾として裁ち落としてよい要素: [巨大背景文字、端の図形など]
- 欠けてはいけない要素: [顔、商品、CTA、読ませるコピーなど]

クリーン素材生成契約（layered_cleanの場合）:
- canonical composite: [参照専用の承認済み基準画像。production assetへ切り出さない]
- asset_triage: [raster_regenerate / css_svg_rebuild / real_text / omit]
- one_output_unit_per_generation: true
- raster assets: [新規生成したfull-field scene plate、クリーン背景プレート、透過foreground/scene PNG、カード内アートワークプレート]
- code assets: [実テキスト仕様、SVG/CSS仕様]
- asset manifest: [provenance、prompt、alpha mode、anchor、responsive relationship]
- cleanliness gate: [crop/mask由来でない、宣言したunit_members以外を含まない、構造テキストや機能UIを含まない、輪郭完全、必要なalpha edgeに汚染なし]
- failure state: [needs_regeneration / blocked_transparency / plan_only]
- surface failure state: [needs_surface_redesign。HTML/CSSへ進まず画像生成へ戻す]

デザイン指示:
- 一流広告代理店のアートディレクターが制作したような高品質WEBセクション画像
- 明るく、抜け感があり、清潔で現代的
- 情報を詰め込まず、大胆な余白を使う
- 日本語タイポグラフィをロゴのように設計する
- ポイントカラーを必ず入れる
- 写真、文字、背景、装飾が同じ世界にあるように統一する
- スマホで見た瞬間に手が止まる違和感を1つ入れる

セクション制約:
- この画像は1つのWEBセクションだけを表現する
- LP全体を1枚に圧縮しない
- 複数セクションを1枚に連結しない
- セクション途中で見切らない
- 読ませる文字、CTA、人物、商品、カード、重要装飾を欠けさせない
- アスペクト比に合わせて要素を小さく詰め込まない
- 窮屈な場合は、情報を削るか別セクション画像に分割する
- 迷ったら詰め込みではなく余白を選ぶ
- 途中で切り抜かれたスクリーンショットではなく、1つの完成したセクションとして見せる

禁止:
- Canvaテンプレート風
- 情報商材バナー風
- 古いチラシ風
- 美容クリニック広告風
- 怪しい副業広告風
- 白文字ベタ打ち
- 安いストックフォト風
- 普通のビジネス服だけの人物
- 暗い室内、曇ったグレー、沈んだトーン
- 読めない日本語
- 意味不明な英字
- 小さすぎる文字
- セクションの途中見切れ

</generate_image>
```

---

## 02. Hero Section Template

```text
<generate_image section_id="section_01_hero">

目的:
最初の3秒でブランドの世界観と約束を伝えるHero Section。

主メッセージ:
[サービスやブランドが読者に与える最も大きな変化]

画面内テキスト:
- ブランド名: [brand_name]
- メインコピー: [short_main_copy]
- サブコピー: [one_line_sub_copy]
- CTA: [short_cta]
- 英字ラベル: [short_english_label]

主役ビジュアル:
[Person-led / Typography-led / Product-led / UI-led / Abstract Graphic-led / Place-led] から1つ選び、大きく使う。人物をまず初期候補にし、別モチーフは人物より強い理由がある場合に主役にする。

デザイン:
- 巨大タイポ × 主役ビジュアル
- または Split Hero / Magazine Cover Hero / Poster Hero
- 日本語コピーは極太ゴシックロックアップまたは漢字巨大化 × かな抑制
- 明るく抜け感のある光
- 鮮やかなアクセントカラーを一点入れる
- 普通のLPファーストビューではなく、広告ポスターのような強さを出す
- 主役と文字が一部重なり、視線が止まる違和感を作る
- 人物写真は強い既定候補。商品、UI、空間、タイポグラフィ、抽象グラフィックの方が価値を一瞬で伝えられる場合は、意図的にそちらを主役にする
- 参考カンプがある場合は、新規生成する背景プレート、個別再生成する透過主役、曲線/下線/矢印などのSVG装飾、実テキストを分けて考える

制約:
- Hero以外の情報を入れない
- 特徴一覧、料金、FAQ、実績を入れない
- 1画像1セクション
- セクション途中で見切らない
- CTA、ブランド名、メインコピーを欠けさせない
- 余白を大胆に取る

</generate_image>
```

---

## 03. Problem Section Template

```text
<generate_image section_id="section_02_problem">

目的:
読者の課題を自分ごと化するProblem Section。

主メッセージ:
[読者がまだ言語化できていない課題や違和感]

画面内テキスト:
- セクションラベル: PROBLEM
- メインコピー: [short_problem_copy]
- 補足: [very_short_support_text]

主役ビジュアル:
課題を体現する人物、または課題を象徴する物、空間、UI、抽象グラフィック、タイポグラフィ。

デザイン:
- 大胆な余白
- ズレたグリッドまたは反復構図
- 不安を煽りすぎず、知的で明るい違和感を作る
- 日本語コピーは巨大一文字、縦横ミックス、または短歌・俳句的余白
- 暗くしない
- 読者が「自分のことかも」と感じる構図

制約:
- 解決策を全部説明しない
- チェックリストを詰め込まない
- 1画像1セクション
- 見出し、補足、主役ビジュアルを切らない
- 余白を残す

</generate_image>
```

---

## 04. Benefit Section Template

```text
<generate_image section_id="section_03_benefit">

目的:
読者が得られる良い変化を明るく提示するBenefit Section。

主メッセージ:
[利用後に読者の生活・仕事・感情がどう変わるか]

画面内テキスト:
- セクションラベル: BENEFIT
- メインコピー: [short_benefit_copy]
- ベネフィット: [最大3点]
- CTAまたは補足: [必要なら短く]

主役ビジュアル:
明るい人物、UIカード、抽象グラフィック、商品、空間。

デザイン:
- Floating Card Layout または Asymmetric Grid
- 色面グラフィック融合
- 明るく、前向きで、軽くなる印象
- ポイントカラーをカードまたは装飾に入れる
- 日本語コピーは極太ゴシックロックアップまたは2段ロックアップ
- カードは最大3枚

制約:
- 機能説明を詰め込まない
- 4つ以上のベネフィットを1枚に入れない
- 文字を小さくしない
- 1画像1セクション
- セクション途中で見切らない

</generate_image>
```

---

## 05. Features Section Template

```text
<generate_image section_id="section_04_features">

目的:
サービスや商品の選ばれる理由を整理して見せるFeatures Section。

主メッセージ:
[最も重要な特徴または選ばれる理由]

画面内テキスト:
- セクションラベル: FEATURES
- メインコピー: [short_feature_copy]
- 特徴: [最大3点]
- 補足: [必要なら短く]

主役ビジュアル:
UIカード、商品、アイコンではなく象徴的なグラフィック、機能を表すビジュアル。

デザイン:
- Card Stack Layout / Editorial Grid / Layered UI
- 主カードを1つ大きく、補助カードは控えめに
- 日本語タイポグラフィは字間広めのサブコピー、欧文ラベル添え、長体ゴシック
- 余白を保つ
- 図解ではなく広告的に見せる

制約:
- 6項目以上を詰め込まない
- すべての機能を説明しない
- UIカードを小さく大量配置しない
- 入りきらない場合は Feature 01 / Feature 02 に分割
- 1画像1セクション

</generate_image>
```

---

## 06. Case Study Section Template

```text
<generate_image section_id="section_05_case_study">

目的:
実績や事例を、信頼感とストーリーで見せるCase Study Section。

主メッセージ:
[この事例から伝えたい成果や変化]

画面内テキスト:
- セクションラベル: CASE STUDY
- 見出し: [short_case_title]
- 成果数字: [最大1〜3個]
- 引用: [short_quote]
- 属性: [company_or_person_label]

主役ビジュアル:
人物写真、事例を象徴する空間、成果物、資料、データ表現。

デザイン:
- Magazine Feature Layout / Editorial Grid / Full Bleed Photo
- 雑誌見出し風ジャンプ率
- 引用を大きく扱う
- 数字は大きく、数は絞る
- 信頼感と余白を両立する

制約:
- 複数事例を1枚に詰め込まない
- 長文インタビューを入れない
- 数字を並べすぎない
- 1事例1画像
- セクション途中で見切らない

</generate_image>
```

---

## 07. Pricing Section Template

```text
<generate_image section_id="section_06_pricing">

目的:
料金やプランを、安心感と選びやすさを持って提示するPricing Section。

主メッセージ:
[価格に対する納得感、選びやすさ、始めやすさ]

画面内テキスト:
- セクションラベル: PRICING
- 見出し: [short_pricing_copy]
- プラン名: [最大3つ]
- 価格: [読みやすく]
- CTA: [short_cta]

主役ビジュアル:
料金カード、プランカード、シンプルな比較UI。

デザイン:
- Comparison Layout または Card Stack Layout
- 推奨プランを1つ強調
- 数字を美しく大きく扱う
- 注釈は最小限
- 余白を大きく

制約:
- 複雑な比較表を入れない
- 4プラン以上を1枚に入れない
- 小さい注釈を大量に入れない
- 複雑な場合は Pricing Overview / Pricing Detail に分割
- CTAを欠けさせない

</generate_image>
```

---

## 08. FAQ Section Template

```text
<generate_image section_id="section_07_faq">

目的:
申し込み前の不安を取り除くFAQ Section。

主メッセージ:
[読者の不安を軽くする一番重要な安心材料]

画面内テキスト:
- セクションラベル: FAQ
- 見出し: [short_faq_title]
- 質問: [最大3個]
- 回答: [各質問ごとに短く]

主役ビジュアル:
アコーディオン風UI、Qの巨大タイポ、余白、安心感のある装飾。

デザイン:
- Minimal FAQ Layout
- White Space Luxury
- Qを大きく扱う
- 文字は読みやすく
- 明るく清潔に

制約:
- FAQを全部入れない
- 長文回答を入れない
- 10個以上の質問を詰め込まない
- 小さい文字にしない
- 1画像1セクション

</generate_image>
```

---

## 09. CTA Section Template

```text
<generate_image section_id="section_08_cta">

目的:
読者に次の行動を促すCTA Section。

主メッセージ:
[今、申し込む・問い合わせる・受け取る理由]

画面内テキスト:
- ブランド名: [brand_name]
- メインコピー: [short_cta_copy]
- サブコピー: [very_short_support_text]
- CTA: [button_text]
- 英字ラベル: [short_english_label]

主役ビジュアル:
人物、強いコピー、商品、色面、象徴的なグラフィック。

デザイン:
- CTA Poster Layout
- 巨大タイポ衝突
- White Space Luxury
- 明るく前向き
- ボタンは1つ
- ポイントカラーをCTAまたは装飾に効かせる
- 最後に記憶に残る余韻を作る

制約:
- 詳細説明を入れない
- 複数CTAを入れない
- 特典を大量に並べない
- 情報商材風に煽らない
- セクション途中で見切らない

</generate_image>
```

---

## 10. Footer Section Template

```text
<generate_image section_id="section_09_footer">

目的:
ページの終わりとして、ブランドの余韻と信頼感を残すFooter Section。

画面内テキスト:
- ブランド名: [brand_name]
- 小さなコピー: [short_brand_message]
- 最小限のリンク風情報: [optional]
- コピーライト風表記: [optional]

主役ビジュアル:
ロゴ風タイポグラフィ、余白、小さなアクセント、静かな背景。

デザイン:
- Minimal Footer Layout
- White Space Luxury
- 字間広めの小さな文字
- ブランドロゴ風の文字組み
- 余白を大きく
- 静かに終わる

制約:
- 大量リンクを入れない
- フッターで説明を増やさない
- 小さい文字を詰め込まない
- CMSテンプレート風にしない

</generate_image>
```

---

## 11. Reference Selection Template

```text
<section_design_plan>

section_id:
[section_id]

section_role:
[このセクションの役割]

main_message:
[1つだけ]

selected_web_section_pattern:
[01_web_section_patterns.md から選択]

selected_layout_pattern:
[02_web_layout_patterns.md から1つ選択]

selected_graphic_pattern:
[03_graphic_composition_patterns.md から1つ選択]

selected_typography_patterns:
[04_japanese_typography_patterns.md から1〜2つ選択]

selected_main_visual_archetype:
[09_main_visual_archetypes.md から1つ選択]

human_presence_decision:
[人物を主役 / 人物を補助 / 別モチーフを主役（理由）]

selected_color_lighting_pattern:
[05_color_lighting_fashion_patterns.md から1つ選択]

selected_industry_tone:
[06_industry_tone_patterns.md から1つ選択]

avoid_from_negative_examples:
[07_negative_examples.md から特に避けるべきもの]

split_decision:
[1枚で完結 / サブセクションへ分割]

</section_design_plan>
```

---

## 12. Self Check Template

```text
<self_check section_id="[section_id]">

1画像1セクションか:
[yes/no]

複数セクションを詰め込んでいないか:
[yes/no]

セクション途中で見切れていないか:
[yes/no]

見出し、本文、CTA、人物、商品、カードが欠けていないか:
[yes/no]

アスペクト比に合わせて縮小・圧縮していないか:
[yes/no]

スマホで読める文字サイズか:
[yes/no]

日本語タイポグラフィはロゴのように設計されているか:
[yes/no]

余白は十分か:
[yes/no]

ポイントカラーは効いているか:
[yes/no]

写真・文字・背景・装飾は同じ世界にあるか:
[yes/no]

主役ビジュアルの型は1つに絞れているか:
[yes/no]

人物を外す場合、その理由は人物写真より強いか:
[yes/no]

安っぽいテンプレート感はないか:
[yes/no]

AIっぽい破綻はないか:
[yes/no]

広告目的とCTAは自然につながっているか:
[yes/no]

レイヤー出力時、canonical compositeを参照専用として固定したか:
[yes/no/not-applicable]

全要素を raster_regenerate / css_svg_rebuild / real_text / omit に分類したか:
[yes/no/not-applicable]

production raster assetを元モックから切り抜かず、宣言した出力ビジュアル単位ごとに再生成したか:
[yes/no/not-applicable]

分割した素材ごとに独立挙動の根拠があり、静的一体構図を過分解していないか:
[yes/no/not-applicable]

画像生成前にsource surface topologyを固定し、section fieldをinvented cardへ、floating sceneをopaque rectangleへ変えていないか:
[yes/no/not-applicable]

full-field sceneのcopy space、またはtransparent sceneのalpha+shadowをプロンプトへ明記したか:
[yes/no/not-applicable]

透過素材の輪郭、alpha edge、元背景色、隣接要素を検査したか:
[yes/no/not-applicable]

CSS/SVGや実テキストで再現すべき要素をラスター生成していないか:
[yes/no/not-applicable]

修正が必要な場合:
[具体的な修正指示]

</self_check>
```

---

## 13. Revision Prompt Template

```text
<revise_image section_id="[section_id]">

現在の問題:
[見切れ / 詰め込み / 文字が小さい / 安っぽい / 暗い / タイポグラフィが弱い / AI破綻 / 素材汚染 / 編集不能なレイヤー / その他]

修正方針:
- 1画像1セクションを維持する
- セクション全体が自然に完結して見えるようにする
- 見出し、本文、CTA、人物、商品、カードを欠けさせない
- 情報を減らす
- 余白を増やす
- メインコピーを短くする
- 日本語タイポグラフィのジャンプ率を上げる
- ポイントカラーを一点だけ効かせる
- 明るく抜け感のある光にする
- 人物写真が凡庸な場合、または別モチーフの方が価値を一瞬で伝える場合は、主役ビジュアル型を選び直す
- 安っぽいテンプレート感を消す
- レイヤー出力時はcanonical compositeを参照専用にし、production assetへ切り出さない
- raster_regenerate対象は宣言した出力ビジュアル単位ごとに再生成し、独立素材では隠れた輪郭まで補完する
- 静的なカード内の一体構図は `card_artwork_plate` にまとめ、分割には `split_evidence` を要求する
- 元背景色、文字片、隣接要素、alpha edge汚染があれば再生成する
- CSS/SVGと実テキストで再現できる要素は画像生成しない

禁止:
- LP全体を1枚にまとめる
- セクション途中でトリミングする
- 文字を小さくして詰め込む
- 複数メッセージを混ぜる

</revise_image>
```

---

## 14. Multi Section Output Template

```text
<website_visual_output_plan>

全体方針:
WEBページ全体を1枚画像にせず、意味のあるセクションごとに画像生成する。
各画像は独立した完成度を持ち、並べたときに1つのWEBページとして自然につながる。

出力セクション:
1. section_01_hero
2. section_02_problem
3. section_03_benefit
4. section_04_features
5. section_05_case_study
6. section_06_pricing
7. section_07_faq
8. section_08_cta
9. section_09_footer

共通制約:
- 1画像1セクション
- 1画像1メッセージ
- セクション途中で見切れない
- 文字、CTA、人物、商品、カードを欠けさせない
- 詰め込まない
- 迷ったら余白
- 各セクションで主役ビジュアルの型を1つに絞る
- 人物写真は強い初期候補。別モチーフは意図して選ぶ
- 必要ならさらに分割する

</website_visual_output_plan>
```

---

## 15. Layered Asset Package Template

```text
<layered_asset_package section_id="[section_id]">

output_mode:
layered_clean

canonical_reference:
- file: [canonical_composite.png]
- width_px: [width]
- height_px: [height]
- color_space: sRGB
- reference_only: true

asset_triage:
- [asset_id, semantic_role, source_surface_topology, generation_unit, unit_members, independent_behavior, raster_regenerate | css_svg_rebuild | real_text | omit, reason]

generation_order:
1. 全要素を4分類し、独立して振る舞う意味単位を決める
2. source surface topologyとtop/right/bottom/left edge policyをsource evidence付きで固定する
3. section fieldはcopy space込みのfull-field scene plateとして新規生成する
4. 下層の空間だけが必要なら、上層要素を含まない背景プレートを新規生成する
5. 独立して振る舞う人物、商品、物体は透過foregroundとして、coupled objects/exploded view/collageは影込みtransparent sceneとして新規生成する
6. 静的なカード内部の一体構図はカード内アートワークプレートとしてまとめて新規生成する
7. 直接透過できない場合だけ、新規単色マット上に生成し、そのマットを除去する
8. 実テキスト仕様とSVG/CSS仕様を作る
9. 各assetを用途に応じてcheckerboard、白、黒、ブランド色または最終cropで検査する
10. topologyと実pixelが矛盾したらneeds_surface_redesignとして画像生成へ戻す

raster_outputs:
- [copy space付きfull-field scene plate]
- [クリーン背景プレート]
- [独立単位として再生成した透過foreground PNG]
- [coupled objectsと影を含むtransparent scene PNG]
- [静的一体構図として再生成したカード内アートワークプレート]

code_outputs:
- [text-spec]
- [SVG/CSS spec]
- [responsive relationships]

asset_manifest:
- reference_input_only
- origin_kind: regenerated
- generation_prompt
- output_sha256
- alpha_mode
- intended_role
- generation_unit
- source_surface_topology
- source_topology_evidence_path
- field_owner
- edge_policy
- copy_space_rois / responsive_focal_points
- unit_members
- independent_behavior
- split_evidence
- keep_together_reason
- anchor
- responsive_relationship

verification:
- cropped_from_canonical = false
- masked_from_canonical = false
- one_output_unit_per_generation = true
- readable_text_present = false
- structural_text_present = false
- functional_ui_present = false
- undeclared_neighboring_element_present = false
- alpha_edge_contamination = none
- important_contour_clipped = false
- occluded_regions_completed = true | not_applicable
- topology_generation_integration_agree = true
- invented_opaque_rectangle = false
- final_consumer_has_no_unintended_frame = true

status:
[complete_clean_assets / needs_regeneration / needs_surface_redesign / plan_only / blocked_transparency / reference_proof_only_not_implementation_ready]

</layered_asset_package>
```
