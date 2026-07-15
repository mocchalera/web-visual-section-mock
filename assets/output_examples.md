# Output Examples

## Multi-section output plan

```text
section_01_hero
- role: create the first visual impact
- message: the one transformation the brand promises
- main visual archetype: Person-led as the default hook; switch to Typography-led / Product-led / UI-led / Abstract Graphic-led only when it is more intentional
- visual: selected main visual + oversized Japanese typography
- layout: Split Hero
- typography: Kanji emphasis + kana suppression
- spacing: large top/bottom whitespace

section_02_problem
- role: make the reader's hidden frustration visible
- message: the current way feels heavier than it should
- main visual archetype: Person-led / Symbolic Object-led / UI-led / Typography-led / Abstract Graphic-led
- visual: a person expressing the hidden tension, or a symbolic object/UI friction if that motif is sharper
- layout: Editorial Grid
- typography: giant single character or short poetic line
- spacing: intellectual whitespace, not fear-based density

section_03_benefit
- role: show the bright change after using the service
- message: work/life becomes lighter and clearer
- main visual archetype: Person-led / UI-led / Abstract Graphic-led / Place-led / Product-led
- visual: bright person-led change scene, floating UI cards, or abstract graphic system + vivid accent color
- layout: Floating Card Layout
- typography: two-step copy lockup
- spacing: max 3 benefit cards
```

## Single section image prompt skeleton

```text
<generate_image section_id="section_01_hero">
Create one complete high-quality WEB hero section image.
Do not show the whole landing page.
Do not crop the section halfway.
Choose one primary main visual archetype; start from Person-led as a strong visual hook unless another motif is more intentional for the message.
Use one clear message, oversized Japanese typography, a bright advertising-grade visual, and generous whitespace.
The main copy must be readable on smartphone.
CTA, brand name, subject, and readable text must not be cut off.
Prefer whitespace over dense layout.
</generate_image>
```

## Layered asset package output

```text
section_01_hero/
- canonical reference: approved 1440x900 image, reference_only=true
- source surface topology: hero world = section_field; foreground product = floating_scene; a real card interior = contained_artwork
- semantic units: copy-space-aware full_field_scene_plate and independently moving subject; a static device-card scene remains one card_artwork_plate
- asset triage: raster units = raster_regenerate; heading/CTA = real_text; wave/icon = css_svg_rebuild
- raster assets: newly generated full-field scene or clean background plate, independently regenerated transparent subject/scene PNG
- code assets: text spec, SVG/CSS spec, responsive relationships
- asset manifest: provenance, generation prompt, source topology evidence, four-edge policy, copy space/focal points, alpha mode, intended role, anchor, responsive relationship
- proof: checkerboard/white/black/brand previews and asset-cleanliness-report.json
- acceptance: no canonical crop/mask, one output unit per generation, no structural text or card chrome, complete contours/crop-safe plates, clean alpha edges where applicable, no invented opaque frame, topology/generation/integration agreement in the final consumer
- status: complete_clean_assets
```
