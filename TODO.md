# TODO — Skill Roadmap

Current skill matrix and what's next — organized by product goal.

---

## Current Skills — Status

| Skill | Status | Purpose |
|---|---|---|
| create-waifu | ✅ Shipped | Wizard for new characters (Plan Mode / Agent Mode) |
| edit-waifu | ✅ Shipped | Modify existing characters, scenes, worldviews |
| waifu-stories | ✅ Shipped | Character authoring engine (JSON + markdown) |
| waifu-generator | ✅ Shipped | Image prompts (MJ/SDXL/PixAI/LiblibAI) |
| world-building | ✅ Shipped | Create/expand worldview files |
| world-concept-art | ✅ Shipped | Environment image prompts for worldviews |
| waifu-lore-weaver | ✅ Shipped | Deep lore: events, secrets, relationships, diaries |
| create-campaign | ✅ Shipped | Cross-platform marketing campaigns + video prompts |
| skill-creator | ✅ Shipped | Meta-skill for building and evaluating skills |

Design docs for shipped skills preserved in `TODO_v1.md`.

---

## Architecture — Blackboard Pattern

All skills share the **worldview directory as a blackboard** — the single source of truth. Skills read from it and write back to it. No skill calls another skill directly; the user decides when to run which skill.

```
┌─────────────────────────────────────────────────────────┐
│          Worldview Directory (shared state)             │
│                                                         │
│  world.md · factions.md · timeline.md · characters.md   │
│  [char-slug]/basics.md · concept-arts.md · scenes       │
│  [char-slug]/events.md · secrets.md · relationships.md  │
│  stories/[story-slug]/...                               │
└──────────┬────────────────────────────┬─────────────────┘
           │                            ▲
      READ │                            │ WRITE-BACK
           ▼                            │
┌────────────────────────────────────────────────────────────┐
│  World Skills              Story Skills                    │
│  ┌────────────────┐          ┌──────────────────────────┐  │
│  │ world-building │          │ create-story             │  │
│  │ create-waifu   │◄────────►│ plot-weaver              │  │
│  │ lore-weaver    │ feedback │ story-writer             │  │
│  │ waifu-generator│  loop    │                          │  │
│  └────────────────┘          └──────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
```

### Why this works

1. **Files as shared state, not databases.** LLM agents work best when state is human-readable markdown. No schema migrations, no API layers. This is what Claude Code / Cursor / agent frameworks are optimized for.

2. **Decoupled skills with file-based contracts.** Each skill declares what it reads and writes. You can run world-building without ever touching stories. You can write stories in a world you didn't build with these tools.

3. **Human-in-the-loop at write-back points.** When story-writer discovers a new character is needed, it doesn't auto-create one — it flags: "Chapter 7 introduces [X]. Run create-waifu to add her, then continue." The user controls when the world grows.

4. **Append-only history + mutable state.** `timeline.md` is append-only (events are facts). `basics.md` and `world.md` are mutable (they evolve as the story deepens understanding of the world).

### The feedback loop in practice

```
1. world-building   → initial world + 3-4 main characters
2. create-story     → design a 20-chapter story arc
3. plot-weaver      → detail chapters 1-5
   └─ "Chapter 4 introduces a rival faction leader —
       run create-waifu to add her before writing"
4. USER: create-waifu for the new character
5. story-writer     → write chapters 1-5
   └─ write-back: append events to timeline.md
6. plot-weaver      → detail chapters 6-10 (reads updated world)
   └─ new character naturally appears in beats
7. ...cycle continues — world and story co-evolve
```

### Write-back rules (extending lore-weaver's pattern)

Story skills follow the same write-back principles as lore-weaver:

| Trigger | Target | What |
|---------|--------|------|
| New character introduced in story | Flag for user | "Run create-waifu for [X]" |
| Story reveals new location/faction | factions.md | Append entry |
| Story event has world-level impact | timeline.md | Append event |
| Character growth during story | basics.md | Subtle update (foreshadow, not spoil) |
| Contradiction with existing world | Flag for user | "Conflict detected: [details]" |

**Supplement, never rewrite. Foreshadow, never spoil. When in doubt, don't write back.**

---

## Part 1 — Waifu Companion App

### ~~chat-ready~~ — Not needed

The chat engine's existing schema (`background_story` 1536 chars + `seed_chat` 256 chars + scenes with `content` 600 chars) **is** the system prompt. waifu-stories already generates all these fields.

Modern LLMs infer voice, boundaries, relationship dynamics, and topic routing from a well-written `background_story` + 2-3 seed chat turns. Adding a separate `chat-config/` layer (system-prompt.md, voice-profile.md, topics.md) would be:

- **Redundant** — background_story IS the system instruction, embedded in the platform schema
- **Maintenance overhead** — every character edit requires regenerating parallel files
- **Solving yesterday's problem** — explicit prompt engineering for behavior that current models handle implicitly

