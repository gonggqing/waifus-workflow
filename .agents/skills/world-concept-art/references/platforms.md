# Platform Reference — Environment & Scene Prompts

Syntax, parameters, and tips for each supported image/video platform, focused on **environment and scene generation** (not character art — see waifu-generator for that).

---

## Midjourney

### Model Selection

| Use Case | Model | Suffix |
|---|---|---|
| **Environments / realistic / cinematic** | V8 Alpha | `--v 8` (add `--raw` when needed) |
| **Anime / 二次元 style worlds** | Niji 7 | `--niji 7` (add `--raw` when needed) |

Default: V8 Alpha for all worlds except explicitly anime/二次元 ones.

**When to use `--raw`:** `--raw` removes MJ's default aesthetic styling and gives the prompt full control over the output. Use it for architectural precision shots, technical detail close-ups, or when matching a specific art style where MJ's beautification would interfere. Omit it for mood/atmosphere shots, establishing shots with dramatic lighting, or painterly scenes where MJ's aesthetic polish improves the result. Not every prompt needs `--raw` — decide per prompt based on whether you want maximum fidelity to the prompt text or MJ's interpretation.

---

### V8 Alpha — Environments (default)

Available on `alpha.midjourney.com` only. Best prompt understanding, 4–5× faster.

**Prompt structure for environments:**
```
[shot type / composition], [main subject — location or scene], [key architectural / terrain details],
[lighting], [atmospheric particles], [color palette], [style anchor], 4K, highly detailed
--ar 16:9 --v 8
```

Add `--raw` when the prompt needs precise control (e.g., architectural detail, specific art style):
```
... 4K, highly detailed --ar 16:9 --v 8 --raw
```

**Recommended aspect ratios for scenes:**

| AR | Use Case |
|---|---|
| `16:9` | Establishing / wide shots, landscapes, skylines |
| `21:9` | Ultrawide cinematic panoramas (max standard AR) |
| `9:16` | Vertical architecture, towers, cliffs, stairwells |
| `4:3` | Interiors, medium shots, balanced compositions |
| `3:4` | Narrow passages, detail shots with vertical emphasis |
| `1:1` | Icons, texture swatches, symbol close-ups |

**Key parameters:**
- `--v 8` — V8 Alpha model
- `--raw` — removes default styling for maximum prompt control. Use selectively: great for architectural precision and specific art style matching; skip for mood/atmosphere shots where MJ's polish helps
- `--hd` — 2K native resolution (2048px; 4× GPU cost; max AR 4:1). Strongly recommended for environment prompts — landscapes benefit greatly from resolution
- `--sref [url/code]` — style reference for visual consistency across locations (4× GPU cost; not compatible with `--hd` or `--q 4`)
- `--q 4` — higher quality (4× GPU; only `1` or `4` in V8)
- `--seed` — reproducible results (~99% identical) — useful for generating consistent location variants (day/night/mood)
- `--chaos` — variation; `--weird` — unconventional; `--tile` — seamless texture patterns

**NOT supported in V8 Alpha:**
- `--cref` / `--cw` (character reference)
- `--no` (negative prompt)
- `::` multi-prompting / prompt weighting
- Image prompts / `--iw`
- `--niji` — use Niji 7 separately for anime worlds
- Upscalers / turbo mode

**GPU cost multipliers (V8):**
| Combo | Cost |
|---|---|
| `--hd` | 4× |
| `--sref` / moodboards | 4× |
| `--q 4` | 4× |
| `--hd` + `--q 4` | 16× |

**Environment-specific tips:**
- V8 excels at complex scene composition — describe spatial relationships explicitly ("a river winding between two mountain ranges", "temple on a cliff overlooking the sea")
- Include atmospheric particles for depth: fog, mist, dust motes, petals, rain, sparks, fireflies
- Specify time of day and light direction for consistent mood across location variants
- Use `--seed` + different lighting/mood suffixes to generate day/night/weather variants of the same location
- Rephrase negatives as positives ("clear sky" not "no clouds")
- For 2K output, prefer `--hd` over upscaling (upscalers not available in V8)

