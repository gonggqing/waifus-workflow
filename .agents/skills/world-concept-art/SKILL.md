---
name: world-concept-art
description: Generate environment and scene image prompts for a worldview — the visual foundation of the world itself (locations, architecture, atmosphere, lighting). Called automatically by world-building after creating or expanding world/factions files, or standalone when you want concept art for a world. Trigger on: "世界概念图", "world concept art", "场景提示词", "environment prompts", "世界视觉", "world visuals", "location art", "generate world art", "场景图", or when the user wants to visualize locations/environments rather than characters. Do NOT use for character image prompts — use waifu-generator instead.
---

# World Concept Art

Generate environment/scene-level image prompts from worldview files. This skill answers "what does the world look like" — not "what do the characters look like" (that's waifu-generator's job).

**Output:** `worldview/[slug]/concept-art/` folder with structured, reusable environment prompts.

**Relationship to other skills:**
- **Upstream:** world-building (provides world.md + factions.md as input)
- **Downstream consumers:** waifu-generator (scene backgrounds for character-in-context images), create-campaign (establishing shots, video backgrounds, mood pieces)
- **Sibling:** waifu-generator (characters) vs. world-concept-art (environments) — they share platform adaptation logic and their outputs can be combined

---

## Two Modes of Operation

### Mode 1 — 初始生成 / Initial Generation

Triggered when `concept-art/` folder doesn't exist for this worldview.

### Mode 2 — 增量更新 / Incremental Update

Triggered when `concept-art/` already exists and world-building has added new locations or changed world descriptions that affect visuals. Append new entries without rewriting existing ones, unless the visual foundation (color palette, lighting system) has changed — in that case, update `overview.md` and note which existing location prompts may need refresh.

---

## Step 1 — Read Worldview Files

