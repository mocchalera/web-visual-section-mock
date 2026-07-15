---
name: web-visual-section-mock
description: "Use for WEB/LP design mock image generation and art direction prompts: section-by-section website visuals, hero/CTA/features/pricing layouts, Japanese typography, main visual archetype selection, reference comp decomposition, and clean implementation asset regeneration. It decides how each visual owns or joins the Web surface before generation, then separates real text and CSS/SVG structure from full-field scenes, transparent objects/scenes, clean backgrounds, and static card artwork plates. Do not use for ordinary coding, generic copywriting, or completed HTML/CSS implementation."
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
- clean semantic-unit raster regeneration for later design revision or HTML/CSS asset use
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

When the user asks for editable layers, separated parts, or implementation-ready assets:

1. Freeze the approved section image as a visual reference, not as a pixel source for production assets.
2. Group visible elements into the smallest **independently behaving semantic visual units**, then classify each unit as `raster_regenerate`, `css_svg_rebuild`, `real_text`, or `omit`.
3. Generate raster assets only for elements whose photographic, illustrative, material, or textural detail cannot be reproduced efficiently in CSS/SVG.
4. Regenerate every required raster unit independently. Use a `full_field_scene_plate` when the visual world should own the whole section and leave measured copy space; use `transparent_foreground` or `transparent_scene` when an object or coupled scene should float directly on the Web background; keep a static card interior—such as a person editorial photo, device mock scene, object still life, collage, or textured abstract—as one clean opaque `card_artwork_plate` when its members always move and scale together.
5. Do not ship crops, masks, extracted pixels, or residual patches from the composite as reusable production assets.
6. Do not raster-generate structural copy, CTA labels, functional UI, live data, simple icons, waves, blobs, rules, arrows, or other elements that need accessibility, interaction, updates, or independent responsive behavior. Decorative lettering and non-functional screen art may remain inside an artwork plate.
7. Generate one **output visual unit** per image-generation call. That unit may contain several coupled objects; verify its declared members, excluded shell/text, completeness, crop safety, and handoff metadata.
8. If the generator cannot emit alpha directly, generate the isolated asset on a new uniform high-contrast matte, remove only that newly generated matte, and inspect the edge. Never remove the original mock background and call the result regenerated.
9. Before writing any raster prompt, lock the section's **source surface topology** as `section_field`, `floating_scene`, `contained_artwork`, `tone_merged_object`, or `source_visible_frame`. A section-field visual cannot be demoted into a centered image card; a floating scene cannot ship with an opaque rectangular background merely because the generator emitted one.
10. Decide the **generation unit** and the **web-surface integration** separately. Every raster must declare exactly one integration mode: `alpha_floating`, `opaque_full_bleed`, `opaque_masked_merge`, `opaque_tone_matched`, or `intentional_frame`, plus the intended treatment of all four edges. An opaque artwork plate with baked outer whitespace is not implementation-ready merely because its internal subject is clean.
11. Verify the final crop in its real consumer. If CSS owns the card background or padding, the raster must not also contribute an accidental panel, margin, or mismatched edge color. If the pixels contradict the locked topology, report `needs_surface_redesign` and return to image generation before HTML/CSS.
12. Treat an approved topology change as a new asset-design revision. Record the affected units, old/new topology, source evidence, invalidated proofs, and a source-based bbox rebaseline; never preserve stale geometry or satisfy handoff with an invisible duplicate element.

## Progressive reference use

Start with this `SKILL.md`. Read additional files from `references/` only when they are useful for the current task.

Recommended reference order:

1. `references/00_reference_index.md` — overview and selection rules
2. `references/01_web_section_patterns.md` — which sections to create and what each should contain
3. `references/10_visual_decomposition_for_web_build.md` — layer stack, full-bleed/container ownership, alignment, and asset strategy for reference comps
4. `references/11_layered_asset_generation_and_reconstruction.md` — clean asset triage, independent regeneration, transparency workflow, manifest, and cleanliness verification
5. `references/02_web_layout_patterns.md` — WEB layout patterns
6. `references/03_graphic_composition_patterns.md` — advertising / poster / editorial composition patterns
7. `references/04_japanese_typography_patterns.md` — Japanese typography patterns
8. `references/09_main_visual_archetypes.md` — person / product / UI / space / typography / abstract / data / artifact main visual selection
9. `references/05_color_lighting_fashion_patterns.md` — color, photo, lighting, fashion direction
10. `references/06_industry_tone_patterns.md` — industry-specific tone and cliché avoidance
11. `references/07_negative_examples.md` — failure modes and fixes
12. `references/08_prompt_templates.md` — prompt compiler templates

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
- **layered asset package**: use an approved section composite as reference and regenerate only the raster-required parts as clean implementation assets
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
typography_source_impression: block width/height, max line width, glyph height, tracking, line advance, ink density, jump ratios, desktop/mobile scale bounds
color_lighting_direction:
spacing_policy:
layer_stack:
width_ownership:
alignment_basis:
asset_strategy:
asset_output_mode:
canonical_composite:
asset_triage: raster_regenerate | css_svg_rebuild | real_text | omit
generation_unit: full_field_scene_plate | clean_background_plate | transparent_foreground | transparent_scene | card_artwork_plate | code_native
source_surface_topology: section_field | floating_scene | contained_artwork | tone_merged_object | source_visible_frame
surface_integration: alpha_floating | opaque_full_bleed | opaque_masked_merge | opaque_tone_matched | intentional_frame
field_owner: section_asset | css | shared | card_content
edge_policy: top/right/bottom/left = alpha | bleed | mask | tone_match | source_frame | not_visible
copy_space_rois:
source_topology_evidence:
surface_owner: css | asset | shared
outer_whitespace_owner: css | asset | none
unit_members:
independent_behavior: motion | responsive_recompose | reuse | interaction | content_update | none
keep_together_reason:
raster_generation_plan:
code_rebuild_plan:
asset_manifest:
cleanliness_gate:
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
- whether the raster should own the section field, float without a rectangle, fill a real card interior, merge by mask/tone, or retain a source-visible frame
- whether a full-field scene needs measured text-safe copy space or a floating scene needs true alpha including its soft shadow

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