**Example (V8 wide establishing shot):**
```
wide establishing shot, ancient mountain temple complex built into cliffside, multiple pagoda-roofed buildings
connected by covered stone bridges, waterfall cascading beside the main hall, morning mist rising from valley below,
golden sunrise light breaking through cloud layers, pine trees clinging to rock faces, ethereal and serene,
Chinese ink painting meets cinematic realism, 4K, highly detailed --ar 16:9 --v 8 --raw
```

**Example (V8 mood/atmosphere — no --raw, letting MJ enhance the mood):**
```
wide establishing shot, misty valley at dawn, layered mountain ridges fading into golden haze,
a single ancient temple silhouette on a distant peak, soft volumetric light through cloud breaks,
peaceful and transcendent, warm amber and cool blue gradient, 4K, highly detailed --ar 16:9 --v 8
```

---

### Niji 7 — Anime / 二次元 Worlds

Use for worlds explicitly styled as anime, game, manga, or 二次元. Developed with Spellbrush, launched Jan 9, 2026.

**Key traits:**
- **Coherency boost** — fine background details (reflections, small elements) much clearer
- **More literal** — be specific; vague/viby prompts give weaker results
- **Cleaner, flatter look** — highlights improved line work, great for anime backgrounds

**Prompt structure:**
```
[shot type], [scene description], [architectural / environment details],
[lighting], [atmosphere], [anime background style], 4K, highly detailed
--ar 16:9 --niji 7
```

Add `--raw` for precise art direction (specific lineart style, exact color control); omit for general anime backgrounds:
```
... 4K, highly detailed --ar 16:9 --niji 7 --raw
```

**When to use Niji 7 for environments:**
- Worldview is described as 二次元, anime, or game-style
- Art direction calls for cel shading, illustration, or flat color backgrounds
- Slice-of-life, school, or Japanese-setting worlds with anime aesthetic

**Example (Niji 7 background):**
```
wide shot, Japanese high school campus in late spring, cherry blossom trees in full bloom along covered walkways,
sports field visible in background, soft pink petals drifting, bright clean sunlight, warm pastel palette,
anime background style, 4K, highly detailed --ar 16:9 --niji 7
```

---

## Seedance — AI Video (Environment Motion)

Seedance is an AI video generation platform best suited for **environment establishing shots and atmospheric motion** — drifting fog, flowing water, swaying trees, time-of-day transitions.

**Prompt approach:** Natural language, descriptive. Focus on **one continuous camera motion** per prompt.

**Prompt structure:**
```
[camera motion], [scene description with motion elements], [lighting], [atmosphere], [mood]
```

**Camera motions for environments:**
| Motion | Best For |
|---|---|
| `slow dolly forward` | Entering a location, revealing depth |
| `slow aerial pullback` | Establishing shots, showing scale |
| `slow pan left/right` | Panoramic sweeps across landscapes |
| `static locked camera` | Atmospheric loops — rain, mist, fireflies |
| `slow crane up` | Revealing vertical architecture, towers |
| `slow orbit` | 360° around a landmark or structure |

**Environment-specific tips:**
- Describe motion in the scene itself: "fog drifting through trees", "water flowing over rocks", "lanterns swaying in wind"
- Keep camera motion simple — one direction per prompt
- 12s max generation length; one continuous shot per prompt
- For location tours, generate multiple 12s cuts and stitch in post
- Best output at 16:9 or 21:9 for cinematic feel

**Example:**
```
slow dolly forward through a moonlit bamboo forest, thin mist drifting between tall stalks,
fireflies floating in warm amber clusters, soft blue moonlight filtering through the canopy,
leaves rustling gently, peaceful and mysterious atmosphere
```

---

## Nano-banana Pro — Image Generation

Nano-banana Pro is a high-quality image generation model strong at **detailed environments, stylized rendering, and painterly aesthetics**.

**Prompt style:** Natural language, English. Descriptive sentences similar to Midjourney. No special parameter syntax — quality and style are controlled by the model's built-in strengths.

