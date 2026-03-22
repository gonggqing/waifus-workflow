# SaaS Design — Waifus Workflow App

MVP scope: dog-food the waifu creation pipeline as a web app for the team, then open to individual creators.

---

## 1. Repo Structure

**Flat Next.js project. This repo as a git submodule under `skills/`.**

```
studio-drawhisper/              ← Next.js project (flat, no monorepo)
├── skills/                     ← git submodule → waifus-workflow
│   ├── .agents/skills/         ← skill definitions (read-only at runtime)
│   ├── worldview/              ← example data / templates only
│   └── CLAUDE.md
├── app/                        ← Next.js App Router
│   ├── (auth)/                 ← login, register
│   ├── (workspace)/            ← main app shell
│   │   ├── layout.tsx          ← 3-panel layout
│   │   ├── page.tsx            ← workspace home
│   │   └── [worldId]/          ← worldview workspace
│   └── api/
│       ├── chat/               ← streaming LLM endpoint
│       ├── files/              ← workspace file CRUD
│       └── skills/             ← skill execution
├── components/
│   ├── sidebar/
│   ├── chat/
│   └── file-viewer/
├── lib/
│   ├── skill-engine/           ← skill parser + prompt assembler
│   ├── file-store/             ← workspace file management
│   └── llm/                    ← LLM client (streaming)
├── public/
├── next.config.ts
├── tsconfig.json
└── package.json
```

Why flat, not monorepo:
- This repo has ONE app — Studio. No shared packages to extract yet.
- DrawHisper (the companion app) is a completely separate repo and Vercel project.
- Shared concerns (auth config, DB schema) are better handled as npm packages or environment config, not monorepo workspaces.
- Flat structure = simpler imports (`@/lib/...`), no workspace indirection, faster `npm install`.
- Can restructure to a monorepo later if a real need arises (shared UI library, shared DB package).

Why submodule:
- Skills = markdown + eval scripts. App = Next.js + DB + auth. Different lifecycles.
- Skill iteration doesn't require app rebuild. UI fixes don't touch skill definitions.
- Submodule pins a version. Bump deliberately when skills are tested.
- Team members can still use skills directly in Claude Code / Cursor without the web app.

### Skill Loading from Submodule

The skill engine discovers skills via a single configurable path:

```typescript
// lib/skill-engine/config.ts
export const SKILL_ROOT = path.join(
  process.env.SKILL_SUBMODULE_PATH ?? 'skills',  // default: ./skills/
  '.agents/skills'
);
```

**Discovery at startup:** Glob `{SKILL_ROOT}/*/SKILL.md` → parse YAML frontmatter (`name`, `description`) → build in-memory routing table. No manifest file needed for MVP — 9 skills, cold parse < 10ms.

**Path isolation — skills vs workspace:**

```
SKILL_ROOT  (.agents/skills/)       → read-only skill definitions
                                       skills never write here at runtime

WORKSPACE_ROOT  (per-user storage)  → read-write workspace files
                                       all tool calls (read_file, write_file)
                                       resolve paths relative to here
```

Skills reference `worldview/[slug]/` — those paths are **always relative to the user's workspace**, not the submodule. The tool executor (Section 4) resolves them:

```typescript
// Tool executor resolves all paths against the user's workspace
function resolvePath(toolPath: string, workspaceId: string): string {
  const normalized = path.normalize(toolPath);
  // Prevent path traversal — must stay inside workspace
  if (normalized.startsWith('..')) throw new ForbiddenPathError(toolPath);
  return path.join(getWorkspaceRoot(workspaceId), normalized);
}
```

**The skills don't need modification.** They say `./worldview/` — the tool layer translates that to the real storage path (idb-keyval key or S3 prefix). Same skill works in Claude Code (local filesystem) and Studio (virtual workspace).

---

## 2. User Model — Same App, Feature Flags

One app. Role-based feature surface.

```typescript
type UserRole = 'creator' | 'individual';

// Feature flags by role
const features = {
  'world-building':    { creator: true, individual: true },
  'create-waifu':      { creator: true, individual: true },
  'edit-waifu':        { creator: true, individual: true },
  'waifu-stories':     { creator: true, individual: true },
  'waifu-generator':   { creator: true, individual: true },
  'lore-weaver':       { creator: true, individual: true },
  'create-campaign':   { creator: true, individual: true },
  'story-pipeline':    { creator: true, individual: true },
  'world-concept-art': { creator: true, individual: true },
  'skill-creator':     { creator: true, individual: false },  // internal only
  'batch-operations':  { creator: true, individual: false },  // or paid tier
  'raw-file-editor':   { creator: true, individual: true },
};
```

Don't hide create-campaign from individuals — solo creators need promotion tools. Only hide skill-creator (meta-tool, no user value).

For MVP: just build creator mode. "Individual" is creator mode minus skill-creator and with usage limits (API cost gating).

---

## 3. UI Design

### Design Principles

This is a **creative authoring tool**, not a generic SaaS dashboard. Every design decision serves one goal: make the _generated waifu content_ feel alive and front-center. The UI is the frame — the characters, worlds, and stories are the painting.

