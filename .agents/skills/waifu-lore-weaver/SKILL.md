---
name: waifu-lore-weaver
description: "Generate deep lore files (events, secrets, relationships, diaries) for an existing waifu character — the memory layer that makes her feel like a real person. Use this skill whenever the user wants to add depth, backstory details, personal history, secrets, relationship dynamics, or diary entries to an existing character. Trigger on: \"lore\", \"深度\", \"记忆层\", \"日记\", \"秘密\", \"事件\", \"关系网\", \"lore-weaver\", \"add lore\", \"character depth\", \"backstory depth\", \"diary\", \"secrets\", \"events\", \"relationships\", \"inner world\", \"make her more real\", \"让她更真实\", \"让她更有血肉\", \"她太扁平了\", \"she feels flat\", \"she needs more depth\", \"personal history\", or when the user wants to expand a character beyond her basics.md definition. Also use after create-waifu when the user wants to go deeper. Do NOT use for character creation (use create-waifu), character editing (use edit-waifu), or worldview expansion (use world-building)."
---

# waifu-lore-weaver — 深度记忆与日记编织者

Generate the "memory layer" for an existing character — events that shaped her, secrets she keeps, relationships as she truly sees them, and diaries in her own voice. These files have three layers of value: creators read them to verify the character feels alive; users discover them during chat (revealed diaries, uncovered secrets); the chat engine retrieves from them so every reply can carry her unique memory traces.

## Core Principles

**从 basics.md 生长，但不止于 basics.md。** The roots of every diary, secret, and event are in the character's existing backstory and worldview — no inventing from nothing. But lore-weaver doesn't just "retell basics.md from a different angle" — it grows things basics.md never mentioned: an unnamed childhood friend, a hidden habit, a small event that changed her. These new details turn a setting document into someone who feels real.

**关联角色作为镜子。** Characters don't exist in isolation. Her diary mentions other characters in the same worldview; her secrets may involve them; her key events may intersect their timelines. Load all related characters' basics.md as context so relationships emerge naturally in the lore layer — not just the one-liner in basics.md's 关联角色 field.

**声音一致性。** diaries.md is written *by* the character — her word choices, her emotional rhythm, her punctuation style. If two characters' diaries read like the same person wrote them, at least one has failed.

**重要发现回写（Write-Back）。** Lore-weaver's main outputs are the 4 lore files, but the writing process may uncover information that belongs in upstream files. See the Write-Back rules in [references/write-back-rules.md](references/write-back-rules.md).

---

## Prerequisites

| Upstream | Files | Required? |
|---|---|---|
| create-waifu | `basics.md`, `concept-arts.md`, scene files | **Yes** — character must exist |
| world-building | `world.md`, `factions.md`, `timeline.md` | **Yes** — world context |
| world-building | `characters.md` | Recommended — character registry |
| Other characters | Their `basics.md` | Recommended — relationship context |
| Other characters | Their `relationships.md` | Optional — cross-reference for asymmetric relationships |

---

## Scope

Each run focuses on **1 worldview + 1 character**. Only when two characters have deep designed relationships (rivals, bonds, master-student) may you process 2 characters simultaneously with cross-references. Never batch-generate an entire worldview.

---

## Workflow

**Generation order matters:** events (factual foundation) → secrets (hidden layer) → relationships (social layer) → diaries (emotional layer). Each step uses the previous as input, preventing contradictions.

### Step 1 — 加载上下文 / Load Context

Read from `worldview/[slug]/`:

1. **Target character:** `[char-slug]/basics.md` (full), `[char-slug]/concept-arts.md` (visual reference), all `[char-slug]/[scene].md` files
2. **World context:** `world.md` (世界概览 + 世界规则), `factions.md` (势力 + 重要地点), `timeline.md` (历史事件)
3. **All characters in worldview:** Read every other character's `basics.md` — at minimum the 关联角色 section. If any character already has `relationships.md`, read that too for cross-reference

If the target character already has lore files (Append mode), read them all first before generating anything.

### Step 2 — 生成 events.md / Generate Events

The factual foundation layer — everything else must be consistent with what happened here.

Read the file format and writing requirements from [references/file-formats.md § events.md](references/file-formats.md).

**Key points:**
- 5-8 key events, chronologically ordered
- Must include: one childhood/early memory, one turning point, one event tied to a core secret, one recent event
- Can create events **not in basics.md** — as long as they're consistent with her personality and world logic. A character whose background says "lost her parents young" — events.md can fill in "the details of that day"
- "她记住的是" (what she remembers) is the most important field — it reveals how she perceives the world
- World-level events → write back to timeline.md

### Step 3 — 生成 secrets.md / Generate Secrets

The hidden information layer — layered by intimacy depth, simulating "the closer you get, the more you learn."

Read the file format and writing requirements from [references/file-formats.md § secrets.md](references/file-formats.md).

