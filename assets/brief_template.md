# Brief Template

Use this template when collecting or structuring a WEB visual mock brief.

```text
Project / brand name:
Product or service:
Target audience:
Main conversion goal:
Desired emotional center:
Existing assets:
Reference images or URLs:
Must include text:
Must avoid:
Main visual candidates:
- person
- product / object
- UI / product screen
- place / space
- typography-led
- abstract graphic
- data / process
- evidence / artifact
Human presence:
- necessary
- supporting only
- avoid / not needed
Preferred tone:
Output needed:
- single visual
- section prompts
- actual section images
- layered editable asset package
- critique / revision

Layer / asset output mode:
- composite only
- layer plan only
- layered clean: independently regenerated raster assets + text/CSS/SVG specs + manifest + cleanliness proof

Source surface topology (choose before each raster prompt):

- section field: full-field scene owns the section and leaves measured copy space
- floating scene: transparent object or coupled transparent scene, including its soft shadow
- contained artwork: raster fills a source-visible card/photo/screen content box
- tone-merged object: alpha, measured mask, or measured edge-tone merge
- source-visible frame: keep a hard rectangle only when the comp visibly shows it

Four-edge policy:

- top / right / bottom / left = alpha / bleed / mask / tone match / source frame / not visible

Raster surface integration (choose per asset, separately from generation unit):

- alpha floating
- opaque full bleed
- opaque masked merge
- opaque tone matched
- intentional source-visible frame

Required editable parts:
- full-field scene plate with copy-space ROIs and responsive focal points when the visual owns the section world
- background plate
- person / product / complex illustration as regenerated transparent asset
- coupled transparent scene for exploded layers, device groups, paper objects, or collages that float on the Web field
- static card interior as one opaque artwork plate when its members always move and scale together
- complex raster texture or atmosphere
- readable text spec
- SVG/CSS candidates for icons, waves, arrows, lines, masks, badges, and simple shapes

Production asset rule:
- canonical composite is reference only
- no crop, mask, or residual from the canonical may be shipped as a production asset
- one semantic output visual unit per generation; coupled objects may share one unit
- do not turn a section field into an invented image card or a floating scene into an opaque rectangle
- verify the actual PNG alpha, outer bands, content bounds, and edge color inside its final Web consumer; CSS and raster must not both own the same background or padding
- if source topology, generated pixels, and final consumer disagree, mark needs_surface_redesign and return to image generation before HTML/CSS

Section-critical typography handoff:

- measure source block/line/glyph ratios, tracking, line advance, ink density, heading-to-body jump ratios, and desktop/mobile scale bounds
- do not reuse one generic bbox/type spec across distinct section comps without source evidence
- readable text and efficiently reproducible CSS/SVG parts are not raster-generated
- transparent foreground or clean opaque background plate required

Expected sections:
- Hero
- Problem
- Benefit
- Features
- Case Study
- Pricing
- FAQ
- CTA
- Footer
```

## Minimal brief fallback

If the user gives only a rough idea, proceed with assumptions:

```text
Assumption 1: target audience = inferred from service
Assumption 2: conversion goal = inquiry / signup / purchase
Assumption 3: visual tone = bright, editorial, high-quality, Japanese typography-led
Assumption 4: output = section-by-section prompts unless actual images are requested
Assumption 5: human presence = default visual hook unless product, UI, space, typography, abstract graphic, data, or artifact is more intentional
Assumption 6: layer output = composite only unless later editing, part separation, or HTML/CSS asset use is requested; then use layered clean
```
