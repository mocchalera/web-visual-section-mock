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
- layered exact: reconstruction layers + reusable assets + manifest + zero-diff proof

Required editable parts:
- background plate
- person / product / UI cutout
- icons
- hand lettering / arrows / underlines
- readable text spec
- SVG/CSS candidates

Exact reconstruction required:
- yes: decoded RGBA pixel_diff_count = 0 and max_channel_delta = 0
- no: perceptual approximation is acceptable

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
Assumption 6: layer output = composite only unless later editing, part separation, or HTML/CSS asset use is requested; then use layered exact
```
