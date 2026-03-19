# Waifus Workflow

A collection of Claude Code skills for creating, visualizing, and organizing waifu characters for a chat platform. The skills work standalone or together as a guided pipeline.

---

## Requirements

- [Claude Code](https://code.claude.com) installed and authenticated
- Node.js (for `npx` — used by the Notion MCP server if configured)

---

## Installation

Clone the repo and symlink the skills you want into your Claude Code skills directory.

```bash
git clone https://github.com/gonggqing/waifus-workflow.git
cd waifus-workflow

# Link all skills globally (available in any Claude Code session)
ln -s $(pwd)/.agents/skills/create-waifu ~/.claude/skills/create-waifu
ln -s $(pwd)/.agents/skills/waifu-stories ~/.claude/skills/waifu-stories
ln -s $(pwd)/.agents/skills/waifu-generator ~/.claude/skills/waifu-generator
ln -s $(pwd)/.agents/skills/world-building ~/.claude/skills/world-building
```

Or link only to this project (open Claude Code from this directory):

```bash
# Already set up in .claude/skills/ — just open Claude Code here
claude .
```

---

## Skills Overview

### `create-waifu` — Full Creation Wizard (Start Here)

The recommended entry point. Walks you through 4 steps to define a character, then automatically runs the full pipeline.

**Trigger phrases:**
> "create waifu" · "新建角色" · "make a waifu" · "我要创建一个角色"

**What it does:**
1. Checks for existing worldviews in `./worldview/`
2. Guides you through world, appearance, personality, and character core
3. Proposes a name and asks for confirmation
4. Runs all pipeline stages automatically:
   - `world-building` — loads worldview context
   - `waifu-stories` — generates the character markdown
   - `waifu-generator` — generates image prompts
   - File assembly — writes all output files to `worldview/[world-slug]/[character-slug]/`
   - `world-building` — updates or creates worldview files

**Output structure:**
```
worldview/
└── cyberpunk-liguang/
    ├── world.md
    ├── factions.md
    ├── timeline.md
    ├── characters.md
    └── wei-la/
        ├── basics.md          ← character profile + image prompts
        ├── nan-qu-ye-shi.md   ← scene file
        └── sheng-dian-hui-lang.md
```

---

### `waifu-stories` — Character Writer

Crafts a structured character profile in Markdown and JSON. Use this standalone if you already have a world in mind and just want to write a character without the full wizard.

**Trigger phrases:**
> "create waifu" · "waifu profile" · "角色设定" · "人物背景" · paste a character idea in Chinese

**Input:** A rough description, a few keywords, or a character concept in any language.

**Output:** Full character markdown with sections for background, personality, appearance, opening dialogue, scenes, and seed chat. JSON block also included when used standalone.

**Key conventions:**
- Content is Chinese-primary — written as a native speaker, not translated
- Appearance is marked **（不主动提及）** — the character doesn't describe herself unprompted
- Each character must have a distinct prose voice, not a generic archetype

---

### `waifu-generator` — Image Prompt Generator

Turns a character definition into ready-to-use image prompts for Midjourney, SDXL, PixAI, or LiblibAI.

**Trigger phrases:**
> "generate image" · "MJ prompt" · "SDXL prompt" · "生成提示词" · "出图"

**Input:** A `basics.md` file, a waifu JSON, or a file path to either.

**Output per character:**
- **Base prompts** (1–3) — shared visual anchor used as prefix for all other prompts
- **Portrait set** (4+) — front, three-quarter, casual, key emotion
- **Scene variants** (2–3 per scene) — different framings for each scene

**Platforms supported:** Midjourney · SDXL · PixAI · LiblibAI

---

### `world-building` — Worldview Manager

Creates and maintains the `worldview/[slug]/` directories that give characters a shared context.

**Trigger phrases:**
> "世界观" · "world building" · "扩展世界观" · "update worldview"

**Two modes:**
- **Mode 1 — Create new worldview:** Called after the first character in a new world. Generates `world.md`, `factions.md`, `timeline.md`, `characters.md` from the character's backstory.
- **Mode 2 — Expand existing worldview:** Appends a new character entry to `characters.md`, adds any new factions/locations/events. Never rewrites existing content.

**Key principle:** Only write what characters and stories have established. Contradictions are flagged with `<!-- ⚠️ 待协调 -->` rather than silently overwritten.

---

### `skill-creator` — Build New Skills

A meta-skill for creating, testing, and improving skills in this format. Runs an eval loop with qualitative review and quantitative benchmarking.

**Trigger phrases:**
> "create a skill" · "improve this skill" · "run evals"

See `.agents/skills/skill-creator/SKILL.md` for full documentation.

---

## Typical Workflows

### Create your first character

```
You: create waifu
```
Follow the 4-step wizard. At the end, files are written to `worldview/`.

### Add a character to an existing world

```
You: create waifu
```
The wizard detects your existing worldviews and lists them at Step 1. Pick one and the new character will be grounded in that world's lore.

### Generate images for an existing character

```
You: generate SDXL prompts for worldview/cyberpunk-liguang/wei-la/basics.md
```

### Expand worldview lore manually

```
You: 扩展世界观 cyberpunk-liguang，新增一个地下黑市地点
```

---

## Worldview Files

Character files are gitignored by default — only the `worldview/` folder is tracked. To version your characters, remove `worldview/*` from `.gitignore`.

---

## Slug Conventions

| Source | Convention | Example |
|--------|-----------|---------|
| Chinese name | Pinyin, hyphenated | 小铃 → `xiao-ling` |
| English name | Lowercase, hyphenated | Elara → `elara` |
| World name | Pinyin or English, hyphenated | 赛博朋克·璃光市 → `cyberpunk-liguang` |
| Scene name | Pinyin abbreviation | 三号窗口 → `san-hao-chuangkou` |