**Prompt structure:**
```
[composition / shot type], [main subject], [key details], [lighting], [atmosphere], [style], [quality cues]
```

**Environment-specific tips:**
- Lean into painterly and stylized descriptions — the model handles artistic interpretation well
- Explicit color palette instructions help maintain visual consistency
- Specify art medium when desired: "digital painting", "matte painting", "concept art", "watercolor"
- Detailed spatial descriptions produce strong depth and composition

**Example:**
```
wide establishing shot, ancient port city at sunset, layered stone buildings cascading down a hillside
to a crowded harbor, fishing boats with colored sails, warm amber and coral sunset light reflecting on calm water,
seabirds silhouetted against the sky, Mediterranean fantasy aesthetic, digital matte painting, highly detailed
```

---

## SDXL (Stable Diffusion XL) — Environments

**Prompt style:** Comma-separated tags. Positive + negative prompts. Tag-based works best.

**Positive prompt structure for environments:**
```
masterpiece, best quality, highly detailed, [environment type], [composition/shot],
[architectural details], [terrain], [lighting], [atmosphere], [color palette], [art style], 8k
```

**Standard negative prompt for environments:**
```
lowres, text, error, cropped, worst quality, low quality, normal quality, jpeg artifacts,
signature, watermark, username, blurry, deformed, ugly, oversaturated, underexposed,
people, characters, figures, person
```

> Add `people, characters, figures, person` to negatives for environment-only shots.

**Useful environment tags:**
- **Quality:** `masterpiece, best quality, ultra-detailed, 8k, absurdres`
- **Composition:** `wide shot, establishing shot, panoramic, bird's eye view, low angle, interior`
- **Style:** `concept art, matte painting, digital painting, photorealistic, anime background`
- **Lighting:** `golden hour, dramatic lighting, volumetric lighting, god rays, moonlight, ambient occlusion`
- **Atmosphere:** `fog, mist, rain, snow, dust particles, lens flare, depth of field`
- **Architecture:** `gothic, art nouveau, brutalist, traditional Japanese, ancient ruins, futuristic`

**Recommended models for environments:**
- **SDXL base** — good general-purpose environments
- **JuggernautXL** — cinematic realism, landscapes
- **DreamShaper XL** — painterly/fantasy environments
- **CounterfeitXL** — anime-style backgrounds and scenes

**SDXL-specific tips:**
- Use `(emphasis:weight)` for important elements: `(volumetric fog:1.3)`, `(golden hour lighting:1.2)`
- `no_humans` tag helps keep environments character-free
- ControlNet with depth/canny maps gives precise architectural control
- For consistent locations across angles, use the same seed + similar prompt structure + different composition tags
- LoRA syntax: `<lora:lora_name:weight>` — environment LoRAs can add specific architectural or biome styles

**Example (SDXL environment):**
```
masterpiece, best quality, ultra-detailed, wide shot, ancient mountain temple complex,
multiple pagoda buildings connected by stone bridges, waterfall, morning mist, pine trees,
golden sunrise light, volumetric god rays, Chinese fantasy, concept art, matte painting, 8k, no_humans
```

**Negative:**
```
lowres, text, error, worst quality, low quality, jpeg artifacts, watermark, blurry, people, characters, person
```

---

## Platform Suffix Quick Reference

Append these to prompts generated from the SKILL.md templates:

| Platform | Suffix |
|---|---|
| MJ V8 (default) | `--ar [ratio] --v 8` |
| MJ V8 + raw | `--ar [ratio] --v 8 --raw` |
| MJ V8 + 2K | `--ar [ratio] --v 8 --hd` |
| MJ V8 + 2K + raw | `--ar [ratio] --v 8 --raw --hd` |
| MJ Niji 7 (anime) | `--ar [ratio] --niji 7` |
| MJ Niji 7 + raw | `--ar [ratio] --niji 7 --raw` |
| Seedance | *(no suffix — natural language only)* |
| Nano-banana Pro | *(no suffix — natural language only)* |
| SDXL | *(tag-based, add negative prompt separately)* |
