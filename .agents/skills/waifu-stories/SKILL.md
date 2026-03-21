---
name: waifu-stories
description: Internal character authoring engine — generates the JSON and markdown definition for a single waifu character. Called by create-waifu during the pipeline; also usable standalone when you already have a clear character concept and want to go straight to a structured definition without the guided wizard. Trigger on: "waifu profile", "character definition", "角色设定", "人物背景", or when the user pastes a character idea and wants it turned into a structured JSON/markdown definition. If the user just says "create a waifu" without details, prefer create-waifu (the wizard) instead.
---

# Waifu Stories

A skill for crafting structured, JSON-formatted waifu character definitions — rich enough to feel like real people, not anime tropes.

## What a Waifu Definition Contains

A **waifu** is a character profile used on the platform. The JSON has two layers:

**Character core** — who she is across all contexts:
- `name` — display name (max 24 chars)
- `description` — one-line hook (max 48 chars)
- `tags` — up to 5 genre/personality labels, each max 12 chars
- `background_story` — her history, personality, quirks (max 1536, aim 500–1024)
- `initial_message` — how she greets a new user (aim 50–100 chars)
- `seed_chat` — few-shot dialogue that nails her voice (max 256 chars)
- `language` — `"zh"`, `"en"`, or `"multilingual"`

**Scenes** — situational contexts she inhabits (one or more):
- `name` — scene title (max 24 chars)
- `description` — one-line scene hook (max 48 chars)
- `initial_message` — her opening line in this scene
- `content` — the scene's backdrop/premise (max 600, aim 300)

## JSON Schema

Always output valid JSON in this exact shape:

```json
{
  "name": "string",
  "description": "string",
  "tags": ["tag1", "tag2"],
  "background_story": "string",
  "initial_message": "string",
  "seed_chat": "string",
  "language": "zh | en | multilingual",
  "scenes": [
    {
      "name": "string",
      "description": "string",
      "initial_message": "string",
      "content": "string"
    }
  ]
}
```

### Field limits — enforce strictly:

- `name` — max 24 chars
- `description` — max 48 chars
- `tags` — max 5 tags, each max 12 chars; aim for 3–4
- `background_story` — max 1536 chars, aim 500–1024
- `initial_message` — aim 50–100 chars
- `seed_chat` — max 256 chars, aim 2–3 turns
- `scene.name` — max 24 chars
- `scene.description` — max 48 chars
- `scene.content` — max 600 chars, aim ~300

Scene content is intentionally kept short — it sets the mood and situational premise, not a full narrative. If the user wants to expand a scene later, they will. Don't overfill.

If input is in Chinese, count characters — Chinese chars count as 1 each.

## Markdown Output Format

When producing the full waifu definition as a markdown file, follow the structure from `references/examples.md` exactly. Key conventions:

- Section headers use bilingual format: **4. 背景介绍 / Background** — this keeps files readable across languages (see `create-waifu/references/file-structure.md` for the full template)
- Sub-section labels stay Chinese only (e.g., **角色定义**, **外貌（不主动提及）**, **个性特质**, **互动风格**) — these are internal structure, not navigation headings
- Appearance sub-section should note **（不主动提及）** — the character doesn't volunteer her own description
- Scene sections follow the four-field structure: 场景描述 · 开场白 · 场景内容
- **Section 2 (世界观 / Worldview):** When called from the create-waifu pipeline with worldview context, copy the 世界摘要 / World Abstract from `world.md` directly — it's already sized and tuned for character-card use. Don't re-summarize or paraphrase. When standalone without worldview files, write a brief world description (~300–500 chars) based on what the user provided

## How to Create a Waifu

### Step 1 — Understand the character

If the user hasn't given enough detail, ask a few focused questions (not all at once):
1. What's her core personality or archetype? What makes her feel different from a stock character?
2. What's her relationship dynamic with the user? (romantic, mentor, rival, childhood friend…)
3. What genre/setting? (modern city, fantasy, sci-fi, school…)
4. Language? (Chinese default for this platform; specify if bilingual)
5. How many scenes to start with, and what situations should they cover?

If the user gives you a rough idea or a few phrases — even in Chinese — work with what you have and draft first, ask later.

### Step 2 — Read the examples

Read `references/examples.md` before drafting. Use those examples to calibrate voice, level of detail, and emotional texture. Don't copy — use them as a quality bar.

### Step 3 — Draft the character

Write from the inside out:
- Start with `background_story` — ground her in specific details (a habit, a memory, a contradiction). Avoid vague traits like "kind and gentle". Prefer: "She always keeps a cracked phone screen because she never had the patience to get it fixed."
- `initial_message` — first-person, in-character, not a chatbot greeting. Should feel like a real opening line.
- `seed_chat` — 2–3 turns of dialogue that shows her voice, not describes it. See format below.
- `description` — write this last; it's a compressed hook, not a summary.
- `tags` — genre + personality + archetype mix. Chinese tags are fine (e.g., `治愈系`, `傲娇`, `学姐`).

**Seed chat format:**
```
User: 你今天怎么样？
Waifu: 还好啦，就是有点困...你呢，有想我吗？
```

### Step 4 — Draft scenes

Each scene shifts the waifu's context — a different setting, mood, or relationship phase. Good scenes have:
- A specific enough premise that the `initial_message` can feel fresh and different from the base one
- A `content` that gives backstory for the scene, not just a summary of the setting

### Step 5 — Output

**When called from the create-waifu pipeline:** output markdown only — no JSON block. Follow the markdown structure from `references/examples.md` directly. Skip the preview step; create-waifu handles the flow.

**When used standalone:** output the JSON block first, then a brief human-readable preview:
- Name · tags (comma separated) · language
- Description
- First 200 chars of `background_story`
- `initial_message`

This lets the user quickly feel if the character "lands" before diving into the JSON.

## Language and Voice

This platform is Chinese-primary. All narrative content — background story, scenes, dialogue — must read as written by a native Chinese speaker, not translated from English.

**Native speaker quality:**
- Use natural Chinese sentence rhythm: short punchy sentences for tension, longer flowing ones for mood. Don't default to subject-verb-object every time.
- 语气词（啦、呢、嘛、哎、罢了）and colloquial particles should appear where they fit the character's voice — not uniformly, not never.
- Avoid stiff constructions like 「她是一个……的人」. Prefer specific behavior over trait labels: 「她从不道歉，但会在第二天默默把你的杯子续满」.
- Idioms, slang, and register should match the character and world. A street hacker doesn't sound like a court lady.

**Each character must have a distinct prose voice.** If you could swap the 背景介绍 from one character into another with no friction, both are too generic. Vary:
- Sentence length and rhythm
- Vocabulary register (formal / colloquial / technical / poetic)
- Narrative temperature (detached and dry vs. close and warm)
- Punctuation cadence — dashes, ellipses, and comma placement are part of voice

**JSON field names stay in English** (they're technical keys). Set `language: "zh"` for Chinese content, `"multilingual"` for mixed. Respect the char limits — count carefully, Chinese chars count as 1 each.

## Iteration

If the user says the character feels flat, generic, or "not quite right":
- Ask what emotion or dynamic they're going for
- Offer 2–3 alternative versions of `initial_message` or `seed_chat` — these are the fastest levers for changing perceived personality
- Rewrite `background_story` with more specificity rather than just more length

## Reference Files

- `references/examples.md` — Golden waifu templates (user-curated). Read before drafting.
