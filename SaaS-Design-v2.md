# SaaS Design v2 — Drawhisper Studio

> Updated based on the actual implemented UI. Supersedes the layout section of the original SaaS-Design.md.
> Original doc: `skills/SaaS-Design.md` (still authoritative for backend, data model, skill engine, deployment).

---

## What Changed: Design Doc → Reality

The original SaaS-Design.md envisioned a **wide sidebar (220px) + wide top bar + center chat + inspector (340px)** layout. The actual implementation evolved into a radically different, more modern approach:

| Aspect | Original Design | Implemented |
|---|---|---|
| **Sidebar** | 220px wide, dual-mode Tasks/Files toggle, text-heavy | 72px icon rail, 4 mode buttons (Agent/World/Generate/Assets) |
| **Top Bar** | Full-width: workspace selector, token counter, Cmd+K, avatar | Removed entirely — space reclaimed |
| **Center** | Chat-only (skill-triggered conversations) | Mode-switched: Agent chat **or** Generate panel (Kling AI-inspired) |
| **Chat Input** | Simple textarea + skill pill badge | Gemini-style rounded container: attachment dropdown, tools dropdown, skill pill in action bar, recommendation chips |
| **Inspector** | 340px, 4 tabs (Files/Preview/Diff/Raw) | Same concept, resizable, collapsible |
| **Welcome** | Not designed | Gemini-style empty state with heading + skill badges |
| **Font** | Inter + Noto Sans SC + JetBrains Mono | Nunito + Noto Sans SC + JetBrains Mono |

---

## Current Architecture: 3-Zone Layout

```
┌──────┬──────────────────────────────────────┬─────────────────┐
│      │                                      │                 │
│ ICON │           CENTER                     │   INSPECTOR     │
│ RAIL │           (mode-switched)            │   (resizable)   │
│ 72px │                                      │   collapsible   │
│      │  ┌────────────────────────────────┐  │                 │
│ Logo │  │ sidebarMode === "tasks"        │  │  Files          │
│      │  │   → ChatPanel                  │  │  Preview        │
│Agent │  │     (welcome → messages)       │  │  Diff           │
│World │  │                                │  │  Raw            │
│Gen   │  │ sidebarMode === "generate"     │  │                 │
│Files │  │   → GeneratePanel              │  │                 │
│      │  │                                │  │                 │
│ ---  │  │ sidebarMode === "world"        │  │                 │
│Tools │  │   → (not yet: world editor)    │  │                 │
│Gear  │  │                                │  │                 │
│ DH   │  │ sidebarMode === "files"        │  │                 │
│      │  │   → (not yet: file browser)    │  │                 │
│      │  └────────────────────────────────┘  │                 │
└──────┴──────────────────────────────────────┴─────────────────┘
```

---

## Module Responsibilities — Reclassified

### Sidebar (Icon Rail) — Global Navigation

The 72px icon rail is **app-level navigation**, not a content panel. It switches the center content mode. It should NOT show workspace files or skill lists — those concerns are delegated elsewhere.

**Nav items:**
| Icon | Mode | Center shows |
|---|---|---|
| MessageCircle | `tasks` (Agent) | ChatPanel — conversational AI workspace |
| Globe | `world` | WorldPanel — world overview, map, factions, timeline |
| Paint | `generate` | GeneratePanel — Kling AI-style image/video generation |
| FileDescription | `files` (Assets) | FileExplorerPanel — full workspace file browser |

**Bottom section:** All Tools, Settings, User avatar

**Key insight:** The sidebar is permanent, minimal, and mode-agnostic. This is Figma/VS Code/Discord's pattern — icon rail for macro-navigation, center for micro-content.

### Center Panel — Mode-Switched Content

The center panel hosts the **primary workspace content** based on sidebar mode. Each mode has its own full-height panel component:

