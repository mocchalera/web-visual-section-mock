---
name: web-visual-section-mock
description: Use for WEB/LP design mock image generation and art direction prompts: section-by-section website visuals, hero/CTA/features/pricing layouts, Japanese typography, main visual archetype selection, reference comp layer/asset decomposition, graphic design patterns, color/photo/fashion direction, and preventing cropped or cramped full-page screenshots. Do not use for ordinary coding, generic copywriting, or completed HTML/CSS implementation.
---

# Web Visual Section Mock Skill

## Purpose

Use this skill to turn a user brief, existing LP/screenshot/reference image, logo, product, service concept, or copy into high-quality WEB visual mock direction. The main output should be **section-based image generation prompts or section-based visual mock images**, not a single compressed full-page screenshot.

This skill is optimized for:

- WEB / LP / landing page visual mock direction
- section-by-section image generation
- hero, problem, benefit, features, case study, pricing, FAQ, CTA, footer visuals
- Japanese typography-heavy visual direction
- main visual archetype selection with person-led visuals as a strong default and other motifs chosen intentionally
- graphic design and editorial composition patterns
- implementation-aware visual decomposition for reference comps: layer stack, full-bleed vs container-bound elements, alignment bases, and asset strategy
- bright, polished, advertising-quality art direction
- preventing mid-section cropping, tiny text, and overstuffed vertical page images

## Do not use this skill for

- ordinary HTML/CSS implementation tasks
- general coding tasks
- generic copywriting with no visual design output
- long-form article writing
- final legal, medical, or financial advice
- full brand strategy documents unless the user specifically asks for visual mock direction

## Non-negotiable constraints

When the task involves outputting a WEB page or LP as images:

1. **Never compress the whole page into one image.**
2. **Use one image per meaningful section.**
3. **Each image must contain one section and one clear message.**
4. **Do not crop a section halfway.**
5. **Do not cut off readable text, CTA, people, products, cards, or essential visual elements.**
6. **Do not shrink text or layout elements just to fit the aspect ratio.**
7. **If a section feels crowded, simplify it or split it into multiple section images.**
8. **When uncertain, choose whitespace over density.**

Decorative oversized typography or background graphics may be intentionally cropped at the edge, but readable content, CTA, the primary subject, and section structure must remain intact.

## Progressive reference use

Start with this `SKILL.md`. Read additional files from `references/` only when they are useful for the current task.

Recommended reference order:

1. `references/00_reference_index.md` — overview and selection rules
2. `references/01_web_section_patterns.md` — which sections to create and what each should contain
3. `references/10_visual_decomposition_for_web_build.md` — layer stack, full-bleed/container ownership, alignment, and asset strategy for reference comps
4. `references/02_web_layout_patterns.md` — WEB layout patterns
5. `references/03_graphic_composition_patterns.md` — advertising / poster / editorial composition patterns
6. `references/04_japanese_typography_patterns.md` — Japanese typography patterns
7. `references/09_main_visual_archetypes.md` — person / product / UI / space / typography / abstract / data / artifact main visual selection
8. `references/05_color_lighting_fashion_patterns.md` — color, photo, lighting, fashion direction
9. `references/06_industry_tone_patterns.md` — industry-specific tone and cliché avoidance
10. `references/07_negative_examples.md` — failure modes and fixes
11. `references/08_prompt_templates.md` — prompt compiler templates

If only one file can be read, use `references/web_visual_agent_references_all.md` as a fallback.

## Pattern selection budget

For each section, select only a few patterns. Do not combine everything.

```text
WEB section pattern: 1
WEB layout pattern: 1
graphic composition pattern: 1
Japanese typography pattern: 1-2
main visual archetype: 1
color / lighting / photo pattern: 1
industry tone pattern: 0-1
```

The references are a menu, not a checklist. Over-combining patterns usually creates cheap, crowded, AI-like output.

## Workflow

### 1. Determine the output mode

Classify the user request as one of the following:

