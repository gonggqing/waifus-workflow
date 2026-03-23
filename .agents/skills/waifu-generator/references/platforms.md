# Platform Reference — Image Generation Syntax

Detailed syntax, parameters, and tips for each supported platform.

---

## Midjourney

Midjourney has multiple active model versions. Choose by content type:

| Use Case | Model | Why |
|---|---|---|
| **General / realistic / environments** | V8 Alpha (`--v 8`) | Best prompt understanding, fastest, 2K `--hd` |
| **Anime / game / 二次元 characters** | Niji 7 (`--niji 7`, V7 only) | Purpose-built for anime/illustration style |
| **Character consistency via `--cref`** | V6 (`--v 6`) | Only version with `--cref` + `--cw` support |

**Style:** Natural language, English preferred. Descriptive sentences work best.

---

### V8 Alpha (default for non-anime)

Available on `alpha.midjourney.com` only (not Discord). Model may change — it's an alpha.

**Prompt structure:**
```
[subject description], [art style], [lighting], [composition], [mood] --ar 2:3 --v 8 --raw
```

**Key parameters:**
- `--ar 2:3` — portrait (use `16:9` for landscape); HD max is 4:1
- `--v 8` — V8 Alpha model, 4–5x faster, much better prompt adherence
- `--raw` — removes default styling for maximum prompt control
- `--hd` — 2K native resolution (2048px, no upscaling needed; 4x GPU cost; max AR 4:1)
- `--sref [url/code]` — style reference (4x GPU cost; not compatible with `--hd` or `--q 4`)
- `--chaos` — variation; `--weird` — unconventional; `--exp` — experimental
- `--stylize` — MJ's aesthetic influence; `--tile` — seamless patterns
- `--seed` — reproducible results (~99% identical)
- `--q 4` — higher quality (4x GPU cost; only `1` or `4` in V8)
- `"quoted text"` — renders beautiful text in the image

**NOT supported in V8 Alpha:**
- `--cref` / `--cw` (character reference) — use V6 for character consistency
- `--no` (negative prompt parameter)
- `::` multi-prompting / prompt weighting
- Image prompts / `--iw` (image weight)
- `--niji` — use `--niji 7` on V7 for anime
- Upscalers / turbo mode

**GPU cost multipliers (V8):**
- `--hd` = 4x
- `--sref` / moodboards = 4x
- `--q 4` = 4x
- `--hd` + `--q 4` = 16x

**Tips:**
- V8 is much better at following detailed directions — be specific and direct
- Rephrase negatives as positives ("clean background" instead of trying to negate)
- Personalization profiles and moodboards are more accurate than ever in V8
- For 2K output, prefer `--hd` over upscaling (upscalers not available in V8)

**Example portrait (V8):**
```
silver-haired girl with amber eyes, black ribbon choker, school uniform, upper body portrait,
soft smile, detailed illustration, warm afternoon light, white background --ar 2:3 --v 8 --raw
```

---

### Niji 7 (anime / 二次元 characters)

Niji models are developed with Spellbrush, focused on Eastern and anime aesthetics. Niji 7 (launched Jan 9, 2026) has its own website and Discord server.

**What's new in Niji 7:**
- **Coherency boost** — fine details like eyes, reflections, and small background elements are much clearer
- **Better prompt adherence** — follows prompts more closely, good for specific designs and repeatable characters
- **More literal** — broad or "vibey" prompts may not behave the same as older niji; be specific
- **Cleaner, flatter look** — designed to highlight improved line work

Use `--niji 7` for any anime, game, manga, or 二次元 style output.

**Prompt structure:**
```
[subject description], [anime art style], [lighting], [composition], [mood] --ar 2:3 --niji 7 --raw
```

**When to use Niji 7 instead of V8:**
- Character has anime/manga/game aesthetic
- Cel shading, lineart, illustration style requested
- The worldview is described as 二次元, anime, or game-style
- User explicitly asks for anime/niji output

**Tips:**
- Be specific — Niji 7 is more literal than previous versions, vague prompts give weaker results
- Lean into the cleaner line work for character sheets and consistent designs
- Use `--raw` for maximum prompt control, same as V8

**Example (Niji 7):**
```
silver-haired girl with amber eyes, black ribbon choker, school uniform, upper body portrait,
soft smile, anime illustration style, detailed lineart, warm afternoon light, white background --ar 2:3 --niji 7 --raw
```