1. **ChatPanel** (Agent) — The AI conversation workspace
   - Welcome screen when empty (heading + input + skill badges)
   - Messages with markdown rendering
   - Inline tool blocks, file cards
   - Gemini-style input with attachment dropdown, tools dropdown, skill pill, recommendation chips

2. **GeneratePanel** (Generate) — Visual asset generation
   - Character reference strip
   - Image/Video mode tabs with sub-tabs
   - Upload zone, prompt area
   - Model selector, generate button

3. **WorldPanel** (World) — *Not yet built*
   - World overview dashboard
   - Timeline view
   - Faction relationship graph
   - Quick-edit cards for world.md, factions.md, locations.md, timeline.md

4. **FileExplorerPanel** (Assets/Files) — *Not yet built*
   - Full workspace file tree
   - File metadata (size, modified, modified-by)
   - Drag-drop organization
   - Context menus (rename, delete, duplicate)

### Inspector (Right Panel) — Context Viewer

The inspector shows **detail for whatever is selected** in the center. It's context-dependent, not mode-specific:

- When a file is opened (from chat file card, or file explorer click) → shows file Preview/Raw/Diff
- When nothing is selected → empty state with recent files
- Future: when a character is selected in World mode → shows character card
- Future: when an image is generated → shows generation preview + metadata

**The original design's "Files" tab in the inspector was conflating two concerns:**
- **Project-level file browsing** → belongs in the center as its own mode (sidebar "Assets")
- **Single-file viewing** → belongs in the inspector

### Resolution: "Assets" vs "Files" Tabs

| Concept | Location | Purpose |
|---|---|---|
| **Assets** (sidebar → center) | Center panel, activated by sidebar Files button | Browse the full workspace: file tree, create/rename/delete, organize folders |
| **Files** tab (inspector) | Inspector panel, one of 4 tabs | Quick file list of recently-opened or contextually-relevant files |
| **Preview** tab (inspector) | Inspector panel | Rendered markdown of the currently open file |
| **Raw** tab (inspector) | Inspector panel | CodeMirror 6 editor for the currently open file |
| **Diff** tab (inspector) | Inspector panel | Unified diff when LLM edits a file |

---

## Next Moves — Priority-Ordered

### Phase 1.5: Complete the Shell (current sprint)

These are gaps between the design doc and the actual implementation:

- [ ] **Sonner toast system** — ✅ Done. Installed, wired to layout.
- [ ] **Attachment dropdown (Plus button)** — ✅ Done. Image (→ jpg 80%), text files, web links.
- [ ] **Copy toast** — ✅ Done. "已复制内容" via sonner.
- [ ] **AI SDK integration** — Wire up `useChat` to `/api/chat/route.ts`. The skill engine config exists (10 skills), the prompt assembler is not yet built. This is the highest-priority backend item.
- [ ] **Workspace file storage** — idb-keyval for MVP. Replace hardcoded demo content in inspector with real file reads.
- [ ] **WorldPanel stub** — Even a simple markdown viewer for `world.md` gives the Globe button purpose.
- [ ] **FileExplorerPanel stub** — File tree using workspace store. Clicking opens in inspector.

### Phase 2: Intelligence Layer

- [ ] **Streaming chat** — `streamText` + `useChat` + tools (read_file, write_file, list_directory, search_files, ask_user)
- [ ] **Skill routing** — keyword matching MVP (descriptions already have triggers)
- [ ] **Tool rendering** — collapsible summary bars in chat (design exists in SaaS-Design.md Section 3)
- [ ] **File write-back** — diff view in inspector when LLM edits files, accept/reject UX
- [ ] **Chat history persistence** — Neon Postgres studio.chat_messages

### Phase 3: Visual Pipeline

- [ ] **GeneratePanel wiring** — connect to image gen API (fal.ai)
- [ ] **Image prompt cards** — Copy + Platform selector in inspector
- [ ] **Model selector** — switch between Gemini / Qwen / GLM in chat input

---

## Design Strategy Improvements

