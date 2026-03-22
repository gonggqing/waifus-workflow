# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A **skill-based waifu creation workflow** — a collection of Claude Code skills that together form a character creation pipeline for a waifu chat platform. There is no build system, test runner, or application code. The "code" is primarily Markdown skill definitions and Python eval scripts.

## Skill Architecture

Skills live in `.agents/skills/` and are symlinked into `.claude/skills/` (project-local) and `~/.claude/skills/` (global). The symlink is how Claude Code discovers and loads skills.

```
.agents/skills/
├── create-waifu/          ← orchestrator: guided wizard (Plan Mode) or zero-friction proposals (Agent Mode)
│   ├── SKILL.md
│   ├── references/
│   │   └── file-structure.md   ← folder/file templates for character output
│   └── evals/
├── edit-waifu/            ← edit existing characters, scenes, worldviews
│   └── evals/
├── waifu-stories/         ← Stage 1: generates character JSON/markdown
│   ├── references/
│   └── evals/
├── waifu-generator/       ← Stage 2: generates character image prompts → concept-arts.md
│   ├── references/
│   └── evals/
├── world-building/        ← Stage 0 + 4: reads/writes worldview context files, calls world-concept-art
│   └── evals/
├── world-concept-art/     ← generates environment/scene image prompts for worldview locations
│   └── SKILL.md
├── create-campaign/       ← cross-platform marketing campaigns with AI video prompts + cut strategies
│   ├── SKILL.md
│   ├── references/
│   │   ├── cut-strategies.md    ← cut transition strategy catalog (high contrast, irony, etc.)
│   │   ├── video-guide.md       ← AI video prompt system (12s cuts, shot types, lighting)
│   │   └── platform-strategies.md ← per-platform formats and tone guides
│   └── evals/
├── waifu-lore-weaver/     ← deep lore: events, secrets, relationships, diaries for existing characters
│   ├── SKILL.md
│   ├── references/
│   │   ├── file-formats.md      ← exact markdown structure for each lore file
│   │   └── write-back-rules.md  ← write-back trigger conditions, targets, principles
│   └── evals/
└── skill-creator/         ← meta-skill for building and evaluating skills
```

**`create-waifu` is the entry point for new characters.** Two modes:
- **Plan Mode** (default) — guided 4-step wizard with 16 worldview presets
- **Agent Mode** (`/a`, `auto`, `自动`) — generates concrete proposals, confirm and go

Both modes support re-roll (`r`) at confirmation to regenerate the character concept while keeping the worldview, or fully random (`0`) to start from scratch.

Pipeline: world-building (context read) → waifu-stories → waifu-generator → file assembly → world-building (expand → world-concept-art). The file assembly step (Stage 3) reads its instructions from `create-waifu/references/file-structure.md`.

**`edit-waifu` handles modifications** to existing characters, scenes, and worldviews. Five modes: targeted edit, iteration, add scene, expand scene, worldview edit.

**`create-campaign` generates cross-platform marketing campaigns.** Input: worldview + character subset. Output: strategy docs, platform-specific copywriting (Reddit/Twitter/TikTok/小红书/Discord), image prompts, and AI video prompts with cut-based composition (12s per cut) and transition strategies (high contrast, irony, progressive reveal, etc.). Campaign files land under `worldview/[slug]/campaign/[campaign-slug]/`.

**`waifu-lore-weaver` generates the deep memory layer** for existing characters. Input: 1 worldview + 1 character (+ related characters as context). Output: events.md (personal chronicle), secrets.md (layered secret map), relationships.md (private relationship map), diaries.md (first-person fragments). Supports write-back to upstream files (basics.md, timeline.md, factions.md, locations.md) when important discoveries emerge. Two modes: Full Run (first time) and Append (incremental additions).

## Character File Output Structure

All generated character files land under `worldview/[worldview-slug]/[character-slug]/`:

```
worldview/
└── [worldview-slug]/
    ├── world.md            ← 世界概览, 世界规则, 文化细节
    ├── factions.md         ← 势力与派系
    ├── locations.md        ← 重要地点
    ├── timeline.md         ← append-only historical events
    ├── characters.md       ← index of all characters in this world
    ├── concept-art/        ← world environment prompts (from world-concept-art)
    │   ├── overview.md     ← visual foundation (palette, lighting, style)
    │   ├── locations.md    ← per-location multi-angle prompts
    │   ├── atmosphere.md   ← time/weather/event overlays
    │   └── details.md      ← world-specific objects and textures
    ├── campaign/          ← marketing campaigns (from create-campaign)
    │   └── [campaign-slug]/
    │       ├── strategy.md     ← campaign strategy + cast selection + release cadence
    │       ├── cast.md         ← per-character promotional facet analysis
    │       ├── reddit.md / twitter.md / tiktok.md / xiaohongshu.md / discord.md
    │       └── video-prompts/  ← character-pvs.md, world-teaser.md, mood-pieces.md
    └── [character-slug]/
        ├── basics.md       ← sections 1–7 (narrative only, no image prompts)
        ├── concept-arts.md ← character + scene image prompts (from waifu-generator)
        ├── [scene-slug].md ← one file per scene
        ├── events.md       ← personal chronicle (from waifu-lore-weaver)
        ├── secrets.md      ← layered secret map (from waifu-lore-weaver)
        ├── relationships.md ← private relationship map (from waifu-lore-weaver)
        └── diaries.md      ← first-person diary fragments (from waifu-lore-weaver)
```

Slug convention: Chinese → pinyin hyphenated (`小铃` → `xiao-ling`), English → lowercase hyphenated.

## Installing a New Symlink

To make a skill available in this project:
```bash
ln -s ../../.agents/skills/<skill-name> .claude/skills/<skill-name>
```

To make it available globally:
```bash
ln -s /Users/gongqing/projects/waifus-workflow/.agents/skills/<skill-name> ~/.claude/skills/<skill-name>
```

## Skill Development (skill-creator)

Skills have a three-level loading hierarchy:
1. **Frontmatter** (`name` + `description`) — always in context; drives triggering
2. **SKILL.md body** — loaded when skill triggers; keep under ~500 lines
3. **`references/`** — loaded on demand from within SKILL.md instructions

Eval test cases live in `<skill>/evals/evals.json`. Running evals produces a workspace at `<skill-name>-workspace/` (sibling to the skill directory), organized by `iteration-N/eval-id/`. The viewer is generated via:
```bash
python -m scripts.aggregate_benchmark <workspace>/iteration-N --skill-name <name>
python <skill-creator-path>/eval-viewer/generate_review.py <workspace>/iteration-N --skill-name "name" --benchmark <workspace>/iteration-N/benchmark.json
```

Description optimization (for trigger accuracy) uses:
```bash
python -m scripts.run_loop --eval-set <path> --skill-path <path> --model <model-id> --max-iterations 5
```

All scripts are run from the `skill-creator/` directory (uses `-m` module syntax).
