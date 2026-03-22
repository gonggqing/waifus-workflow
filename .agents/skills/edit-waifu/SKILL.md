---
name: edit-waifu
description: Edit and iterate on existing waifu characters, scenes, and worldviews. Use this skill whenever the user wants to modify a character's personality, appearance, background, or voice; add, rewrite, or remove scenes; adjust worldview lore; regenerate image prompts after changes; or iterate on a character that doesn't feel right yet. Trigger on: "编辑角色", "修改角色", "edit waifu", "update character", "改场景", "add scene", "换性格", "调整", "这个角色不太对", "重写", "换个开场白", "this character doesn't feel right", "she's too bland", "needs more depth", "make her more interesting", or any request to change something about an existing character or world. Also use when the user says a character feels "flat", "generic", or wants to try a different direction.
---

# Edit Waifu

An interactive editor for modifying existing characters, scenes, and worldview files. Works with the file structure produced by create-waifu.

---

## Before Anything — Locate the Target

The user might reference a character by name, by file path, or just say "that last character". Figure out what they mean:

1. If a path is given, read it directly
2. If a character name is given, scan `./worldview/` for matching `basics.md` files — check the `# [角色名]` heading in each
3. If ambiguous (multiple matches or no match), list what's available and ask

Once found, read the character's `basics.md` and list their scene files. Present a brief summary:

```
找到角色：[name]
世界观：[worldview name] (worldview/[slug]/)
场景：[list scene files]

你想修改什么？
```

---

## Edit Modes

The user might ask for one specific change or a broad "make this better". Handle both.

### Mode A — Targeted Edit

The user knows exactly what to change: "换个开场白", "add a library scene", "她的性格太温柔了，改凶一点".

1. **Identify the section(s) affected** — map the request to specific file sections:
   - Personality/voice → 角色定义, 个性特质, 互动风格 in `basics.md`
   - Appearance → 外貌（不主动提及）in `basics.md`
   - Background → 背景介绍 in `basics.md`
   - Opening line → 开场白 in `basics.md`
   - Seed dialogue → 初始对话 in `basics.md`
   - Scene → the specific `[scene-slug].md` file
   - Image prompts → `concept-arts.md` in the character folder
   - World lore → `world.md`, `factions.md`, `locations.md`, `timeline.md` in the worldview folder

2. **Show the current content** of just that section (not the whole file)

3. **Draft the replacement** and show it as a diff-style before/after

4. **Apply on confirmation** — edit only the targeted section, leave everything else untouched

### Mode B — Iteration ("this doesn't feel right")

The user is unhappy but vague. This is the most common case after a first creation pass.

1. **Ask one focusing question** — don't barrage them with five questions. Pick the most likely issue:
   - "哪里感觉不对？是性格太平、开场白没吸引力、还是整体方向不对？"
   - If they say "just make it better", propose 2–3 specific changes you'd make and why

2. **Offer quick-swap options** for the highest-leverage fields:
   - 2–3 alternative `initial_message` options (this changes perceived personality fastest)
   - 1–2 alternative `seed_chat` snippets
   - A revised personality angle if the archetype feels stale

3. **Apply the user's choice**, then ask if there's more to adjust

### Mode C — Add Scene

1. Ask what situation/mood the scene should cover (or accept the user's description)
2. Draft using the four-field format:
   ```markdown
   # [场景名称]

   **场景描述：** [one-line premise]

   **开场白：**
   "[opening line]"

   **场景内容：**
   [scene background — max 600 chars]
   ```
3. Save as `worldview/[worldview-slug]/[character-slug]/[scene-slug].md`
4. Optionally generate scene image prompts (ask if the user wants them) — append to `concept-arts.md`

### Mode D — Expand Scene

An existing scene is too thin or the user wants more depth.

1. Read the current scene file
2. Identify what's underdeveloped — usually 场景内容 is too sparse or 开场白 is generic
3. Draft an expanded version:
   - Thicken 场景内容 with sensory details, character-specific reactions, environmental texture (stay within 600 char limit)
   - Rewrite 开场白 to be more in-character and situational
   - Refine 场景描述 if it's too vague
4. Show before/after, apply on confirmation

### Mode E — Worldview Edit

User wants to modify world lore, factions, locations, or timeline.

1. Identify which worldview file(s) to edit
2. Follow world-building's principles:
   - **世界摘要 / World Abstract** — if the edit changes the world's structural identity (new faction, shifted power balance, new core rule), **rewrite the entire 世界摘要** to reflect the current state (≤300 chars, cap 500)
   - **世界概览 / World Overview** — integrate new information by rewriting the overview paragraph to stay coherent (not append)
   - **factions.md / locations.md / timeline.md** — append or modify specific entries
   - **Flag contradictions** with `<!-- ⚠️ 待协调：[issue] -->`
   - Match the world's prose voice (cyberpunk ≠ xianxia ≠ modern)
3. If the change affects existing characters (e.g., a faction is renamed or dissolved), flag which character files need review
4. If 世界摘要 / World Abstract was rewritten, note that existing characters' basics.md Section 2 (世界观 / Worldview) may need updating (list affected characters but don't auto-edit without confirmation)

---

## Cascading Changes

Some edits ripple outward. Handle these automatically:

| What changed | What else to update |
|---|---|
| Appearance (外貌) | Offer to regenerate image prompts in `concept-arts.md` |
| Personality (个性特质/互动风格) | Review 开场白 and 初始对话 — still consistent? |
| Background (背景介绍) | Check worldview `characters.md` summary — still accurate? |
| New scene added | Update `concept-arts.md` with scene variants if prompts exist |
| Character name | Update all references: basics.md header, scene files, characters.md entry |
| World rules changed | Flag potentially affected characters in same worldview |

Don't silently cascade — tell the user what else changed and why.

---

## Regenerating Image Prompts

If appearance or outfit changed, the existing prompts in `concept-arts.md` are stale. Offer to regenerate:

1. Read the updated 外貌 and 个性特质 sections from `basics.md`
2. Follow waifu-generator's process: rebuild visual profile → base prompts → portraits → scene prompts
3. Replace `concept-arts.md` in the character folder
4. Keep the same platform(s) as before

If only a scene was added or changed, generate just the new scene prompts and append them to `concept-arts.md`.

---

## Cross-Character Updates

If the edit changes a relationship or shared history with another character in the same worldview:

1. Identify affected characters from `characters.md`
2. Read their `basics.md`
3. Propose specific edits to their files (don't apply without showing the user)
4. Flag contradictions with `<!-- ⚠️ 待协调：[issue] -->`

---

## Voice Consistency

After any edit to personality, background, or voice-related fields, do a quick consistency check:

- Does the 开场白 still sound like this character?
- Does the 初始对话 match the updated personality?
- Do scene 开场白s align with the character's voice?

If something is off, point it out and offer a fix. Don't silently let inconsistencies through.

---

## After Editing

Report what changed:

```
✓ 已修改: [list of files and sections changed]
✓ 级联更新: [any cascading changes made]
⚠ 需要注意: [any flags or inconsistencies]
```

Ask if there's anything else to adjust. Editing is inherently iterative — expect multiple rounds.