**1. Material Design 3 (MD3) foundation.** Follow MD3 design patterns — dynamic color, elevation system, shape scale, motion tokens. Studio gets its own MD3 theme (distinct from DrawHisper’s branding) with a seed color tuned for a creative authoring tool. Dark-mode first — creative tools live in dark mode. Light mode as opt-in.

**2. Content density over whitespace.** Users are building complex worlds — they need to see context (chat + files + previews) simultaneously. Generous whitespace wastes viewport on a 3-panel layout. Use 4/8px spacing rhythm (MD3 spacing), compact but readable.

**3. Keyboard-first, mouse-friendly.** Command palette (`Cmd+K`) as the primary power-user navigation. Every sidebar action has a shortcut. But all actions remain click-accessible — no hidden-only keyboard paths.

**4. No emoji as UI icons.** Use **itshover** animated icons throughout — shadcn-compatible, built on `motion`, smooth hover animations that make the UI feel alive. Install via `npx shadcn@latest add <url>`. Fallback to Lucide for any missing glyphs. Emoji is fine *inside* user/AI content — never in structural UI.

**5. URL-driven state.** Every meaningful state — active workspace, open file, active skill, sidebar section — is reflected in the URL. Deep-linkable, Cmd+click works, browser back/forward works. Use `nuqs` for query param sync.

**6. Progressive disclosure.** Simple surface, depth on demand. New users see a clean chat + sidebar. Power users discover command palette, keyboard shortcuts, raw file editing, diff view. Don't overwhelm on first visit.

**7. Contextual, not static.** The sidebar adapts based on what's happening. During character creation, show relevant characters and worldview files. During story writing, show chapter structure. The sidebar is a *workspace navigator*, not a fixed menu.

### Tech Foundation

```
Icons:        itshover (itshover.com/icons) — animated, shadcn-native
              Fallback: Lucide React (lucide-react) for missing glyphs
Design:       MD3 — Material Design 3 patterns (color, elevation, shape, motion)
              Studio has its OWN theme seed — distinct from DrawHisper’s branding
Colors:       MD3 dynamic color system via CSS variables, dark-first
              Tonal surfaces: --surface, --surface-container, --surface-container-high
              On-surface: --on-surface, --on-surface-variant
              Primary: --primary, --on-primary, --primary-container
              Secondary/Tertiary for accent hierarchy
              Error: --error, --on-error
              Generate with Material Theme Builder → export CSS tokens
Typography:   Inter (UI) + Noto Sans SC (Chinese body) + JetBrains Mono (code/raw)
              Base: 14px, line-height 1.6, body max 65–75 chars per line
              font-variant-numeric: tabular-nums for token counts
Spacing:      4/8px system — paddings: 8, 12, 16, 24; gaps: 4, 8, 12, 16
Radius:       Consistent radius-sm(4px), radius-md(6px), radius-lg(8px)
Motion:       framer-motion (motion) for all animations
              150–300ms for micro-interactions, ease-out for enter, ease-in for exit
              Honor prefers-reduced-motion. Spring physics for panel resize.
              AnimatePresence for mount/unmount transitions
              layout prop for smooth reflow on panel resize
Breakpoints:  1024 (collapse right panel), 768 (stack to single panel + drawer)
```

### Layout — Three Panels

```
┌─────────────────────────────────────────────────────────────────────┐
│  [Logo]   玄霄宗 ▾            12.4k tokens       [Cmd+K]   [·]    │
├───────────┬─────────────────────────────────────┬───────────────────┤
│           │                                     │                   │
│  SIDEBAR  │         CENTER                      │  INSPECTOR        │
│  220px    │         flex-1                      │  340px            │
│  collaps. │                                     │  collapsible      │
│           │ ┌─────────────────────────────────┐ │                   │
│ ┌───────┐ │ │                                 │ │ ┌───────────────┐ │
│ │ TASKS │ │ │  Based on 玄霄宗's worldview    │ │ │ Files Preview │ │
│ │       │ │ │  and existing characters, I've  │ │ │  Diff   Raw   │ │
│ │ New   │ │ │  designed a new character:      │ │ ├───────────────┤ │
│ │ World │ │ │                                 │ │ │               │ │
│ │ New   │ │ │  ┌── read 4 files ── ✓ ──┐     │ │ │ # 苍鸢        │
│ │ Char  │ │ │  │ world.md    2.1K      │     │ │ │               │ │
│ │ Edit  │ │ │  │ factions   3.4K      │     │ │ │ ## 核心身份    │ │
│ │ Char  │ │ │  │ timeline   1.2K      │     │ │ │ 寒铁峰上最年  │ │
│ │ Lore  │ │ │  │ chars      0.8K      │     │ │ │ 轻的匠师，十  │ │
│ │ ...   │ │ │  └───────────────────────┘     │ │ │ 五岁入门...   │ │
│ │       │ │ │                                 │ │ │               │ │
│ ├───────┤ │ │  ┌── writing basics.md ── ⏳ ─┐ │ │               │ │
│ │ FILES │ │ │  │ ████████░░░░ streaming... │ │ │               │ │
│ │       │ │ │  └───────────────────────────┘ │ │               │ │
│ │ world │ │ │                                 │ │               │ │
│ │ facts │ │ └─────────────────────────────────┘ │               │ │
│ │ 苍鸢/ │ │ ┌─────────────────────────────────┐ │               │ │
│ │ 云笙/ │ │ │ Message...              [Send]  │ │               │ │
│ └───────┘ │ └─────────────────────────────────┘ │ └───────────────┘ │
├───────────┴─────────────────────────────────────┴───────────────────┤
│                  ◀ ── drag ── ▶           ◀ ── drag ── ▶            │
└─────────────────────────────────────────────────────────────────────┘
```