**The real quality lever is making `background_story` and `seed_chat` better** — that's waifu-stories + lore-weaver's job. If a character feels flat in chat, edit her basics.md, don't add a voice-profile.md.

### dialogue-expander — 对话样本扩展

**Problem:** Each character has only 2-3 seed chat turns (256 chars). For fine-tuning, few-shot examples, or voice calibration on weaker models, this isn't enough.

**What this skill does:** Generate high-quality dialogue samples from basics.md + scenes + lore files.

**Output:**
```
worldview/[worldview-slug]/[character-slug]/
└── dialogues/
    ├── casual.md          ← 日常闲聊 (8-12 组)
    ├── emotional.md       ← 情感深入 (6-8 组)
    ├── scene-specific.md  ← 每个场景展开 (4-6 组/场景)
    └── boundary.md        ← 边界测试：拒绝/回避/冷下来 (4-6 组)
```

Each set: 6-10 turns, covering different intimacy stages, emotional trajectories, character-initiated actions, and secret-adjacent conversations. Annotated with metadata (intimacy phase, mood, related secret/event).

Quality > quantity. 12 good dialogues beat 50 template dialogues.

### multi-greeting — 多开场白生成

**Problem:** One greeting per scene. Repeat visits feel stale.

**What this skill does:** 5-8 greeting variants per scene, varying by mood, relationship phase, and time/context.

May merge into edit-waifu as a sub-mode rather than a standalone skill.

---

## Part 2 — Story Pipeline

Three new skills that form the story-telling backbone. All three read from the worldview blackboard and write back when appropriate.

### create-story — 故事架构师 / Story Architect

**Concept:** Design the **skeleton** of a complete story — not the story itself, but "what is this story about, how is it told, why is it worth telling."

**Input:** World files + character basics.md (+ lore if available) + user's story intent (one sentence or detailed outline)

**Output:**

```
worldview/[worldview-slug]/stories/[story-slug]/
├── premise.md         ← logline + core conflict + theme + POV + timeline
├── arcs.md            ← per-character transformation trajectory
├── structure.md       ← chapter outline + pacing curve + per-chapter events
└── threads.md         ← main thread + sub-threads + foreshadowing plan + mystery seeds
```

#### premise.md

```markdown
# [Story Title]

## Logline
[One sentence: "When [character] encounters [event], she must [choice], or [cost]"]

## Core Conflict
[The fundamental tension — not "defeat the villain" but "someone who believes in rules discovers the rules are wrong"]

## Theme
[What this story is really about — trust, identity, sacrifice, freedom. One word or one question]

## Story Type / POV / Timeline / Intended Reader Experience
```

#### arcs.md

Per main character:
```markdown
## [Name] · [Arc type: growth/fall/revelation/healing/...]

**Start:** [state, beliefs, fears at story opening]
**Pressure:** [what forces her to change]
**Turn:** [where her beliefs crack]
**Cost:** [what she loses for changing]
**End:** [who she becomes — not necessarily "better", maybe just "different"]
```

Arcs should intersect — A's turning point may be B's pressure source.

#### structure.md

Per chapter:
```markdown
## Chapter N · [Title]
**Core Event:** [what must happen]
**POV:** [whose perspective]
**Emotional Arc:** [calm→unease / tension→release / ...]
**Character Arc Progress:** [which arc advances, to where]
**End Hook:** [why the reader turns the page]
```

Chapter count by story scale: short 3-5, medium 8-15, long 20+. Not every chapter needs a climax — pacing is breathing.

#### threads.md

Main thread (driving question + resolution condition), sub-threads (seeds, development, convergence), foreshadowing table (detail / planted in ch X / revealed in ch Y / reader reaction), mystery seeds.

**Design principles:**
- Architecture only — no narrative text
- Extract conflict material from existing character files, don't invent from nothing
- Lore-weaver's secrets.md / events.md are "gold mines" for plot design
- Architecture is cheap to revise; finished prose is expensive to revise
- Include brief architectural notes: why this conflict, why this order, why this POV

**Write-back:** Flag new characters / locations / factions needed. Don't create them — let the user decide when.

---

### plot-weaver — 剧情编织者 / Plot Weaver

**Concept:** Expand create-story's chapter outline into **detailed beat sequences** — what happens in each scene, what's said, how emotions flow, how the turn lands.

The bridge from skeleton to screenplay. create-story says "chapter 3 has a confrontation"; plot-weaver says "the confrontation happens at the Ascension Terrace ruins, Cang Yuan asks three questions, Yun Sheng's hands start shaking at the second, after the third the light in the scene changes entirely."

**Input:** create-story's 4 files + character files + world files

**Output:**

```
worldview/[worldview-slug]/stories/[story-slug]/
└── chapters/
    ├── ch01-[slug].md
    ├── ch02-[slug].md
    └── ...
```

#### Per-chapter structure