---

### V6 (character consistency workflows)

Use V6 only when `--cref` is needed for cross-image character consistency.

```
[prompt] --ar 2:3 --v 6 --style raw --cref [url] --cw 80
```

---

## SDXL (Stable Diffusion XL)

**Style:** Comma-separated tags. Positive + negative prompts. Booru-tag style works well.

**Positive prompt structure:**
```
masterpiece, best quality, highly detailed, [character base tags], [composition], [lighting], [mood], [art style]
```

**Standard negative prompt:**
```
lowres, bad anatomy, bad hands, text, error, missing fingers, extra digit, fewer digits, cropped, worst quality, low quality, normal quality, jpeg artifacts, signature, watermark, username, blurry, bad proportions, deformed, ugly, duplicate, morbid, mutilated, out of frame, extra limbs, disfigured
```

**Useful SDXL tags:**
- Quality: `masterpiece, best quality, ultra-detailed, 8k`
- Style: `anime coloring, cel shading, soft shading, watercolor`
- Lighting: `soft lighting, studio lighting, rim light, dramatic lighting`
- Composition: `upper body, bust shot, full body, close-up, cowboy shot`

**Recommended models for anime waifus:**
- Anything V5 / V5.5
- CounterfeitXL
- BluMix XL

**Tips:**
- Use `(parentheses)` for emphasis: `(silver hair:1.3)`
- Use `[brackets]` for de-emphasis: `[background clutter:0.5]`
- Add `1girl` as first tag for single character
- LoRA syntax: `<lora:lora_name:weight>` — weight 0.6–0.8 is usually safe

---

## PixAI

**Style:** Anime-focused, tag-based, very similar to SDXL/NAI. Strong LoRA ecosystem.

**Prompt structure:** Same as SDXL — comma-separated tags, positive + negative.

**Standard negative prompt:**
```
lowres, bad anatomy, bad hands, text, error, missing fingers, extra digit, fewer digits, cropped,
worst quality, low quality, normal quality, jpeg artifacts, signature, watermark, username, blurry,
nsfw (add if needed for SFW content)
```

**PixAI-specific tips:**
- Platform is well-suited for anime character consistency
- Browse PixAI's LoRA library for character-type LoRAs (school uniform, maid, etc.)
- Use `nai3` style tags: `amazing quality, very aesthetic, absurdres`
- For emotionally expressive characters, tag the expression explicitly: `teary eyes`, `shy smile`, `pout`

**Quality tags for PixAI:**
```
amazing quality, very aesthetic, absurdres, masterpiece, newest, sensitive
```

---

## LiblibAI (哩布哩布)

**Style:** Chinese-friendly platform. Supports both natural language and tag-based prompts depending on the selected model.

**Models:**
- **SDXL-based models** → use English/SDXL tag style (same as above)
- **FLUX-based models** → use natural language (similar to MJ, works well in Chinese or English)
- **SD1.5 anime models** → classic anime style, booru tags

**Chinese prompt tips:**
- For FLUX models, Chinese natural language prompts work well
- Mix Chinese and English: `一个银发少女，amber eyes，穿着学校制服，上半身特写`
- Platform has active Chinese model community — check model pages for recommended tags

**Platform-specific features:**
- Supports ControlNet for pose control (useful for consistent character angles)
- Has workflow templates for character sheets
- LoRA support similar to SDXL

**Recommended for:**
- Chinese-language waifu platforms needing CN-tuned models
- When the user wants a more localized aesthetic
- FLUX-based high-quality generation with Chinese prompts

---

## General Tips Across Platforms

**For character consistency:**
1. Generate a front-facing reference image first
2. Use that image as a character reference (ControlNet / `--cref`) for all subsequent generations
3. Keep the character base prompt word-for-word identical across prompts

**Aspect ratios:**
- Portrait: `2:3` or `3:4`
- Wallpaper/scene: `16:9`
- Square avatar: `1:1`
- Phone wallpaper: `9:16`

**Scene lighting quick reference:**
- Warm/cozy: `warm ambient light, golden hour, soft window light`
- Cool/melancholic: `overcast sky, diffused light, muted colors, blue tones`
- Dramatic: `rim lighting, high contrast, dramatic shadows, moody`
- Night: `moonlight, city lights bokeh, neon reflections, dark atmosphere`
- Fantasy: `magical glow, ethereal light, particles, sparkles`