### 1. Kill the "dual-mode sidebar" concept

The original design's Tasks/Files segmented toggle was **replaced by something superior** — the icon rail with 4 discrete modes. This is the correct direction because:

- Tasks and Files are not dual aspects of the same panel — they're independent workspace modes
- The icon rail scales to more modes (Generate, World) without UI complexity
- Each mode gets the full center width instead of fighting over 220px

**No action needed** — the current implementation is better than the design doc.

### 2. Inspector should be optional, not default

The original design assumed the inspector is always visible. For most mobile/tablet users and even desktop users with narrow screens, the inspector wastes space until they actually open a file.

**Recommendation:** Start with inspector collapsed. Auto-expand when:
- User clicks a file card in chat
- LLM writes a file (show diff tab)
- User explicitly clicks a file in the file explorer

### 3. Rethink the "All Tools" button

Currently bottom of sidebar, does nothing. Two options:
- **Option A:** Remove it. The tools dropdown in the chat input already surfaces all skills. "All Tools" is redundant.
- **Option B:** Make it a command palette trigger (currently Cmd+K). One-click access for mouse users.

**Recommendation:** Option B — clicking "All Tools" opens the command palette.

### 4. Token counter needs a home

The original design put it in the top bar (now removed). Options:
- Bottom of chat messages area (subtle, like Claude's UI)
- Inside the chat input container footer
- Inspector header when a conversation has context

**Recommendation:** Below the chat messages area, right-aligned, subtle `text-muted-foreground/40 text-xs tabular-nums`.

### 5. Workspace selector needs a home

The original design put it in the top bar. Without a top bar:
- **Option A:** Logo click opens workspace selector dropdown
- **Option B:** Command palette has "Switch workspace" action
- **Option C:** Settings gear includes workspace management

**Recommendation:** Option A — click the logo sparkles icon → workspace selector dropdown. Natural, discoverable. The logo currently does nothing.

---

## Updated Tech Stack

| Layer | Technology | Status |
|---|---|---|
| Framework | Next.js 16.2.1 (App Router, Turbopack) | ✅ Built |
| React | React 19.2.4 | ✅ |
| Styling | Tailwind CSS v4 + shadcn/ui v4.1.0 (base-nova, base-ui) | ✅ Built |
| Icons | itshover (22 animated) + Lucide React fallback | ✅ Built |
| Animation | motion v12.38.0 + `useReducedMotion` | ✅ Built |
| URL state | nuqs v2.8.9 | ✅ Built |
| Panels | react-resizable-panels v4.7.4 | ✅ Built |
| Toast | sonner | ✅ Built |
| Fonts | Nunito (EN) + Noto Sans SC (CN) + JetBrains Mono (code) | ✅ Built |
| Theme | MD3 dark, indigo hue 280, oklch palette | ✅ Built |
| Chat AI | AI SDK v6 (`streamText`, `useChat`) | ⏳ Not wired |
| File editor | CodeMirror 6 | ⏳ Not installed |
| Diff view | `diff` lib + custom renderer | ⏳ Not started |
| File storage | idb-keyval | ⏳ Not started |
| Database | Neon Postgres (shared with DrawHisper) | ⏳ Not started |
| Auth | Auth.js v5 | ⏳ Not started |
| Image gen | fal.ai | ⏳ Not started |

---

## Files Changed in This Sprint

| File | Change |
|---|---|
| `components/chat/attachment-dropdown.tsx` | New — Plus button → dropdown (images, text, links) |
| `components/chat/web-link-dialog.tsx` | New — https:// link input dialog |
| `components/chat/chat-input.tsx` | AttachmentDropdown replaces plain Plus button |
| `components/chat/chat-message.tsx` | Copy → sonner toast instead of icon swap |
| `components/ui/sonner.tsx` | Toast component (dark theme, top-center) |
| `app/layout.tsx` | Added `<Toaster />` |
