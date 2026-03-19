# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A **skill-based waifu creation workflow** — a collection of Claude Code skills that together form a character creation pipeline for a waifu chat platform. There is no build system, test runner, or application code. The "code" is primarily Markdown skill definitions and Python eval scripts.

## Skill Architecture

Skills live in `.agents/skills/` and are symlinked into `.claude/skills/` (project-local) and `~/.claude/skills/` (global). The symlink is how Claude Code discovers and loads skills.

```
.agents/skills/
├── create-waifu/          ← orchestrator: runs the full pipeline
│   ├── SKILL.md
│   └── references/
│       └── file-structure.md   ← folder/file templates for character output
├── waifu-stories/         ← Stage 1: generates character JSON/markdown
├── waifu-generator/       ← Stage 2: generates image prompts (SDXL/MJ/etc.)
├── world-building/        ← Stage 0 + 4: reads/writes worldview context files
└── skill-creator/         ← meta-skill for building and evaluating skills
```

**`create-waifu` is the entry point.** It orchestrates the others in sequence: world-building (context read) → waifu-stories → waifu-generator → waifu-flows (file assembly) → world-building (expand). The file assembly step (Stage 3) reads its instructions from `create-waifu/references/file-structure.md`.

## Character File Output Structure

All generated character files land under `worldview/[worldview-slug]/[character-slug]/`:

```
worldview/
└── [worldview-slug]/
    ├── world.md            ← 世界概览, 世界规则, 文化细节
    ├── factions.md         ← 势力与派系, 重要地点
    ├── timeline.md         ← append-only historical events
    ├── characters.md       ← index of all characters in this world
    └── [character-slug]/
        ├── basics.md       ← sections 1–6 + section 8 (image prompts)
        └── [scene-slug].md ← one file per scene
```

Slug convention: Chinese → pinyin hyphenated (`小铃` → `xiao-ling`), English → lowercase hyphenated.

## Installing a New Symlink

To make a skill available in this project:
```bash
ln -s ../../.agents/skills/<skill-name> .claude/skills/<skill-name>
```

To make it available globally:
```bash
ln -s /Users/perflab/Documents/projects/waifus-workflow/.agents/skills/<skill-name> ~/.claude/skills/<skill-name>
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