**Top bar:** Minimal. Workspace selector (dropdown), token counter (`tabular-nums`), command palette trigger (`Cmd+K`), user avatar. No breadcrumbs — the sidebar + URL handle navigation.

**Panels:** Two draggable splitters via `react-resizable-panels`. Double-click a splitter to collapse that panel. `Cmd+B` toggles inspector (right). `Cmd+\` toggles sidebar (left). On viewport < 1024px, inspector collapses to a bottom sheet.

### Sidebar — Dual-Mode Navigator

The sidebar has **two sections**, toggled by segmented control at top:

**Tasks** (default) — skill launcher, grouped by pipeline stage:
```
TASKS                             shortcuts
──────────────────────────────────────────
  World
    New World                      Cmd+Shift+W
    Concept Art                    Cmd+Shift+A

  Characters
    New Character                  Cmd+N
    Edit Character                 Cmd+E
    Deep Lore                      Cmd+L
    Character Art                  Cmd+Shift+G

  Stories
    New Story
    Plot Chapters
    Write

  Campaign
    New Campaign

  Settings                        (creator only)
    Skill Editor
```

**Files** — workspace file tree with metadata:
```
FILES
──────────────────────────────────────────
  玄霄宗/
  ├─ world.md                    2.1K
  ├─ factions.md                 2.0K
  ├─ locations.md                1.8K
  ├─ timeline.md                 1.2K
  ├─ characters.md               0.8K
  ├─ concept-art/
  ├─ 苍鸢/
  │  ├─ basics.md                4.2K  ← active
  │  ├─ concept-arts.md          2.8K
  │  ├─ events.md                1.9K
  │  ├─ secrets.md               1.6K
  │  ├─ relationships.md         2.1K
  │  └─ diaries.md               1.4K
  └─ 云笙/
     └─ ...
```

Clicking a file opens it in the inspector. Right-click for context menu (rename, delete, duplicate). Files modified by the current conversation get a subtle dot indicator.

**Contextual behavior:** When a skill is active (e.g., create-waifu running), the Files section auto-scrolls to and highlights the directory being written to. When lore-weaver runs, it highlights the character's directory.

### Command Palette — `Cmd+K`

The fastest path to any action. Fuzzy search across:
- Skill triggers ("create character", "新建角色", "deep lore")
- Workspace files ("basics.md", "苍鸢")
- Actions ("switch workspace", "toggle dark mode", "export")
- Recent conversations

```
┌─ ────────────────────────────────────────── ─┐
│  ⌘K  create char...                          │
├──────────────────────────────────────────────┤
│  → New Character       create-waifu    Cmd+N │
│    New Character Art   waifu-generator       │
│    Edit Character      edit-waifu      Cmd+E │
│  ─ Files ──────────────────────────────────  │
│    苍鸢/basics.md      4.2K                  │
│    characters.md       0.8K                  │
└──────────────────────────────────────────────┘
```

Implementation: shadcn/ui `<CommandDialog>` (wraps cmdk). Categories: Skills (weighted highest) → Files → Actions → History.

### Center — Chat with Inline Artifacts

**Tool blocks** render inline as collapsible summary bars — not full bordered boxes. Minimal visual weight when collapsed:

*Collapsed (default after completion):*
```
  ✓ Read 5 worldview files · 8.9K                        ⌄
```

*Expanded on click:*
```
  ✓ Read 5 worldview files · 8.9K                        ⌃
    world.md         2.1K    ✓
    factions.md      2.0K    ✓
    locations.md     1.8K    ✓
    characters.md    0.8K    ✓
    timeline.md      1.2K    ✓
```

*In-progress:*
```
  ⏳ Writing character files...                            ⌄
    basics.md        4.2K    ✓
    scene-1.md              ⏳  streaming...
    concept-arts.md          ○  pending
```

**File creation cards** — when the LLM finishes writing a file, a compact card appears inline. Clicking opens in inspector:

```
  ┌─ basics.md ───────────────── 4.2K · 7 sections ─┐
  │  # 苍鸢 · Cang Yuan                              │
  │  寒铁峰上最年轻的匠师，十五岁入门...               │
  │                                    Open ↗  Raw ↗ │
  └──────────────────────────────────────────────────┘
```

**ask_user blocks** — render as inline forms, not modals:
```
  ┌─ What worldview should this character belong to? ─┐
  │  [玄霄宗]     [末世废土]     [+ Create New]       │
  └───────────────────────────────────────────────────┘
