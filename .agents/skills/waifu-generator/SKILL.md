---
name: waifu-generator
description: Generate image generation prompts for a waifu character from their definition file or JSON. Use this skill whenever the user wants to visualize a waifu, generate Midjourney/SDXL/PixAI/LiblibAI prompts, create a character sheet, get portrait variants (expressions, outfits, angles), or generate scene illustrations. Trigger on: "generate image", "MJ prompt", "SDXL prompt", "画图", "生成提示词", "出图", "character art", "scene illustration", or when the user wants to visualize a waifu. Also triggered by edit-waifu when appearance changes require prompt regeneration.
---

# Waifu Generator

A skill for turning waifu definitions into ready-to-use image generation prompts — structured for character consistency across portraits and scenes.

## Input

Accept any of:
- A `.md` waifu file produced by waifu-stories (preferred)
- A waifu JSON object pasted inline
- A file path to either

**Reading a markdown file** — extract from these sections:
- **外貌（不主动提及）** → core appearance: hair, eyes, outfit, posture
- **个性特质** → dominant mood, behavioral quirks, contrast states
- **角色定义** → role, setting, backstory context
- **标签** → genre and archetype (informs art style)
- Each **场景** block → read 场景描述, 开场白, and 场景内容 for environment and emotional cues

**Reading a JSON** — extract from `background_story`, `tags`, `initial_message`, `seed_chat`, and `scenes[].content`.

If no explicit appearance details exist, infer from archetype and tags. Flag all inferences in the 备注 section so the user can correct them.

**Platform selection:** If called from the create-waifu pipeline, default to SDXL unless the user specified a platform during the wizard. If used standalone and no platform is specified, ask — don't assume.

## What Gets Generated

1. **Base prompts** — 1–3 visual anchors depending on how many distinct looks the character has
2. **Portrait set** — minimum 4 variants, each built on a base
3. **Scene prompts** — 2–3 variants per scene, each with a short one-line description

---

## Step 1 — Build the visual profile

Before writing any prompts, read `references/platforms.md` for the target platform's syntax, parameters, and negative prompts.

Then extract:
- **Core appearance** — 3–5 traits that must appear in every prompt (these become the base)
- **Outfits** — signature outfit; any secondary outfit mentioned or implied
- **Default expression** — her resting emotional state
- **Contrast state** — if she has a notable shift (e.g., always cold but occasionally cracks), note it
- **Art style cue** — inferred from genre tags (anime, fantasy, modern, etc.)

---

## Step 2 — Build base prompts

A base is the shared visual anchor prefixed verbatim into every portrait and scene prompt. Never paraphrase a base — even small wording changes cause character drift across generations.

Generate:
- **Base A（常态）** — signature outfit, default expression, art style tags
- **Base B（换装）** — if a second outfit is clearly implied, generate a second base
- **Base C（情绪反转）** — if the character has a strong contrast state worth capturing separately

Keep each base under 60 tokens. Label and name each one.

Example:
```
[Base A — 制服 · 慵懒日常]
1girl, long blonde hair in loose side braid, half-lidded sleepy blue eyes,
guild receptionist uniform (blue white red, loose collar), relaxed slouch,
anime illustration, soft lineart, warm indoor light
```

---

## Step 3 — Portrait set

Minimum 4 portraits. Each portrait = one base + composition tag + expression/pose delta.

Label each with a short one-line description before the prompt block. The description is for the user's reference when selecting or adjusting images later.

Cover at minimum:
- 正面立绘 — front-facing, neutral expression, main outfit
- 四分之三侧 — three-quarter angle, same outfit
- 便服 — casual/off-duty look, softer expression
- 关键情绪 — the single emotion most tied to her character (reluctant warmth, suppressed exhaustion, hidden sharpness…)

Add more if the character has additional notable outfits or expressions.

---

## Step 4 — Scene prompts

For each scene, generate **2–3 prompt variants**. Each variant explores a different framing or moment within the same scene. Label each.

Pull from the scene:
- Environment and lighting from **场景内容**
- Mood from **场景描述**
- Emotional beat from **开场白**

Useful variant strategies:
- Wide environment shot vs. character close-up reaction
- The calm opening moment vs. the peak tension moment
- Solo framing vs. implied viewer/other presence

---

## Step 5 — Output format

Write the output to `concept-arts.md` in the character's folder (e.g., `worldview/[worldview-slug]/[character-slug]/concept-arts.md`), **not** inside `basics.md`. This keeps narrative definition and visual prompts cleanly separated.

Group by platform. Within each platform, order: bases → portraits → scenes.

```
# [角色名] — 视觉概念 / Concept Arts

## [Platform]

### 角色基底

**Base A · [label]**
```
[base prompt]
```

**Base B · [label]**  （如有）
```
[base prompt]
```

---

### 立绘组

> 正面立绘 · [one-line description]
```
[full prompt]
```

> 四分之三侧 · [one-line description]
```
[full prompt]
```

> 便服 · [one-line description]
```
[full prompt]
```

> [关键情绪] · [one-line description]
```
[full prompt]
```

---

### 场景插图

#### [场景名称]

> [variant 1 · one-line description]
```
[prompt]
```

> [variant 2 · one-line description]
```
[prompt]
```

> [variant 3 · one-line description]
```
[prompt]
```

---

### 备注

- 以下外貌细节为推断，请确认或修正：[list]
- 推荐模型/LoRA：[if applicable]
```

If generating for multiple platforms, repeat the full block per platform with a `---` separator between them.

---

## Prompt Writing Guidelines

**Composition tags:**
- Portraits: `upper body`, `bust shot`, `close-up`
- Scenes: `full body`, `cowboy shot`, `cinematic composition`
- Emotion close-ups: `face focus`, `detailed eyes`

**Lighting by mood:**
- Warm/cozy: `soft window light`, `warm ambient glow`, `golden hour`
- Dramatic: `rim lighting`, `high contrast`, `dramatic shadows`
- Melancholic: `overcast light`, `muted palette`, `diffused`
- Night: `moonlight`, `city lights bokeh`, `cool blue tones`

**Platform suffixes:**
- Midjourney: append `--v 6.1 --style raw --ar 2:3`
- SDXL/PixAI: prepend `masterpiece, best quality, highly detailed,`
- Negative prompt for SDXL/PixAI/LiblibAI: see `references/platforms.md`

## Reference Files

- `references/platforms.md` — full platform syntax, parameters, negative prompts. Read when generating for a specific platform.
