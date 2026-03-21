# AI 视频生成指南 / Video Prompt Guide

Technical reference for generating AI video prompts in waifu campaigns. All video prompts use English regardless of worldview language.

---

## Prompt Structure Formula

```
[Shot Type] + [Subject] + [Action/Emotion] + [Setting] + [Lighting] + [Style] + [Technical]
```

**Example breakdown:**
```
"Cinematic slow dolly-in on a silver-haired girl sitting alone at guild window,
she looks up from her newspaper with half-lidded sleepy eyes, sunset light
streaming through stained glass, warm color grading, anime illustration style,
4K, shallow depth of field"

Shot:      Cinematic slow dolly-in
Subject:   silver-haired girl at guild window
Action:    looks up from newspaper with sleepy eyes
Setting:   guild window, stained glass
Lighting:  sunset light streaming through
Style:     warm color grading, anime illustration
Technical: 4K, shallow depth of field
```

---

## Cut-Based Composition

Current AI video models generate **~10-12 seconds** per generation. Longer videos are composed by stitching multiple cuts in editing software.

- **1 cut = 1 AI video generation ≈ 10-12s** (recommend 12s)
- Each cut gets one prompt — one continuous shot, no mid-cut transitions
- Total duration = number of cuts × seconds per cut
- Post-production: stitch cuts in editing software (CapCut / Premiere / DaVinci), add transitions/subtitles/BGM

---

## Shot Type Reference

| Shot | Purpose | Campaign Use |
|---|---|---|
| Wide / establishing shot | Show overall space | World panorama, faction HQ, city skyline |
| Medium shot (waist-up) | Character in context | Character's natural state in their environment |
| Close-up | Emotional delivery | Expression changes, opening line accompaniment |
| Extreme close-up | Detail / suspense | Eye movement, hand gesture, iconic object |
| Low angle | Power / menace | Hidden-powerhouse reveal, boss character |
| POV shot | Immersion | "You walk into the café, she looks up at you" |
| Dutch angle | Unease / abnormality | Yandere moments, worldview crisis |

---

## Camera Movement Reference

| Movement | Effect | Campaign Use |
|---|---|---|
| Tracking shot | Follow character | Character walking through worldview scene |
| Dolly in | Approach, focus | Push from scene panorama to character face — reveal emotion |
| Dolly out / pull back | Reveal scope | Character close-up → pull back to reveal worldview scale |
| Pan | Horizontal survey | Sweep across multi-character lineup |
| Crane shot | Vertical + horizontal | Epic opening for World Teaser PV |
| Static | No motion, contemplative | Emotional content: character sitting still |
| Handheld | Urgency / reality | Chat replay style, "candid" feel |

---

## Lighting Keywords

Match worldview type and character mood:

| Worldview Type | Recommended Lighting | Mood |
|---|---|---|
| 修仙 / Eastern Fantasy | Golden hour, soft mist, ethereal glow, moonlight | Ethereal, timeless |
| 赛博朋克 / Cyberpunk | Neon reflections, rim lighting, rain-wet surfaces | Cold, urban |
| 校园 / School / Daily Life | Natural soft lighting, warm afternoon, overcast | Warm, everyday |
| 暗黑 / Lovecraftian | Low key, hard shadows, single light source, fog | Uneasy, oppressive |
| 奇幻冒险 / Fantasy Adventure | Dramatic rim light, campfire glow, magical particles | Epic, adventurous |

---

## Video Type Templates

### 角色 PV / Character Reveal — ~24s (2 cuts × 12s)

```
Cut 1 (12s): Wide establishing shot of [worldview scene], slow dolly-in toward [character],
             [worldview lighting], atmospheric, cinematic, [character] gradually visible

Cut 2 (12s): Close-up of [character], [character appearance keywords], [emotional action],
             [signature moment], dramatic lighting, pull back to reveal
             [character] in context of [worldview]
```

See `cut-strategies.md` for transition strategies between cuts (High Contrast, Irony, etc.).

