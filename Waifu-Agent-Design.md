# Waifu Agent Design — `character/new` in DrawHisper App

> Consumer-grade character creation agent for drawhisper.com.  
> Companion doc to [SaaS-Design.md](SaaS-Design.md) (Studio at studio.drawhisper.com).

---

## 1. The Two Products

```
studio.drawhisper.com              drawhisper.com/character/new
─────────────────────              ────────────────────────────
Power-user creation tool.          Consumer creation experience.
Chat-first, 3-panel layout.       Agent-guided wizard.
Full skill pipeline.               One skill: create a character.
Markdown files, worldview trees.   Structured form → JSON → DB.
For the team / advanced creators.  For every DrawHisper user.
Output: worldview/ directory.      Output: a Character record in DB.
```

Studio is the factory. The `character/new` agent is the **storefront** — where normal users create characters that live inside DrawHisper's companion chat.

---

## 2. Architecture: Guided Agent, Not Raw Chat

Normal users don't want a blank chat box. They want to be guided, surprised, and delighted. The flow is a **stepped agent** — a hybrid of form wizard and AI conversation where the AI does the heavy lifting and the user makes choices.

```
┌───────────────────────────────────────────────────────────┐
│  character/new                                     Step 2/4│
│                                                           │
│  ┌─ Agent ────────────────────────────────────────────┐   │
│  │                                                    │   │
│  │  Based on your choices, here are 3 character       │   │
│  │  concepts for a 赛博朋克 world:                     │   │
│  │                                                    │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐         │   │
│  │  │ 🔮 Neon  │  │ 💀 Ghost │  │ 🎭 Echo  │         │   │
│  │  │ Weaver   │  │ Runner   │  │          │         │   │
│  │  │          │  │          │  │          │         │   │
│  │  │ A rogue  │  │ Ex-corp  │  │ Street   │         │   │
│  │  │ hacker   │  │ assassin │  │ artist   │         │   │
│  │  │ who...   │  │ who...   │  │ who...   │         │   │
│  │  │          │  │          │  │          │         │   │
│  │  │ [Pick]   │  │ [Pick]   │  │ [Pick]   │         │   │
│  │  └──────────┘  └──────────┘  └──────────┘         │   │
│  │                                                    │   │
│  │  [🎲 Re-roll all]          [✏️ Describe your own]  │   │
│  │                                                    │   │
│  └────────────────────────────────────────────────────┘   │
│                                                           │
│  ┌─ Progress ─────────────────────────────────────────┐   │
│  │  ✓ World  →  ● Concept  →  ○ Personality  →  ○ Done│   │
│  └────────────────────────────────────────────────────┘   │
│                                                           │
│  [← Back]                                    [Skip Step →]│
└───────────────────────────────────────────────────────────┘
```

---

## 3. The 4-Step Flow

Maps to create-waifu's Plan Mode, but **the AI fills in everything — the user just picks and tweaks.**

### Step 1: World — Choose a vibe, not a worldview

```
Pick a world for your character:

┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐
│ 🏔 Fantasy │ │ ⚔️ Wuxia   │ │ 🌆 Modern  │ │ 🔮 Cyber   │
│ 奇幻冒险   │ │ 江湖武侠   │ │ 都市日常   │ │ 赛博朋克   │
├────────────┤ ├────────────┤ ├────────────┤ ├────────────┤
│ 🏫 Campus  │ │ 🌌 Sci-Fi  │ │ 👻 Horror  │ │ 🐉 Xianxia │
│ 校园青春   │ │ 星际科幻   │ │ 灵异怪谈   │ │ 修仙世界   │
├────────────┤ ├────────────┤ ├────────────┤ ├────────────┤
│ 🎭 Gothic  │ │ 🌸 Yokai   │ │ 🗡 Medieval│ │ 🎲 Random  │
│ 暗黑童话   │ │ 日式妖怪   │ │ 中世纪     │ │ 随机       │
└────────────┘ └────────────┘ └────────────┘ └────────────┘

Or describe your own world: [___________________________]
```