- **single visual**: one hero, poster, banner, or standalone section
- **multi-section website**: LP or full WEB page visual direction
- **prompt rewrite**: improve an existing prompt
- **critique / diagnosis**: identify why a visual prompt or generated image is failing
- **skill / reference editing**: revise this skill or its references

For multi-section websites, create a section map before generating or writing prompts.

### 2. Build a section map

For each section, define:

```text
section_id:
section_role:
main_message:
visible_text:
main_visual:
main_visual_archetype:
layout_pattern:
graphic_pattern:
typography_pattern:
color_lighting_direction:
spacing_policy:
layer_stack:
width_ownership:
alignment_basis:
asset_strategy:
split_decision:
avoid:
```

Default section sequence:

```text
section_01_hero
section_02_problem
section_03_benefit
section_04_features
section_05_case_study
section_06_pricing
section_07_faq
section_08_cta
section_09_footer
```

Only include sections that fit the user’s task. If a section would be too dense, split it into natural sub-sections such as `feature_01`, `feature_02`, `pricing_overview`, or `pricing_detail`.

### 3. Clarify the visual direction without over-asking

If the user provides enough information, proceed. If details are missing, make reasonable assumptions and label them briefly. Do not stop the task just because some brand details are missing.

Infer:

- subject and offer
- target audience
- reader desire, fear, aspiration
- emotional center
- brand tone
- visual constraints from provided assets
- where human presence should be the default visual hook and where another motif would be more intentional
- required sections
- content that should be omitted or moved to another section

### 4. Decompose complex reference visuals when useful

When the user provides a design comp, screenshot, generated reference image, or coding-oriented design commentary, use `references/10_visual_decomposition_for_web_build.md` before writing the final visual prompt. Keep the output as visual direction, not final HTML/CSS implementation.

For each complex section, decide:

- layer stack from background to foreground
- full-bleed elements vs container-bound elements
- alignment basis such as container left edge, center line, subject face, or CTA baseline
- which photo assets should be regenerated as clean background plates or transparent foreground cutouts
- which decorations should be SVG/CSS candidates, such as curves, waves, underlines, arrows, icons, or oversized motifs
- which readable copy, CTA, labels, and microcopy must remain real text rather than being fused into the background
- which decorative elements may crop at the viewport edge, and which essential elements must never crop
- which relationships must survive responsive changes

### 5. Choose patterns deliberately

For each section:

- choose the section pattern from `01_web_section_patterns.md`
- use `10_visual_decomposition_for_web_build.md` when the reference image has complex layers or the mock should later be coded
- choose one layout pattern from `02_web_layout_patterns.md`
- choose one graphic composition pattern from `03_graphic_composition_patterns.md`
- choose one or two Japanese typography patterns from `04_japanese_typography_patterns.md`
- choose one main visual archetype from `09_main_visual_archetypes.md`
- choose one color / light / photo direction from `05_color_lighting_fashion_patterns.md`
- optionally choose one industry tone from `06_industry_tone_patterns.md`
- check likely failures in `07_negative_examples.md`

Use person-led visuals as a strong default candidate, especially for Hero, Benefit, CTA, and services where trust, emotion, aspiration, or personal credibility matter. Choose product/object, UI/product screen, place/space, typography-led, abstract graphic, data/process, or evidence/artifact-led visuals when that motif carries the message more sharply than a person photo. Avoid repeating the same generic person treatment across every section.

### 6. Write or execute image prompts section by section

When generating prompts, use clear blocks like:

```text
<generate_image section_id="section_01_hero">
...
</generate_image>
```

When the environment has an image generation tool and the user asks for actual images, generate each section separately rather than asking for one full-page image.

Every section prompt must include the following meaning:

```text
This image represents only one complete website section.
Do not compress the whole landing page into one image.
Do not crop the section halfway.
Do not cut off readable text, CTA, people, product, cards, or essential visual elements.
Do not shrink content to fit the aspect ratio.
If the section feels crowded, simplify the content or split it into another section image.
Prefer generous whitespace over dense layout.
The section must feel complete, intentional, and not like a randomly cropped screenshot.
```