Read from `worldview/[slug]/`:
- **`world.md`** — extract from 世界概览 / World Overview and 世界规则 / World Rules:
  - Geography and terrain types
  - Climate and weather
  - Magic/tech visual manifestations (how does the world's power system look?)
  - Time period and architectural era
  - Overall mood and tonal cues
- **`factions.md`** — extract from 重要地点 / Key Locations:
  - Each named location's description
  - Architectural style and materials
  - Atmosphere and function
  - Any faction-specific visual markers (banners, symbols, lighting)

If `concept-art/overview.md` already exists (Mode 2), read it to maintain consistency with existing visual decisions.

---

## Step 2 — Visual Element Extraction

Systematically extract these dimensions from the text:

| Dimension | What to Extract | How It's Used |
|---|---|---|
| **色调基底 / Color Palette** | Primary colors, accent colors, seasonal variations | Every prompt's color grading keywords |
| **光线系统 / Lighting System** | Default light source, time-of-day variations, special light effects | Lighting keywords in every prompt |
| **地形地貌 / Terrain** | Landscape types, scale, natural features | Wide/establishing shot compositions |
| **建筑风格 / Architecture** | Building types, materials, ornamental style, scale | Medium shots and interiors |
| **氛围微粒 / Atmospheric Particles** | What's in the air — mist, particles, petals, sparks, rain | Atmosphere layer in every prompt |
| **标志物件 / Signature Objects** | World-specific visual icons — artifacts, vehicles, creatures | Detail shots and foreground elements |

For anything not described in the source text, leave it out — don't invent visual details that the world-building hasn't established.

---

## Step 3 — Generate Prompts

### overview.md — Visual Foundation

Write a concise visual specification that anchors all location prompts:

```markdown
# [世界名称] — 视觉总纲 / Visual Foundation

## 色调基底 / Color Palette
[Primary colors + accent colors + mood associations. E.g., "青黛+云白+金霞; night scenes add 月蓝"]

## 光线系统 / Lighting System
[Default lighting + time-of-day variants + special effects. E.g., "Default: soft diffused light through cloud layers; Dawn/dusk: golden sidelight; Night: moonlight + floating spirit particles"]

## 氛围常量 / Atmospheric Constants
[What's always in the air — fog, particles, weather patterns. E.g., "Thin mountain mist always present; spirit energy manifests as faint floating light dots"]

## 风格锚定 / Style Anchor
[Art style keywords that apply to all prompts. E.g., "Chinese ink painting meets cinematic realism; ethereal, serene, vertical composition emphasis"]

## 可组合关键词 / Composable Keywords
- **背景层 / Background:** `[reusable background snippet]`
- **光线层 / Lighting:** `[reusable lighting snippet]`  
- **氛围层 / Atmosphere:** `[reusable atmosphere snippet]`
```

### locations.md — Per-Location Prompts

For each location found in factions.md's 重要地点 section, generate multi-angle prompts:

```markdown
### [地点名] — [English Name]

**全景 / Wide Establishing Shot:**
[prompt: far view showing the location's scale and environmental context]

**中景 / Medium Environmental Shot:**
[prompt: closer view showing architectural details and atmosphere]

**室内或近景 / Interior or Detail Shot:** (if applicable)
[prompt: interior environment or signature detail close-up]

**夜景变体 / Night Variant:** (if the location has a distinct night identity)
[prompt: same location under night lighting]

**情绪变体 / Mood Variants:**
- 平静 / Calm: `[prompt — serene, peaceful version]`
- 紧张 / Tense: `[prompt — stormy, ominous, red-tinted]`
- 史诗 / Epic: `[prompt — golden hour, dramatic scale, cinematic]`

**可组合片段 / Composable Fragments:**
- 背景层: `[background-only prompt, appendable to character prompts]`
- 光线层: `[lighting keywords for this location]`
- 氛围层: `[atmospheric particle/weather keywords]`
```

Prioritize locations that are:
1. Explicitly described in factions.md with enough visual detail to work from
2. Likely to appear in character scenes (check if any character scenes reference them)
3. Visually distinctive (skip generic locations like "a road" unless they're important)

### atmosphere.md — Time/Weather/Event Prompts

Environment prompts not tied to specific locations — these are mood/condition overlays:

```markdown
### [时间/天气/事件] — [English Name]

[prompt: atmospheric condition that can be layered onto any location]

**适用场景 / Use Cases:** [when to use this — e.g., "tension scenes", "peaceful moments", "world-level crisis"]
```

Cover at minimum:
- 2–3 time-of-day variants (dawn, midday, night)
- 1–2 weather conditions relevant to this world
- 1 special event atmosphere (if the world has notable visual events — festivals, magical phenomena, etc.)

### details.md — Micro-Level Visual Elements

Close-up prompts for world-specific objects, textures, and symbols:

```markdown
### [物件/纹饰名] — [English Name]

[prompt: detailed close-up of this world-specific visual element]

**用途 / Usage:** [where this appears — architecture, clothing, artifacts, etc.]
```

Only generate entries for items explicitly mentioned in the world files. This file grows incrementally as the world develops.

---

## Output Structure

```
worldview/[slug]/concept-art/
├── overview.md      ← visual foundation (color palette, lighting system, style anchor, composable keywords)
├── locations.md     ← per-location multi-angle prompts (wide, medium, detail, night, mood variants)
├── atmosphere.md    ← time/weather/event atmospheric overlays
└── details.md       ← world-specific objects, textures, symbols
```

---

## Prompt Writing Rules

**Language:** All prompts in English — even for Chinese-language worlds. AI image models trained primarily on English captions.

**One prompt = one image.** Don't describe multiple views or time transitions in a single prompt.

**Structure each prompt as:** `[composition/shot type], [main subject], [key details], [lighting], [atmosphere], [style keywords], [quality tags]`

**Always include from overview.md:** the style anchor keywords and at least one composable keyword layer (background, lighting, or atmosphere) to maintain visual consistency across all generated images.

**Quality tags:** append `4K, highly detailed` for SDXL/PixAI; use `--v 6.1 --style raw` for Midjourney. Follow the same platform conventions as waifu-generator's `references/platforms.md`.

**Don't over-specify.** Leave room for the model's interpretation — 40-80 tokens per prompt is the sweet spot. Over-packed prompts cause muddy outputs.

---

## Incremental Update Rules (Mode 2)

When called after world-building expands an existing world:

1. **New location added to factions.md** → append its section to `locations.md`
2. **Existing location description changed** → update that location's prompts in place, flag with `<!-- 🔄 updated: [reason] -->`
3. **World overview/rules changed with visual impact** → update `overview.md`'s affected sections (color palette, lighting, etc.) and add a note: `<!-- ⚠️ visual foundation changed — existing location prompts may need refresh -->`
4. **New atmospheric phenomenon or world event** → append to `atmosphere.md`
5. **New world-specific object/symbol** → append to `details.md`
6. **No visual changes** (only character/timeline additions) → do nothing, report "no visual changes detected"

---

## After Writing

Report:
- Files created or updated
- Number of locations with prompts
- Any locations skipped (insufficient visual description) — suggest enriching them in factions.md
- Whether overview.md visual foundation changed (affects existing prompts)