The user picks a genre card. The AI **generates a concise world context** internally (equivalent to world.md's 世界摘要). This is NOT saved as a worldview file — it's used as LLM context for the next steps.

### Step 2: Concept — AI proposes 3 characters, user picks one

```
The AI generates 3 distinct character concepts:
- Different in gender, personality, occupation, and relationship angle
- Each shown as a card with: name, one-line hook, 2-sentence premise
- User picks one, re-rolls all, or writes their own description

After picking, the user sees an expanded concept card:
- Name + title
- Appearance sketch (text description)
- Core trait + hidden side
- Relationship setup with user
- [Accept] or [Tweak: change her personality / make her older / ...]
```

"Tweak" opens a free-text input — the AI adjusts the concept based on the user's note and re-presents.

### Step 3: Personality & Voice — AI shows tone, user fine-tunes

```
Based on the concept, the AI proposes:
- Primary tone (e.g., 傲娇 tsundere) with explanation
- A sample greeting (initial_message)
- A sample dialogue exchange (seed_chat preview)
- Signature detail (catchphrase, habitual gesture, etc.)

User choices:
- [Sounds good] → accept all
- Adjust tone: [More playful] [More serious] [More flirty] [More mysterious]
- Re-write greeting: [___________________________]
```

The AI regenerates on each adjustment. Fast iteration, not a blank form.

### Step 4: Preview & Create — Full card, one-click publish

```
┌─ Character Preview ───────────────────────────────┐
│                                                    │
│  霓虹织者 · Neon Weaver                            │
│  "赛博朋克世界的地下黑客少女"                        │
│                                                    │
│  Tags: #赛博朋克  #黑客  #傲娇  #孤独              │
│                                                    │
│  ── Background ────────────────────────────────    │
│  在新上海的霓虹底层长大的少女，15岁就...            │
│  (truncated preview, expandable)                   │
│                                                    │
│  ── Opening Line ──────────────────────────────    │
│  "又一个被防火墙拦住的菜鸟？...算了，看你            │
│   可怜，这次免费帮你。"                              │
│                                                    │
│  ── Sample Chat ───────────────────────────────    │
│  You: 你今天心情怎么样？                             │
│  Her: 心情？你管我心情干嘛...又不是                   │
│       你关心就会变好的。(小声) 不过谢了。             │
│                                                    │
│  ── Scenes (2) ────────────────────────────────    │
│  🌃 深夜天台  ·  "别跟着我...好吧，你想              │
│                   看星星也可以，只是..."              │
│  💻 地下工作室 ·  "进来别乱碰！这些设备               │
│                   每个都比你的命贵。"                 │
│                                                    │
│  [✏️ Edit anything]     [🎲 Re-generate]            │
│                                                    │
│               [✨ Create Character]                 │
└───────────────────────────────────────────────────┘
```

"Create Character" calls the API, which:
1. Finalizes the JSON (name, description, tags, background_story, initial_message, seed_chat, scenes)
2. Validates all field limits (same as waifu-stories SKILL.md)
3. Inserts into DrawHisper's `app.characters` table
4. Deducts credits from `platform.credits`
5. Redirects to the character's page where the user can start chatting

---

## 4. Technical Implementation

### Single API route, multi-step conversation

```typescript
// drawhisper.com/app/api/character/agent/route.ts
import { streamText } from 'ai';

export async function POST(req: Request) {
  const { messages, step } = await req.json();

  // The system prompt is a SIMPLIFIED version of create-waifu + waifu-stories
  // — same creative principles, but output is structured JSON, not markdown files
  const systemPrompt = buildAgentPrompt(step);

  const result = streamText({
    model: selectModel(),
    system: systemPrompt,
    messages,
    tools: {
      propose_characters: {
        description: 'Propose character concepts for the user to pick from',
        parameters: z.object({
          concepts: z.array(z.object({
            name: z.string().max(24),
            hook: z.string().max(48),
            premise: z.string().max(200),
            gender: z.enum(['female', 'male', 'neutral']),
            tone: z.string(),
          })).max(3),
        }),
      },
      finalize_character: {
        description: 'Generate the complete character JSON',
        parameters: characterSchema,  // same schema as waifu-stories output
      },
    },
  });

  return result.toDataStreamResponse();
}
```

### System prompt design

**The system prompt is NOT the full SKILL.md.** It's a distilled version:
- Same creative principles (show-don't-tell, voice through dialogue, no generic traits)
- Same field limits and counting rules
- Same worldview presets
- But no file operations, no worldview directory management, no multi-stage pipeline
- Output is structured tool calls (JSON), not markdown files

### Step-to-prompt mapping

| Step | System prompt includes | Tool called |
|---|---|---|
| 1. World | 16 worldview presets, world generation rules | — (user picks from UI) |
| 2. Concept | World context + character differentiation rules | `propose_characters` |
| 3. Personality | Concept + tone palette + dialogue writing rules | `propose_characters` (refined) |
| 4. Preview | Full waifu-stories output rules + field limits | `finalize_character` |

### Rendering

Use `useChat` with custom tool rendering. Each tool call (`propose_characters`) renders as the card UI shown above. The user's "Pick" / "Re-roll" actions are sent as follow-up messages.

---

## 5. Studio vs Agent: When to Use What

```
                    Studio                      character/new Agent
                    ──────                      ────────────────────
User               Team creators               Normal DrawHisper users
Goal               Full worldview + pipeline   One character, ready to chat
World handling     Creates worldview/ files     Generates context on-the-fly
Output             Markdown files in workspace  JSON → DB record
Scenes             Unlimited, separate files    2–3, inline in JSON
Lore               events, secrets, diaries     Not available (too complex)
Image prompts      concept-arts.md              Optional (Phase 3)
Quality ceiling    Highest (multi-step refine)  High (same AI, simpler flow)
Time to result     10–30 min (full pipeline)    2–5 min (4 steps)
Credits cost       Higher (many LLM calls)      Lower (fewer, focused calls)
```

---

## 6. Shared Intelligence, Different UX

The key insight: **both products use the same creative intelligence** (character writing rules, dialogue quality standards, worldview presets). The difference is UX surface:

```
                       ┌──────────────────┐
                       │  Creative Rules  │  ← shared
                       │  (from SKILL.md  │
                       │   principles)    │
                       └────────┬─────────┘
                                │
               ┌────────────────┼────────────────┐
               ▼                                  ▼
    ┌─────────────────┐                ┌─────────────────┐
    │  Studio          │                │  character/new   │
    │  Chat + Tools    │                │  Stepped Agent   │
    │  Markdown files  │                │  JSON → DB       │
    │  Full pipeline   │                │  Pick & tweak    │
    └─────────────────┘                └─────────────────┘
```

Extract the shared rules into a `lib/creative-rules/` package:
- Character writing principles
- Worldview presets (the 16 genres)
- Field limits and validation
- Tone palette definitions
- Dialogue quality guard-rails

Both Studio's skill engine and the character/new agent import from the same source. When you improve character quality in one place, it improves everywhere.
