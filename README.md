# web-visual-section-mock

Agent Skill for high-quality WEB / LP visual mock art direction with section-by-section image generation, Japanese typography, intentional main visual archetype selection, editable layer/part asset packages with exact reconstruction, graphic composition, color/photo/fashion direction, and strict anti-cropping / anti-stuffing rules.

## Package structure

```text
web-visual-section-mock/
  SKILL.md
  references/
    00_reference_index.md
    01_web_section_patterns.md
    02_web_layout_patterns.md
    03_graphic_composition_patterns.md
    04_japanese_typography_patterns.md
    05_color_lighting_fashion_patterns.md
    06_industry_tone_patterns.md
    07_negative_examples.md
    08_prompt_templates.md
    09_main_visual_archetypes.md
    10_visual_decomposition_for_web_build.md
    11_layered_asset_generation_and_reconstruction.md
    web_visual_agent_references_all.md
  assets/
    brief_template.md
    output_examples.md
```

## Install

Download this repository as a ZIP, or create a ZIP from the `web-visual-section-mock/` folder, then upload it as an Agent Skill.

The package must contain a single top-level folder with one `SKILL.md` manifest and its supporting `references/` and `assets/` files.

## Main use cases

- Create image generation prompts for WEB / LP visual mockups
- Split a full landing page into section-by-section images
- Design hero, problem, benefit, features, pricing, FAQ, CTA, and footer visuals
- Use person-led visuals as a strong default while intentionally switching to other motifs when stronger
- Decompose reference comps into layer stack, full-bleed/container elements, alignment bases, and asset strategy before prompt writing
- Generate reconstruction layers and reusable trimmed assets, then prove the manifest-driven recomposition matches the canonical image pixel-for-pixel
- Apply Japanese typography patterns
- Avoid cropped section screenshots and overstuffed page images
- Diagnose weak or cheap-looking image prompts

## Core principle

```text
1 image = 1 section = 1 clear message.
Never compress a whole WEB page into one image.
Choose one main visual archetype per section.
Use person-led visuals as a strong default hook; switch to another motif only when it is more intentional.
When uncertain, choose whitespace or split the section.
When layered output is requested, decoded RGBA must satisfy pixel_diff_count = 0 and max_channel_delta = 0; otherwise exact fidelity is unproven.
```

## Validation

This is an instruction-only skill: no scripts, runtime dependencies, binaries, or generated assets are required.

See `PACKAGE_VALIDATION.md` for the current package check summary.