### 世界观预告 / World Teaser — ~36s (default 3 cuts × 12s)

```
Cut 1 (12s): Aerial/crane establishing shot of [world panorama],
             [atmospheric conditions], epic scale, slow camera movement

Cut 2 (12s): Medium shots of [landmark locations], introduce key characters —
             [character A appearance] in [scene], building momentum, dramatic lighting

Cut 3 (12s): Key conflict/tension moment — [character interaction or world event],
             slow motion climax, pull back to epic wide shot
             [post-production: overlay world title + tagline]
```

Flexible options:
- 5 cuts × 10s = 50s (more locations/characters, fast pace)
- 4 cuts × 8s = 32s (compact, social media pre-roll)
- 2 cuts × 12s = 24s (minimal, two most impactful scenes)

### 情绪短片 / Mood Piece — ~12s (1 cut)

```
Cut 1 (12s): Static or ultra-slow dolly-in on [character] in [intimate setting],
             [character doing characteristic action], [soft/moody lighting],
             [art style keyword], lo-fi atmosphere
```

Subtitles (character dialogue) added in post-production.

### 对话实录 / Chat Replay — ~24s (2 cuts × 12s)

```
Cut 1 (12s): POV shot of phone/screen, [setting context lighting],
             text messages appearing with typing indicator,
             [character name]'s reply appears with emotional pause

Cut 2 (12s): Camera zooms into message / transition to
             [character] in [character scene], [surprising follow-up moment],
             soft lighting shift, [character reaction expression]
```

Cut 2 is optional — pure chat screen can work as 1 cut.

---

## Model Recommendations

| Content Need | Recommended Model | Reason |
|---|---|---|
| Realistic / Cinematic | Veo 3, Runway Gen-3 | Natural lighting, smooth camera motion |
| Anime / 2D | Kling, Wan 2.5 | Good anime aesthetic compatibility |
| Character consistency | Seedance, Wan 2.5 (img2vid) | Reference image control, minimal character drift |
| Quick test | Veo 3.1 Fast, Pika | Fast generation for A/B testing |
| Abstract / Conceptual | Grok, Sora | Creative interpretation of abstract concepts |

---

## Key Practices

- **12s generation cap.** All videos are multi-cut compositions. Never try to generate >12s in one prompt.
- **One prompt = one cut.** One continuous shot per prompt. Multi-cut videos are stitched in post.
- **Character consistency:** Generate reference image from `concept-arts.md` → use img2vid models.
- **English prompts only.** Chinese-language worlds localize via subtitles + BGM.
- **All text in post-production.** AI video renders text badly — never include subtitles, titles, or captions in prompts.
- **2-3 variants per cut.** AI video is stochastic — generate multiple, pick the best.
- **Post-production stitching:** CapCut / Premiere / DaVinci for cuts + transitions + subtitles + BGM.

---

## Common Errors

| Error | Problem | Fix |
|---|---|---|
| Multiple shots in one prompt | Model confused | One prompt = one shot |
| Subject without action | Static-feeling output | Must include action or camera movement |
| Conflicting styles | "minimalist cyberpunk baroque" | Pick one clear aesthetic direction |
| No lighting specified | Mood ambiguous | Lighting defines mood — always specify |
| Chinese prompt | Model comprehension drops | Use English; localize via subtitles |
| Text/subtitles in prompt | AI renders garbled text | All text added in post-production |

---

## Inheriting World Visual Language

Campaign video prompts should inherit visual language from upstream assets:

**From `concept-art/overview.md`:**
- Color palette keywords (base color + accent)
- Default lighting system
- Atmospheric particles / weather
- Style anchor phrase

**From `concept-art/locations.md`:**
- Composable background-layer keywords for each location
- Composable lighting-layer keywords
- Composable atmosphere-layer keywords

**From character's `concept-arts.md`:**
- Character base appearance prompt (hair, eyes, outfit, features)
- Key expression/pose variants

This ensures visual consistency between image assets and video assets within the same campaign.
