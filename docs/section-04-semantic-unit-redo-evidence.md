# Section 04 semantic-unit redo evidence

## Scope

The updated semantic visual-unit rules were applied to the approved Section 04 canonical image, followed by a fresh `mockup-to-code` HTML/CSS implementation.

## Output root

`/Users/mocchalera/.codex/visualizations/2026/07/14/019f5e62-1832-77e3-a464-a3759f3b1fea/web-visual-section-mock-lp/layered-clean-pilot/section_04_design_range/semantic-unit-redo`

## Decomposition result

- `person-card-artwork-v1.png`: portrait + local field + light/shadow
- `device-card-artwork-v1.png`: laptop + phone + complete static screens + surface/shadows
- `object-card-artwork-v1.png`: torus + plinth + contact/cast shadows
- `abstract-card-artwork-v1.png`: complete textured print composition
- TYPE artwork, structural text, labels, shell borders, registration marks and responsive layout remain code-native.

Every raster declares `assetUnit.kind: card_artwork_plate`, `splitPolicy: keep-together`, all independent-behavior flags false, a source evidence crop, exact prompt provenance and an adopted pre-generation receipt.

## Verification

- Pre-CSS contract doctor: pass
- Asset preflight: pass
- Generated asset cleanliness: `complete_clean_assets`
- Browser box diff: 17/17 pass
- Desktop/mobile visual check: pass
- Responsive width sweep: 320, 390, 768, 1024, 1440 and 1728 pass; no root overflow suppression and no clipped meaningful content
- Typography report: pass
- Artifact checklist: `needs_work` only for FV pixel diff
- Completion verdict: `prototype`
- Remaining computed reason: FV pixel diff `needs_work`, ratio `0.27597`; no blocked reasons

The computed prototype label is preserved verbatim. It reflects non-identical regenerated pixels, not a missing asset, invalid split, responsive failure or box mismatch.
