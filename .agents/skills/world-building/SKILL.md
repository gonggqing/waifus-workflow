---
name: world-building
description: Create and continuously expand worldview files for the waifu platform. Use this skill when creating a new worldview from scratch, updating an existing worldview with a new character, expanding world lore, or when create-waifu needs to read or write worldview context. Trigger on: "世界观", "world building", "扩展世界观", "update worldview", "add to world", or when called by create-waifu as part of the character creation pipeline.
---

# World Building

A skill for creating and maintaining `./worldview/[slug]/` — living directories that grow richer with each new character and story.

---

## File Structure

Each worldview lives in its own folder with 4 files. The split keeps fast-changing data (characters, events) separate from the stable core (world rules, culture).

```
worldview/[slug]/
├── world.md        ← 世界概览 · 世界规则 · 文化细节
├── factions.md     ← 势力与派系 · 重要地点
├── timeline.md     ← 历史事件（追加日志）
└── characters.md   ← 角色档案
```

**Slug convention:** world name in pinyin or English, hyphenated.
- 赛博朋克·璃光市 → `cyberpunk-liguang`
- 奇幻七王国大陆 → `fantasy-seven-kingdoms`
- 现代都市 → `modern-city`

---

## File Templates

### world.md

```markdown
# [世界名称]

> [一句话概括这个世界的气质和基调]

---

## 世界概览

[总体描述：地理范围、时代背景、世界基调。200–400 字，不超过 600。]

---

## 世界规则

[底层逻辑：魔法体系 / 科技水平 / 超自然法则 / 社会规则。
列点或短段，每条简明扼要。]

---

## 文化细节

[质感细节：习俗、禁忌、俚语、日常生活。碎片积累，不需系统。]
```

### factions.md

```markdown
# [世界名称] · 势力与地点

---

## 势力与派系

### [派系名称]
[规模、控制范围、核心理念、与其他势力关系。100 字以内。]

---

## 重要地点

### [地点名称]
[位置、氛围、在世界中的功能或象征意义。]
```

### timeline.md

```markdown
# [世界名称] · 历史时间线

---

- **[年份 / 时期]** — [事件简述，一句话]

<!-- 按时间正序或倒序追加，保持统一方向 -->
```

### characters.md

```markdown
# [世界名称] · 角色档案

---

## [角色名]
- **文件:** `worldview/[worldview-slug]/[slug]/basics.md`
- **身份:** [职业 / 角色定位]
- **简述:** [一句话描述]
- **势力归属:** [所属派系或独立]
- **与世界的联系:** [该角色如何与世界的历史、势力、规则交织]

---
```

---

## Two Modes of Operation

### Mode 1 — 创建新世界观

Triggered when no worldview folder exists for this world.

**Input:** A character's `basics.md` (specifically the 世界观 section)

**Process:**
1. Create `./worldview/[slug]/`
2. Build all 4 files from the character's 世界观 section:
   - `world.md` — expand the world description into 概览 + 规则 + 文化细节
   - `factions.md` — extract any organizations/locations mentioned
   - `timeline.md` — extract any historical events from the backstory
   - `characters.md` — add this first character as the initial entry, with file path `worldview/[slug]/[character-slug]/basics.md`
3. The character folder itself (`worldview/[slug]/[character-slug]/`) is created by waifu-flows — world-building only creates the 4 world files alongside it.
4. Enrich where the source gives clear signals. For anything not established, leave a `<!-- 待扩展 -->` placeholder rather than inventing.

### Mode 2 — 扩展现有世界观

Triggered when the worldview folder already exists and a new character has just been created.

**Input:** `./worldview/[slug]/` (all 4 files) + new character's `basics.md`

**Process — update only the files that changed:**

- **characters.md** — always: append the new character's entry
- **factions.md** — if the character belongs to a new faction or references a new location: append only the new entries; never rewrite existing ones
- **timeline.md** — if the character's backstory references a new historical event: append it in chronological order
- **world.md** — only if the new character introduces a fundamentally new world rule or cultural detail not yet captured; otherwise leave untouched

**Never rewrite existing content.** Only append or add new sections. If a new character's backstory contradicts existing lore, flag with `<!-- ⚠️ 待协调：[issue] -->` and let the user resolve it.

---

## Context Loading (for create-waifu)

When create-waifu needs worldview context before character generation, load files in this order:

1. **Always read:** `world.md` + `characters.md`
2. **Read if character has faction affiliation:** `factions.md`
3. **Read if character has historical backstory:** `timeline.md`

This keeps context load minimal while covering the most relevant information.

---

## Expansion Principles

**Stay grounded.** Only write what the characters and stories have established.

**Grow incrementally.** A worldview that starts at 300 words and grows to 2000 over ten characters is better than one front-loaded with invented lore that later conflicts.

**Prefer specific over general.** "企业财团控制上层" is less useful than "七大企业财团中，赤核集团控制璃光市70%的义体供应链". Specifics come from backstories — pull them out.

**Flag contradictions.** When two characters' backstories don't align, mark the tension and let the user resolve it.

**Write as a native Chinese speaker.** World descriptions, location prose, and faction summaries should read with natural rhythm and texture — not like database entries or translated copy. Each world has its own atmosphere; let the prose reflect it:
- A cyberpunk world: clipped, neon-lit, cynical undertone
- A xianxia world: elevated register, unhurried, weight of centuries
- A modern city world: casual, observational, detail-specific
- A dark fantasy world: dense, myth-adjacent, portentous

If two worldviews read identically in style, at least one is wrong. Voice is part of world-building.

---

## After Writing

Report:
- Files created or updated (list only the ones that changed)
- New entries added to each file
- Any `<!-- ⚠️ 待协调 -->` flags needing attention
- Total character count in characters.md