### 7. Generate a layered asset package when requested

After the section composite is approved, use `references/11_layered_asset_generation_and_reconstruction.md`.

Run the phase in this order:

```text
canonical visual reference lock
semantic visual-unit grouping, then asset triage
source surface-topology lock and four-edge policy
one-output-unit-per-generation prompt plan
full-field scene-plate regeneration with measured copy space where the visual owns the section
clean background plate regeneration
transparent foreground or coupled transparent-scene regeneration where art floats on the Web field
opaque card artwork plate regeneration where internal parts stay coupled
text and CSS/SVG implementation specs
asset manifest with provenance and placement relationships
cleanliness, alpha-edge, completeness, and contamination verification
in-context surface verification for alpha, edge bands, padding ownership, mask/bleed/tone continuity, and intentional frames
consumer-truth proof using the actual full-field, floating-scene, or card-content target
topology-revision ledger and source-based rebaseline when implementation feedback changes the surface relationship
```

The default output mode for later implementation is `layered_clean`. The canonical composite guides identity, pose, palette, light, camera, and composition, but its pixels are not reusable output. Regenerate each raster-required semantic unit independently. Do not split coupled objects merely because their silhouettes are distinguishable; split only for source-evidenced motion, responsive recomposition, reuse, interaction, content updates, accessibility, or independent layering. Do not use a hidden crop, full-frame copy, masked composite fragment, or residual patch to fake editability.

Optional pixel-exact reconstruction may be kept only as a separate audit/reference package when explicitly requested. It must never substitute for, be mixed into, or be reported as the clean production asset set.

### 8. Typography requirements

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

For every `fv-critical` or `section-critical` heading, measure the source impression before handoff: block width/height relative to the source frame, maximum line width, visible glyph height, tracking in `em`, line advance, interline gap, ink density, heading-to-lead/body/label jump ratios, and desktop/mobile scale bounds. A pattern label such as “bold gothic” is art direction, not an implementation contract. Do not copy one bbox/type spec across distinct section comps unless source evidence proves a shared lockup system.

Avoid:

- white text simply placed over a photo
- all text at the same size
- tiny unreadable Japanese
- meaningless English
- excessive text blocks
- copying a known ad, magazine cover, or existing brand system

### 9. Main visual, light, and styling requirements

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

### 10. Self-check before final output

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
If layered output was requested, is the canonical composite used only as a visual reference?
Has every element been classified as raster_regenerate, css_svg_rebuild, real_text, or omit?
Were elements grouped by independent implementation behavior before classification?
Does every split have a source- or product-evidenced motion, responsive, reuse, interaction, update, accessibility, or layering reason?
Are static card interiors kept as one artwork plate when splitting would damage light, shadow, perspective, texture, or contact relationships?
Are all production raster files independently regenerated rather than cropped or masked from the composite?
Was each output visual unit generated alone, with no structural text, functional UI, card chrome, or unrelated decoration?
Do foreground assets have clean transparency and complete formerly occluded shapes?
Do background plates contain no upper-layer subjects or fused readable text?
Are simple shapes, icons, waves, arrows, rules, and readable copy assigned to CSS/SVG or real text instead of raster output?
Does the asset manifest record reference, generation prompt, output file, alpha mode, intended role, anchor, and responsive relationship?
Does every critical heading carry source-measured scale, spacing, density, jump ratios, and responsive scale bounds rather than a repeated generic type spec?
Does every raster declare a surface-integration mode separately from its generation unit?
Was source surface topology decided before the raster prompt, with source evidence and a four-edge policy?
If the visual owns the section world, does it bleed as a full-field scene plate instead of appearing inside an invented image card?
If an object, exploded diagram, or collage floats on the Web background, is it a true transparent scene with its shadow rather than an opaque rectangle?
Was the actual PNG inspected for alpha, uniform outer bands, content bounds, and edge color, then reviewed inside its final consumer without double padding or a color seam?
Is the integration proof produced by the visible element that will paint the asset, rather than a hidden measurement proxy?
If feedback changed the topology, were affected assets, crops, measurements, and downstream QA explicitly invalidated and rebuilt from source evidence?
If the generated pixels contradict the topology, was the asset sent back to regeneration before HTML/CSS?
Did the cleanliness report verify isolation, alpha_edge_contamination, background contamination, and completeness?
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
- If the user asks for editable layers or coding assets, deliver or specify the canonical visual reference, asset-triage ledger, independently regenerated raster assets, real-text and CSS/SVG specs, asset manifest, and cleanliness report as one package.
- If the user asks for critique, be direct and identify concrete failure modes.
- If the user asks to revise this skill, preserve the progressive-disclosure structure: keep `SKILL.md` concise and place detailed reusable knowledge in `references/`.
- Use Japanese by default when the user writes in Japanese.
