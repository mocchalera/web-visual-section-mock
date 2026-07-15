# Web-surface-first asset generation and handoff gate

Date: 2026-07-15

## User-observed failure

The LP typography improved, but several generated raster assets still appeared as detached rectangular images instead of belonging to the Web surface:

- the first-view key visual was reduced to a cropped right-side card even though the source scene owns the section field;
- the Problem section's long paper/site object was rendered with an unwanted rectangular background instead of floating on the section;
- the Section 05 exploded semantic-unit scene carried a white image plate instead of transparent shadows and panels;
- the three Section 06 scenes also carried image backgrounds that introduced double padding and color seams.

These are generation-planning failures, not CSS-polish failures. The image-generation stage must decide how generated pixels meet the Web surface before producing assets.

## Implemented contract

The decomposition model now records three independent decisions for each critical raster:

1. semantic generation unit: what must stay visually coupled;
2. source surface topology: whether the source is a `section_field`, `floating_scene`, `contained_artwork`, `tone_merged_object`, or `source_visible_frame`;
3. Web integration mode and four-edge policy: alpha, bleed, mask, tone match, source frame, or not visible.

Two generation units were added:

- `full_field_scene_plate` for a scene that owns the full section and preserves measured copy space;
- `transparent_scene` for coupled multi-object scenes, exploded diagrams, collages, and their soft shadows on alpha.

The `mockup-to-code` pre-CSS gate now blocks an opaque framed substitute when the source says `section_field` or `floating_scene`. It also requires topology evidence, compatible integration mode, edge policy, and pixel behavior before implementation begins.

## Acceptance coverage

The automated acceptance suite covers:

- blocking a section-field hero demoted to an invented consumer frame;
- blocking an opaque floating scene;
- accepting a `transparent_scene` with real alpha and shadow pixels;
- accepting a `full_field_scene_plate` with measured copy space and asset-owned bleed;
- retaining contained card artwork as a valid distinct case.

## Verification

- `web-visual-section-mock`: package tests passed, 3/3.
- `web-visual-section-mock`: Skill Creator validation passed.
- `mockup-to-code`: full component suite passed, 150/150.
- `mockup-to-code`: Skill Creator validation passed.
- JSON schemas, example manifests, Python compilation, and `git diff --check` passed.

## Dogfood against the current LP

The updated preflight was run against the existing full-page LP manifest. It returned:

- status: `blocked`;
- implementation allowed: `false`;
- completion allowed: `false`;
- blocked critical rasters: 18/18.

The missing fields are `sourceTopology`, `sourceTopologyEvidencePath`, and `edgePolicy`. This is the intended result: the existing assets must return to the generation-design phase instead of being cosmetically patched with CSS.

Artifacts:

- `.project-loop/tmp/current-lp-surface-topology-gate.json`
- `/Users/mocchalera/.codex/skills/mockup-to-code/test/run_asset_preflight_tests.py`
- `/Users/mocchalera/.codex/skills/mockup-to-code/VALIDATION.md`