**Key points:**
- Three tiers: 表层 (surface — observable quirks), 中层 (middle — requires trust), 深层 (deep — extremely private)
- 2-3 secrets per tier, 6-9 total on first run
- Cross-tier "contradictions" are **by design** — surface says "she doesn't care about the past", deep reveals "she has nightmares every night." This is 表里不一, not a bug. Same-tier factual conflicts ARE bugs — resolve by latest-wins + fix the old entry
- Each secret has a "浮现情境" field — describe the *circumstances* where this secret naturally surfaces (场景、话题、气氛), not "when the user asks X." No fourth-wall breaking; no mentioning 用户
- Deep secrets that reveal entirely new character dimensions → write back a vague foreshadowing line to basics.md (never spoil the secret itself)

### Step 4 — 生成 relationships.md / Generate Relationships

The social layer — her private relationship map, not the public profile in basics.md.

Read the file format and writing requirements from [references/file-formats.md § relationships.md](references/file-formats.md).

**Key points:**
- Cover all characters listed in basics.md's 关联角色, plus any new relationships that make sense given the worldview
- **Single-directional perspective** — only "how she sees others." The other character's view lives in their own relationships.md. Asymmetry is realistic, not a bug
- "她心里的版本" (her honest version) is the core field — not objective relationship description, but her subjective feelings with her biases and blind spots
- "认知边界" is author/engine-level metadata — it prevents the character from saying things she shouldn't know. This is the one omniscient-perspective field; the rest are all her view
- "她记住的那一刻" — the moment that defines this relationship for her (parallels events.md's "她记住的是"). The other person may not even remember it
- Never reference "用户" or break the fourth wall in the character's subjective fields — write as if describing a real person in her world
- New important relationships not in basics.md → write back a one-line summary to basics.md's 关联角色

### Step 5 — 生成 diaries.md / Generate Diaries

The emotional layer — first-person fragments in her own voice, built on everything generated so far.

Read the file format and writing requirements from [references/file-formats.md § diaries.md](references/file-formats.md).

**Key points:**
- 5-8 entries, 100-300 characters each — fragments, not essays
- Written in the character's **own voice**: her punctuation habits, her emotional register, her way of thinking
- Not all entries are heavy — some are just "that annoying adventurer came again today, his handwriting on the quest form was a mess"
- Time span matters — include old diaries (her voice before growth may differ from now)
- Diaries can reference events and hint at secrets, but never explicitly state them

### Step 6 — 回写检查 / Write-Back Pass

Scan all generated content against the write-back rules in [references/write-back-rules.md](references/write-back-rules.md).

For each potential write-back:
1. Check if the information is genuinely important (not just personal emotion or trivia)
2. Check if the target file already contains this information or a hint of it
3. If write-back is warranted, apply it — append/micro-edit, never large-scale rewrite
4. When in doubt, don't write back. Better to miss one than to pollute upstream files

### Step 7 — 输出报告 / Report

Present to the user:

```
## Lore Generation Report

**角色：** [character name] · [worldview name]
**模式：** Full Run / Append

### 生成文件
- events.md — [N] 个事件
- secrets.md — [N] 个秘密（表层 X / 中层 Y / 深层 Z）
- relationships.md — [N] 段关系
- diaries.md — [N] 篇日记

### 回写操作
- [target file]: [what was written back] (or "无回写")

### 建议后续
- [e.g., "Run lore-weaver for [related character] to build the other side of the relationship"]
- [e.g., "Consider expanding world timeline with the [event name] discovered in events.md"]
```

---

## Append Mode

When the target character already has lore files:

1. Read all existing lore files first — understand existing themes, timeline, covered topics
2. Do NOT duplicate existing themes, events, or secrets
3. Append new entries only — triggered by: new worldview events, new related characters, user requesting a specific direction to explore
4. Check new content against existing entries for factual conflicts — if found, resolve by updating the old entry (same-tier conflict rule)
5. Follow the same Step 2-7 sequence, but each step reads existing files as additional context

---

## Output Structure

```
worldview/[worldview-slug]/[character-slug]/
├── basics.md          ← (existing, may receive write-backs)
├── concept-arts.md    ← (existing, read-only)
├── [scene].md         ← (existing, read-only)
├── events.md          ← 个人编年史：塑造她的关键时刻
├── secrets.md         ← 秘密图谱：分层的隐藏信息
├── relationships.md   ← 关系深网：她真正怎么看每个人
└── diaries.md         ← 私密日记：角色的内心独白
```

---

## References

| File | When to read | Content |
|---|---|---|
| [references/file-formats.md](references/file-formats.md) | Steps 2-5 | Exact markdown structure and writing requirements for each lore file |
| [references/write-back-rules.md](references/write-back-rules.md) | Step 6 | Write-back trigger conditions, targets, principles, and summary table |

---

## Integration with Other Skills

**← create-waifu:** Optional final step — after character creation, ask if the user wants to run lore-weaver for depth
**← edit-waifu:** After editing character background/personality, existing lore files may need sync — run in Append mode
**↔ world-building:** Reads world context as input; writes back world-level discoveries to timeline.md / factions.md
**→ create-campaign:** Campaign can extract "hooks" from secrets.md — surface secrets are great social media material
**↔ related characters:** When character A's lore involves character B, reads B's basics.md for consistency; if A's events have major impact on B, can append to B's events.md. When later running lore-weaver for B, reads A's relationships.md to see "A's view of B" as reference
