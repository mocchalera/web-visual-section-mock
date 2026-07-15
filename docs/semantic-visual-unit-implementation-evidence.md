# Semantic visual-unit implementation evidence

Date: 2026-07-14

## Acceptance behavior

- Static card interiors may be regenerated as one `card_artwork_plate` when their members have no independent motion, responsive recomposition, reuse, interaction, content update, or layering need; the same identifier passes unchanged into `mockup-to-code` as `assetUnit.kind`.
- Structural headings, labels, CTA, functional UI, live data, card shells, and page layout remain HTML/CSS.
- A split raster unit must record independent behavior and concrete `splitEvidence`.
- `minimumAtomicParts` records visible internal craft, not a mandatory DOM-node or file count.
- Same-card critical rasters sharing one `clipOwner` emit `asset-overdecomposition-risk` when the split has no independent-behavior evidence.

## Verification

### web-visual-section-mock

Command:

```text
node --test .project-loop/tmp/validate-package.test.mjs
```

Result: 3 passed, 0 failed. Package metadata, combined reference synchronization, and the clean semantic-unit asset contract passed.

Command:

```text
/usr/local/bin/python3 /Users/mocchalera/.codex/skills/.system/skill-creator/scripts/quick_validate.py .
```

Result: `Skill is valid!`

### mockup-to-code

Command:

```text
npm test
```

Result: 141 component tests passed. The contract-doctor subset passed 33/33, including:

- `test_allows_static_composite_as_one_card_artwork_plate`
- `test_flags_same_card_rasters_without_independent_split_evidence`
- `test_blocks_card_photo_without_asset_surface_ownership_contract`

Additional checks: schema and starter manifest parse as JSON, `contract_doctor.py` compiles, Skill Creator validation passes under `/usr/local/bin/python3`, and `git diff --check` passes in both skill repositories.
