# Platform Reference — Image Generation Syntax

Detailed syntax, parameters, and tips for each supported platform.

---

## Midjourney

**Style:** Natural language, English preferred. Descriptive sentences work best.

**Prompt structure:**
```
[subject description], [art style], [lighting], [composition], [mood] --ar 2:3 --v 6.1 --style raw --q 2
```

**Key parameters:**
- `--ar 2:3` — portrait aspect ratio (use `16:9` for landscape scenes)
- `--v 6.1` — latest model (as of early 2025)
- `--style raw` — less opinionated output, better for character consistency
- `--q 2` — high quality
- `--cref [url]` — character reference image for consistency across generations
- `--sref [url]` — style reference
- `--chaos 0` — low chaos = more predictable output

**Tips:**
- Put the most important descriptors first — MJ weights earlier tokens more
- Use `::` for emphasis or de-emphasis: `silver hair::2` (boost), `wings::0` (suppress)
- No negative prompts in MJ — rephrase as positive ("clean background" instead of "no background")
- For character consistency across scenes, use `--cref` with a reference image once you have one

**Example portrait:**
```
silver-haired girl with amber eyes, black ribbon choker, school uniform, upper body portrait,
soft smile, anime illustration style, detailed lineart, warm afternoon light, white background --ar 2:3 --v 6.1 --style raw --q 2
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