```

**Streaming text** — standard token-by-token rendering. Chinese text uses Noto Sans SC at 15px for comfortable reading. `text-wrap: pretty` on paragraphs to avoid widows.

**Input bar** — bottom of center panel. Textarea with auto-resize (1–5 lines). Active skill shown as a pill badge: `[create-waifu ×]`. `Enter` to send, `Shift+Enter` for newline. `↑` to edit last message.

### Inspector — Right Panel

Four tabs at top: **Files** | **Preview** | **Diff** | **Raw**

**Preview** (default) — rendered markdown:
- Rich rendering with proper H1–H6, tables, blockquotes, code blocks
- Chinese typography optimized: Noto Sans SC, 15–16px, line-height 1.8
- Image prompts display as styled cards with "Copy Prompt" + "Generate" buttons
- Character basics.md gets special rendering: name as hero header, sections as cards

**Raw** — CodeMirror 6 with syntax highlighting:
- Markdown mode with Chinese IME support
- Line numbers, word wrap, search (`Cmd+F`)
- Direct editing enabled — changes save on blur (debounced 500ms)
- Show unsaved indicator in tab

**Diff** — activated automatically when LLM edits a file:
- Unified diff (not split). Green/red lines.
- Hunk-level accept/reject buttons
- "Accept All" / "Reject All" in tab header

**Files** — same tree as sidebar, but with more detail:
- File size, last modified, modified-by (user vs AI)
- Thumbnail previews for concept-art entries (future)

**Empty state** when no file selected:
```
  Select a file from the sidebar
  or let a skill generate content

  Recent: basics.md · concept-arts.md · world.md
```

---

## 4. Skill Engine — How Skills Run

The skill engine is the bridge between the chat UI and the LLM. It translates sidebar clicks and user messages into structured LLM calls.

### Architecture

```
User Message / Sidebar Click
         │
         ▼
┌─────────────────────────┐
│   Skill Router          │ ← matches user intent to skill
│   (description matching │    via embeddings or keyword match
│    or explicit trigger)  │
└─────────┬───────────────┘
          │
          ▼
┌─────────────────────────┐
│   Prompt Assembler      │ ← builds the full LLM prompt
│                         │
│   1. System: skill SKILL.md body
│   2. Context: relevant workspace files
│   3. References: skill's references/ files (on demand)
│   4. User message
└─────────┬───────────────┘
          │
          ▼
┌─────────────────────────┐
│   LLM Client            │ ← streaming response
│   (Claude / GPT-4o)     │
│                         │
│   Tools available:       │
│   - read_file            │
│   - write_file           │
│   - list_directory       │
│   - search_files         │
│   - ask_user             │ ← pause and ask for input
└─────────┬───────────────┘
          │
          ▼
┌─────────────────────────┐
│   Tool Executor         │ ← executes tool calls against workspace
│                         │
│   - read_file → return content from user's workspace
│   - write_file → create/update file, trigger diff view
│   - list_directory → return workspace tree
│   - search_files → grep across workspace
│   - ask_user → render question in chat, wait for response
└─────────┬───────────────┘
          │
          ▼
┌─────────────────────────┐
│   Write-back Handler    │ ← detects and surfaces write-back actions
│                         │
│   When LLM writes to timeline.md, locations.md, factions.md, or
│   modifies basics.md → show diff + confirmation prompt
└─────────────────────────┘
```

### Skill Loading Pipeline

How the Prompt Assembler loads a skill from the submodule:

```typescript
// lib/skill-engine/loader.ts
import { SKILL_ROOT } from './config';