Also include the same meaning in Japanese when the target output is Japanese:

```text
この画像は1つのWEBセクションだけを表現する。
LP全体を1枚に圧縮しない。
セクション途中で見切らない。
読ませる文字、CTA、人物、商品、カード、重要な装飾を欠けさせない。
アスペクト比に合わせるために要素を小さく詰め込まない。
窮屈な場合は、情報を削るか、別セクション画像に分割する。
迷ったら詰め込みではなく余白を選ぶ。
途中で切り抜かれたスクリーンショットではなく、1つの完成したセクションとして見える必要がある。
```

### 7. Typography requirements

Japanese typography must be designed, not merely placed.

Prioritize:

- short main copy
- strong line breaks
- large jump ratio
- kanji / kana size contrast
- vertical + horizontal mixing when useful
- logo-like lockups
- generous spacing
- meaningful English labels only

Avoid:

- white text simply placed over a photo
- all text at the same size
- tiny unreadable Japanese
- meaningless English
- excessive text blocks
- copying a known ad, magazine cover, or existing brand system

### 8. Main visual, light, and styling requirements

Keep the visual bright, fresh, and advertising-grade.

Choose exactly one primary visual archetype per section:

- person-led
- product / object-led
- UI / product screen-led
- place / space-led
- typography-led
- abstract graphic-led
- data / process-led
- evidence / artifact-led
- hybrid scene-led with one clear dominant subject

Human subjects are a strong default visual hook, especially when the brief is vague. Keep a person-led visual unless another motif is clearly more specific to the offer, product, mechanism, place, or proof. If a non-person motif becomes the main visual, make it just as art-directed: strong scale, beautiful light, material detail, clear silhouette, generous whitespace, and a vivid accent color.

Use:

- high-key studio light
- bright outdoor daylight
- clean reflective urban light
- modern fashion editorial styling
- product still-life, polished UI mocks, editorial spaces, symbolic objects, typography-first compositions, data/process visuals, or abstract graphic systems
- accent colors in clothing, props, UI, product details, type, or graphic elements

Avoid:

- dark offices
- cloudy gray backgrounds
- ordinary business suits as the main look
- generic person photos as filler
- cheap stock-photo gestures
- stale corporate photography
- vague abstract backgrounds with no message
- heavy shadows and muddy tones

### 9. Self-check before final output

Before responding, check each section:

```text
Is it one image, one section, one message?
Is the section complete rather than cropped halfway?
Are readable text, CTA, people, product, cards, and important visual elements intact?
Is the layout free from aspect-ratio stuffing?
Is the text large enough for smartphone viewing?
Is the Japanese typography logo-like and intentionally designed?
Is there exactly one primary visual archetype?
If the visual is person-led, does it avoid generic stock-photo treatment?
If the visual is not person-led, is the motif an intentional choice that beats a person photo for this message?
If a reference comp is involved, is the layer order explicit?
Are full-bleed elements and container-bound elements separated?
Is the alignment basis clear?
Are clean background plates, transparent subject cutouts, SVG/CSS decorations, and real text separated where useful?
Is there enough whitespace?
Is there at least one vivid accent color?
Do photo, text, background, and decoration feel like one world?
Is there a small but effective visual tension or違和感?
Does it avoid Canva, info-product banner, clinic ad, old flyer, and cheap LP template clichés?
```

If any answer fails, revise by reducing information, increasing whitespace, shortening copy, changing the composition, or splitting the section.

## Response behavior

- If the user asks for a final prompt, output the complete prompt directly.
- If the user asks for section prompts, output separate section blocks.
- If the user asks for actual images and an image tool is available, generate section images separately.
- If the user asks for critique, be direct and identify concrete failure modes.
- If the user asks to revise this skill, preserve the progressive-disclosure structure: keep `SKILL.md` concise and place detailed reusable knowledge in `references/`.
- Use Japanese by default when the user writes in Japanese.