```markdown
# Chapter N · [Title]

## Summary
[3-5 sentences]

## Beat Sequence

### Beat 1 · [Name] · [Location]
**Action:** [specific events]
**Key Line:** [the 1-2 sentences that land hardest — "钉子句"]
**Emotion Flow:** [entering → shift → exiting state]
**Inner Voice:** [what she's thinking, if her POV]
**Foreshadowing:** [what's planted, what echoes]

### Beat 2 · ...

## End State
**Character State Delta:** [who changed, how]
**Information Delta:** [what reader/characters now know]
**Suspense:** [page-turn fuel]
```

**Design principles:**
- Beat = minimal plot unit — one scene shift, one emotional turn, one new information
- 4-8 beats per chapter, max 10 (more = split the chapter)
- Key lines are "nail sentences" only — full dialogue is story-writer's job
- Emotion flow matters more than events
- Each chapter's beats form a mini-arc

**Write-back:** This is where new world elements surface most often. When designing beats, plot-weaver identifies:
- "This chapter needs a new character [X] — role: [description]"
- "This location doesn't exist yet — suggest adding to factions.md"
- "This event should be reflected in timeline.md"

These become actionable items for the user, not automatic changes.

---

### story-writer — 故事书写者 / Story Writer

**Concept:** Turn plot-weaver's beat sequences into **readable story text** — narrative prose, dialogue, description.

The pipeline's final output. Architecture designs the what; plotting designs the how; this skill does the writing.

**Input:** Plot chapter files + character files (basics.md + lore) + world files

**Output:**

```
worldview/[worldview-slug]/stories/[story-slug]/
└── text/
    ├── ch01-[slug].md
    ├── ch02-[slug].md
    └── ...
```

```markdown
# Chapter N · [Title]

[Narrative text]

---
<!-- Words: [N] -->
<!-- POV: [character] -->
<!-- Story time: [in-world timestamp] -->
```

**Writing principles:**
1. **Voice-first:** POV narrative carries the character's texture — Cang Yuan's narration is restrained and precise; Yun Sheng's is visual and jumpy
2. **Dialogue = personality:** If you can swap names between two characters' dialogue with no friction, at least one failed
3. **Show, don't tell:** "She was nervous" is telling. "She turned the cup three times before speaking" is showing
4. **Rhythm = sentence length variation:** Short for tension, long for calm, one word alone in a paragraph for shock
5. **2000-5000 words per chapter.** Never exceed 8000 (split if needed)

**Execution modes:**
- **Chapter-by-chapter:** Write one, user reviews, confirm before next
- **Revision:** User can target specific beats ("rewrite beats 3-5") or voice ("this dialogue doesn't sound like her")
- **Voice calibration:** After chapter 1, user confirms narrative voice before subsequent chapters follow suit

**Write-back:** After writing, story-writer appends relevant events to `timeline.md` and flags any character state changes that should be reflected in `basics.md`.

**Continuity checking:** When writing chapter N, reads all prior chapters + current world state. Flags contradictions rather than silently fixing them.

---

## Pipeline 总览

```
┌────────────────────────────────────────────────────────────────┐
│                 Worldview Directory (blackboard)               │
│  world.md · factions.md · timeline.md · characters.md          │
│  [character]/basics.md · scenes · lore files                   │
│  stories/[story]/premise · arcs · structure · chapters · text  │
└──────┬──────────────────────────────────────┬──────────────────┘
       │                                      ▲
       │            ┌─────────────────────────┤
       ▼            │                         │
  ┌─────────┐  ┌─────────┐  ┌────────────┐    │
  │ create- │→ │ plot-   │→ │ story-     │────┘
  │ story   │  │ weaver  │  │ writer     │ write-back
  └─────────┘  └────┬────┘  └────────────┘
                    │
                    ▼
              "Chapter 4 needs
               a new character"
                    │
                    ▼
              USER runs create-waifu
                    │
                    ▼
              World directory updated
                    │
                    ▼
              plot-weaver continues
              with enriched world
```

**Two product lines share one worldview.** The companion app consumes waifu-stories output (JSON fields → chat engine). The story service consumes the same world + characters through the story pipeline. Both write back to the shared blackboard.

---

## 实现优先级

### Tier 1 — Story Pipeline (core product expansion)
| Skill | Priority | Reason |
|---|---|---|
| **create-story** | P0 | Pipeline entry — no skeleton, no story |
| **plot-weaver** | P1 | Skeleton → screenplay bridge, also where write-back needs surface |
| **story-writer** | P1 | Final output — but architecture + plot must be solid first |

### Tier 2 — Companion App Enhancement
| Skill | Priority | Reason |
|---|---|---|
| **dialogue-expander** | P1 | More dialogue samples for voice calibration + fine-tuning |
| **multi-greeting** | P2 | UX polish — may merge into edit-waifu |

### Tier 3 — Scale Infrastructure
| Skill | Priority | Reason |
|---|---|---|
| **world-inspector** | P1 | After 5+ characters + ongoing stories, contradictions accumulate fast |
| **export-packager** | P2 | Package output for delivery (novel PDF, wiki, platform JSON) |