async function loadSkill(skillId: string) {
  // 1. Read SKILL.md body (the system prompt)
  const body = await fs.readFile(
    path.join(SKILL_ROOT, skillId, 'SKILL.md'), 'utf-8'
  );

  // 2. Strip YAML frontmatter, keep the body
  const { content, data: meta } = parseFrontmatter(body);

  // 3. Resolve references/ on demand (when SKILL.md says "read references/X")
  //    The LLM doesn't get references upfront — they're loaded via tool calls
  //    or injected when the skill body explicitly includes them
  const refs = await glob(path.join(SKILL_ROOT, skillId, 'references', '*.md'));

  return { id: skillId, meta, prompt: content, referenceFiles: refs };
}
```

The Prompt Assembler then injects a workspace context preamble before the skill prompt:

```
All file paths are relative to the user's workspace.
The workspace contains: {tree of user's worldview/ files}
```

This is how skills written for `./worldview/` work in Studio without modification — the LLM sees the workspace tree and operates on it through the tool layer.

### Tool Definitions

The LLM gets these tools (mapped to workspace operations):

```typescript
const tools = [
  {
    name: 'read_file',
    description: 'Read a file from the user workspace',
    parameters: { path: 'string' }  // relative to workspace root
  },
  {
    name: 'write_file',
    description: 'Create or overwrite a file in the user workspace',
    parameters: { path: 'string', content: 'string' }
  },
  {
    name: 'edit_file',
    description: 'Apply a targeted edit to an existing file',
    parameters: { path: 'string', old_text: 'string', new_text: 'string' }
  },
  {
    name: 'list_directory',
    description: 'List files and folders in a workspace directory',
    parameters: { path: 'string' }
  },
  {
    name: 'search_files',
    description: 'Search for text across workspace files',
    parameters: { query: 'string', path?: 'string' }
  },
  {
    name: 'ask_user',
    description: 'Ask the user a question and wait for their response',
    parameters: { question: 'string', options?: 'string[]' }
  }
];
```

### Tool Rendering in Chat

Tool calls render as **collapsible summary bars** (see Section 3 — Center panel). Design rules:

- **Collapsed by default** after completion — one-line summary showing operation + total size
- **Expanded on click** — shows individual file details
- **In-progress** — shows animated spinner + per-file status (✓ done / ⏳ streaming / ○ pending)
- **Group consecutive same-type calls** — 4 sequential reads → one "Read 4 files" bar
- Tool bars use `--bg-elevated` background, `radius-md`, 1px border in `--border-subtle`
- No heavy box-drawing characters — use CSS borders and padding

**Write-back detection:** When the LLM writes to timeline.md, locations.md, factions.md, or modifies another character's basics.md, the write-back handler (see Section 8) intercepts and surfaces a confirmation card.

### Streaming UX

The LLM response streams in real-time:
1. **Tool calls** appear as blocks the instant they're invoked (not after completion)
2. **Text** streams token-by-token (standard chat UX)
3. **File writes** show a progress indicator while content streams, then snap to the preview card
4. **Write-backs** trigger a confirmation toast: "timeline.md updated — [View Changes]"

---

## 5. Data Model

### Workspace = Worldview Directory

Each user workspace IS a worldview directory — the same structure this repo produces:

```
workspace/
├── world.md
├── factions.md
├── locations.md
├── timeline.md
├── characters.md
├── concept-art/
├── [character-slug]/
│   ├── basics.md
│   ├── concept-arts.md
│   ├── [scene-slug].md
│   ├── events.md
│   ├── secrets.md
│   ├── relationships.md
│   └── diaries.md
├── stories/
│   └── [story-slug]/
│       ├── premise.md
│       ├── arcs.md
│       ├── structure.md
│       ├── threads.md
│       ├── chapters/
│       └── text/
└── campaign/
    └── [campaign-slug]/
```

### Storage

```
Shared Neon Postgres (one project)
 ├── platform schema     → users, sessions, credits, transactions (shared with DrawHisper)
 └── studio schema       → workspaces, chat_messages, file_metadata, generations

Workspace files
 ├── MVP: idb-keyval     → browser-local IndexedDB
 └── Prod: S3 bucket     → versioned, CDN delivery
```

**Workspace files: idb-keyval (MVP) → S3 (production).**
- **MVP:** Zero infra for dog-fooding. Files stored as key-value pairs in the browser. Key format: `workspace:{id}/path/to/file.md` → value: file content string. Workspace export/import as JSON blob for backup and sharing between team members.
- **Production:** Migrate from idb-keyval by batch-uploading all keys to S3 paths. Each write creates a new version (S3 versioning). User can revert any file. CDN delivery for the file viewer.

**Chat history + metadata:** Neon Postgres `studio` schema. Each message has: role, content, tool_calls (JSON), skill_id, workspace_id. See Section 10 for the full shared database architecture (platform + studio schemas).

**Versioning:** MVP relies on idb-keyval snapshots (manual export). Production uses S3 versioning — cheap insurance for a creative tool where "undo" is critical.

### Why file-like storage, not database rows

The blackboard pattern depends on files being addressable by **path**. If files were database rows:
- Tool definitions would need database queries instead of file paths
- Skill prompts that reference "read factions.md" or "read locations.md" would need translation
- The entire skill library (SKILL.md files) was designed around filesystem operations
- No migration cost from Claude Code → web app: same mental model

idb-keyval gives us path-based semantics locally. S3 gives us the same semantics at scale with versioning + CDN.

---

## 6. Tech Stack (MVP)

| Layer | Technology | Why |
|---|---|---|
| Framework | Next.js 16 (App Router) | SSR + API routes + streaming. One deployment unit |
| Styling | Tailwind + shadcn/ui | Fast to build, good defaults |
| Icons | itshover (+ Lucide fallback) | Animated hover icons, shadcn-native. `npx shadcn@latest add` |
| Animation | framer-motion (`motion`) | Layout animations, AnimatePresence, spring physics |
| Chat UI | Vercel AI SDK (`useChat`) | Built-in streaming, tool call handling, message formatting |
| LLM | Vercel AI SDK model routing | Multi-provider: Google (Gemini), Qwen, GLM via existing API keys |
| File panels | CodeMirror 6 (editor) + react-markdown (preview) | CodeMirror for raw view, react-markdown for rendered view |
| Diff view | `diff` lib + custom renderer | Or react-diff-viewer for quick MVP |
| Resizable panels | `react-resizable-panels` | Battle-tested, accessible |
| Database | Neon Postgres (shared with DrawHisper) | platform schema (users/credits) + studio schema (workspaces/chat). Single source of truth for credits |
| File storage (MVP) | idb-keyval (IndexedDB) | Zero-infra local storage for dog-fooding |
| File storage (prod) | S3 (existing bucket) | Versioned, CDN delivery. Migrate from idb-keyval |
| Auth | Auth.js (NextAuth v5) | Same pattern as DrawHisper. MVP: hardcoded email+password |
| Deployment | Vercel | Zero-config for Next.js, edge functions for streaming |

### Vercel AI SDK — Why This Matters

The AI SDK (`ai` package) handles the hardest parts of the chat UI:

```typescript
// Server: app/api/chat/route.ts
import { streamText } from 'ai';
import { google } from '@ai-sdk/google';
import { createOpenAICompatible } from '@ai-sdk/openai-compatible';

// Multi-provider setup — Vercel AI SDK model routing
const qwen = createOpenAICompatible({ name: 'qwen', baseURL: '...' });
const glm = createOpenAICompatible({ name: 'glm', baseURL: '...' });

const models = {
  'gemini-2.5-pro': google('gemini-2.5-pro'),
  'qwen-max': qwen('qwen-max'),
  'glm-4-plus': glm('glm-4-plus'),
} as const;

export async function POST(req: Request) {
  const { messages, skillId, workspaceId, modelId } = await req.json();

  // 1. Load skill prompt
  const skillPrompt = await loadSkillPrompt(skillId);

  // 2. Build context from workspace files
  const context = await loadWorkspaceContext(workspaceId, skillId);

  // 3. Stream with tools — model selected by user or skill default
  const result = streamText({
    model: models[modelId] ?? models['gemini-2.5-pro'],
    system: `${skillPrompt}\n\n${context}`,
    messages,
    tools: workspaceTools(workspaceId),  // read_file, write_file, etc.
    maxSteps: 20,  // allow multi-step tool use
  });

  return result.toDataStreamResponse();
}
```

```tsx
// Client: components/chat/ChatPanel.tsx
import { useChat } from '@ai-sdk/react';

function ChatPanel({ workspaceId, activeSkill }) {
  const { messages, input, handleSubmit, setInput } = useChat({
    api: '/api/chat',
    body: { workspaceId, skillId: activeSkill },
  });

  return (
    <div>
      {messages.map(msg => (
        <ChatMessage key={msg.id} message={msg} />
        // ChatMessage renders tool calls as collapsible blocks
        // and file references as preview cards
      ))}
      <ChatInput value={input} onChange={setInput} onSubmit={handleSubmit} />
    </div>
  );
}
```

The AI SDK gives us:
- Streaming text rendering (done)
- Tool call lifecycle (pending → executing → completed) with `toolInvocations` on each message (done)
- Multi-step tool use — LLM calls a tool, gets result, calls another tool, etc. (done)
- Client-side state management for messages (done)

We build: tool call rendering components, file preview cards, and the skill routing layer.

---

## 7. Skill Routing — How the Right Skill Activates

Two paths:

**Explicit (sidebar click):**
```
User clicks "New Character" →
  setActiveSkill('create-waifu') →
  prefill chat input: "Create a new character" →
  system prompt includes create-waifu/SKILL.md
```

**Implicit (free-form chat):**
```
User types: "给玄霄宗加一个新角色" →
  /api/chat receives message →
  skillRouter(message) → matches "新角色" → create-waifu →
  system prompt includes create-waifu/SKILL.md
```

Routing implementation (MVP — keyword matching, upgrade to embeddings later):

```typescript
function routeSkill(message: string, currentSkill?: string): string | null {
  // If a skill is already active and user is continuing the conversation, keep it
  if (currentSkill && !isNewTaskIntent(message)) return currentSkill;

  // Match against skill descriptions (same triggers as SKILL.md frontmatter)
  const skills = loadSkillManifest();
  for (const skill of skills) {
    if (skill.triggers.some(t => message.includes(t))) return skill.id;
  }

  // No match → general assistant (no skill loaded)
  return null;
}
```

For MVP, keyword matching is fine. The skill descriptions already have comprehensive trigger lists. Embeddings-based routing is a future optimization.

---

## 8. Write-Back UX

When the LLM modifies shared files (timeline.md, locations.md, factions.md, basics.md), the UI surfaces this prominently:

```
┌─ ⚡ Write-back detected ─────────────────────────┐
│                                                   │
│  story-writer updated 2 shared files:             │
│                                                   │
│  📄 timeline.md (+2 events)          [View Diff]  │
│  📄 苍鸢/basics.md (+1 line)         [View Diff]  │
│                                                   │
│  [Accept All]  [Review Each]  [Reject All]        │
└───────────────────────────────────────────────────┘
```

"Review Each" opens the diff view in the right panel, file by file. This keeps the human in the loop for all blackboard mutations — critical for creative tools where "silent edits" erode trust.

---

## 9. Image Generation — In-App vs External

### Current State

`waifu-generator` and `world-concept-art` produce **text prompts** for image generation platforms (Midjourney, SDXL, PixAI, LiblibAI). Today the user copies prompts and runs them externally.

### Decision: MVP = Copy Prompt, Phase 2 = In-App Generation

**MVP (Phase 1–2):** Prompts are first-class content. Display them as styled cards in the inspector with:
- **Copy** button (copies raw prompt to clipboard)
- **Platform selector** (MJ / SDXL / PixAI format variants) — waifu-generator already adapts to platforms
- Visual layout: prompt text + tags + negative prompt, each in its own section
- No API calls, no image generation infra

**Phase 3:** Add optional in-app generation via a pluggable provider:

```typescript
// lib/image-gen/provider.ts
interface ImageProvider {
  id: string;
  name: string;
  generate(prompt: string, options: ImageGenOptions): Promise<ImageResult>;
  estimateCost(prompt: string, options: ImageGenOptions): number;  // tokens or cents
}

interface ImageGenOptions {
  width: number;
  height: number;
  style?: string;        // e.g., 'anime', 'realistic'
  negativePrompt?: string;
  seed?: number;
}

interface ImageResult {
  url: string;           // temporary URL from provider
  cost: number;
  metadata: Record<string, unknown>;
}
```

**Provider candidates (ranked by anime quality + API ease):**

| Provider | Anime quality | API | Pricing | Notes |
|---|---|---|---|---|
| fal.ai | High (SDXL, Flux) | REST, fast | Pay-per-call (~$0.01–0.05) | Best DX, supports SDXL fine-tunes |
| Replicate | High (any model) | REST | Pay-per-second | Most flexible, run any model |
| Stability AI | Good (SD3) | REST | Credits | Official Stable Diffusion API |
| Together AI | Good | REST | Pay-per-call | Fast inference, competitive pricing |

**Architecture for in-app generation:**

```
User clicks "Generate" on prompt card
         │
         ▼
┌─────────────────────────────┐
│  Cost estimation shown      │  "~$0.03 · SDXL via fal.ai"
│  [Generate]  [Cancel]       │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│  POST /api/images/generate  │ ← server-side, user never sees API key
│  → provider.generate()      │
│  → save result to S3         │  ← permanent storage, not temp URL
│  → update concept-arts.md   │  ← append image URL below prompt
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│  Inspector shows:           │
│  - Generated image          │
│  - Prompt used              │
│  - Regenerate / Variations  │
│  - Download                 │
└─────────────────────────────┘
```

**Key decisions:**
- API keys stored server-side only — user never configures an API key for the default provider
- Cost displayed before generation — no surprise bills
- Generated images saved to S3 alongside workspace files (e.g., `苍鸢/images/portrait-1.webp`)
- concept-arts.md updated to reference generated images: `![portrait](images/portrait-1.webp)`
- Provider is pluggable — start with fal.ai, add more later
- Generation is **always optional** — prompts remain the primary output, images are a convenience layer

**What NOT to build:**
- No training/fine-tuning (use external services for LoRA etc.)
- No real-time generation during chat streaming (generate on explicit user action only)
- No image editing (inpainting, outpainting) — out of scope for a character authoring tool

---

## 10. Deployment — DrawHisper Integration

### Database Architecture: Shared Platform DB, Separate App Schemas

DrawHisper has a production-ready user system with Auth.js, credits, and subscriptions. **If Studio uses a separate user database, credits don’t transfer.** That’s a dealbreaker — a user who buys credits on DrawHisper expects them to work in Studio.

The right pattern: **one shared Neon Postgres project**, schema-separated.

```
Neon Postgres (one project, one database)
│
├── platform schema (shared)     ← BOTH apps connect here
│   ├── users                     Auth.js user table
│   ├── accounts                  OAuth providers
│   ├── sessions                  Auth.js sessions
│   ├── credits                   balance, tier, limits
│   ├── transactions              credit purchases, usage deductions
│   └── subscriptions             plans, billing cycles
│
├── app schema                    ← DrawHisper app connects here
│   ├── characters                companion chat data
│   ├── conversations             user-character chat history
│   └── ...                       (DrawHisper-specific tables)
│
└── studio schema                 ← Studio connects here
    ├── workspaces                one per worldview
    ├── chat_messages             skill conversation history
    ├── file_metadata             index of workspace files (content in idb/S3)
    └── generations               image gen jobs, costs, results
```

**Why this works:**

1. **Single credit balance.** User pays once, spends credits across both apps. `platform.credits` is the single source of truth. Studio deducts credits via the same `platform.transactions` table — `type: 'studio_llm_call'` vs `type: 'companion_chat'`.

2. **Single sign-on.** Auth.js sessions in `platform.sessions`, cookie domain set to `.drawhisper.com`. User logs into drawhisper.com, opens studio.drawhisper.com — already authenticated. Zero friction.

3. **App isolation.** Studio’s workspace/chat data never touches DrawHisper’s companion data. Different query patterns, different scaling needs. Schema separation keeps this clean.

4. **No table duplication.** No copying users/credits tables between databases. No sync jobs. No eventual-consistency bugs where a user’s credits are different in each app.

5. **One migration tool.** Same Drizzle/Prisma config, same `db push` / `db migrate`. Platform schema changes are shared; app/studio schema changes are independent.

**Auth.js configuration:**

```typescript
// Shared auth config — used by BOTH apps
// Cookie domain: .drawhisper.com (works for all subdomains)
export const authConfig = {
  // ... same providers, same adapter
  cookies: {
    sessionToken: {
      name: 'authjs.session-token',
      options: {
        domain: '.drawhisper.com',  // ← shared across subdomains
        path: '/',
        httpOnly: true,
        secure: true,
        sameSite: 'lax',
      },
    },
  },
};
```

**Credit deduction from Studio:**

```typescript
// Studio calls this when LLM generates content
async function deductCredits(userId: string, cost: number, meta: CreditMeta) {
  return db.transaction(async (tx) => {
    const user = await tx.query.platform.credits.findFirst({
      where: eq(credits.userId, userId),
    });
    if (!user || user.balance < cost) throw new InsufficientCreditsError();
    
    await tx.update(platform.credits)
      .set({ balance: sql`balance - ${cost}` })
      .where(eq(credits.userId, userId));
    
    await tx.insert(platform.transactions).values({
      userId,
      amount: -cost,
      type: 'studio_llm_call',  // distinguishes from companion_chat
      meta,  // { skillId, modelId, tokens, workspaceId }
    });
  });
}
```

**MVP shortcut:** For dog-fooding, skip credit deduction entirely — hardcoded team accounts with unlimited usage. Wire up the credit system when opening to individuals (Phase 4).

### What stays separate

- **Frontend codebases** — completely different UIs, no shared components worth extracting yet.
- **Vercel projects** — separate deployments, separate domains. Studio is its own Vercel project.
- **Environment variables** — each app has its own LLM API keys, but shares the same `DATABASE_URL` (same Neon connection string, both apps access all schemas).

### Subdomain

Yes, use a subdomain under drawhisper.com — it signals this is part of the DrawHisper ecosystem, and DNS + SSL are trivial on Vercel.

**`world.drawhisper.com` works**, but better options:

| Subdomain | Pros | Cons |
|---|---|---|
| `world.drawhisper.com` | Clear meaning, "world-building" | Feels limited to world-building only; this tool does characters, stories, campaigns too |
| `studio.drawhisper.com` | **"Studio" = creation tool.** Matches industry convention (Roblox Studio, Android Studio). Implies the full pipeline. | Slightly generic |
| `forge.drawhisper.com` | Evocative — forging characters, worlds. Unique. | Less immediately clear to outsiders |
| `create.drawhisper.com` | Direct, obvious intent | Very generic, could mean anything |
| `craft.drawhisper.com` | Creative connotation, implies care and craftsmanship | Could be confused with Minecraft |

**Recommendation: `studio.drawhisper.com`**

"Studio" is the strongest signal for a creation/authoring tool:
- DrawHisper (drawhisper.com) = the companion app users interact with
- DrawHisper Studio (studio.drawhisper.com) = where creators build the characters and worlds that power DrawHisper
- Natural language: "Open it in Studio" / "Published from Studio"
- Future-proof: story pipeline, campaign generation, image gen all fit under "Studio"

Vercel setup: add `studio.drawhisper.com` as a custom domain on the new project. DNS: CNAME `studio` → `cname.vercel-dns.com`.

---

## 11. MVP Scope — What to Build First

### Phase 1: Core Loop (dog-food)
- [ ] 3-panel layout with resizable splitters (react-resizable-panels)
- [ ] MD3 dark theme with Material Theme Builder tokens
- [ ] itshover animated icons (+ Lucide fallback) — no emoji in structural UI
- [ ] Chat with streaming (Vercel AI SDK + useChat)
- [ ] Tool execution: read_file, write_file, list_directory
- [ ] Tool rendering: collapsible summary bars with status
- [ ] Dual-mode sidebar: Tasks (skill launcher) + Files (workspace tree)
- [ ] Inspector: Preview (rendered markdown) + Raw (CodeMirror 6)
- [ ] Command palette (Cmd+K) via cmdk
- [ ] Skill routing: 3 skills (world-building, create-waifu, waifu-stories)
- [ ] Workspace: one hardcoded workspace per user (no multi-world yet)
- [ ] Auth: Auth.js with hardcoded email+password, cookie on .drawhisper.com
- [ ] Storage: idb-keyval for files, shared Neon Postgres (platform + studio schemas) for chat
- [ ] Deploy to studio.drawhisper.com
- [ ] URL-driven state (workspace, open file, active skill in query params)

### Phase 2: Full Pipeline
- [ ] All 9 current skills active
- [ ] Multi-workspace (multiple worldviews per user)
- [ ] Inspector Diff tab for edits (unified diff, hunk-level accept/reject)
- [ ] Write-back confirmation UI (Section 8)
- [ ] Chat history persistence + resume
- [ ] Migrate file storage from idb-keyval to S3 (existing bucket)
- [ ] Workspace versioning (S3 versioning)
- [ ] Image prompt cards with Copy + Platform selector
- [ ] Keyboard shortcuts for all sidebar actions
- [ ] Model selector in chat (switch between Gemini / Qwen / GLM)

### Phase 3: Story Pipeline + Image Gen
- [ ] create-story, plot-weaver, story-writer skills
- [ ] Story workspace view (chapter navigator in sidebar)
- [ ] Cross-panel linking (click character name in story → open basics.md)
- [ ] In-app image generation via fal.ai (with cost estimation UX)
- [ ] Generated images saved to S3, referenced in concept-arts.md
- [ ] Image gallery view in inspector for character concept-art

### Phase 4: Scale
- [ ] Wire up credit deduction (shared platform.credits with DrawHisper)
- [ ] Individual user tier with usage limits
- [ ] MD3 light mode theme
- [ ] Export (PDF, JSON, platform import)
- [ ] Collaborative editing (multi-user on same workspace)
- [ ] world-inspector (consistency checking)
- [ ] Responsive: single-panel mobile layout with bottom sheet inspector