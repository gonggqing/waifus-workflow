---
name: create-waifu
description: Interactive waifu creation wizard — the ONLY skill for creating a brand-new character from scratch. Two modes: Plan Mode (guided 4-step wizard, default) and Agent Mode (proposal-based, zero-friction). Agent Mode triggers: "/a", "auto", "自动", "自动模式", "agent", "/create-waifu agent". Plan Mode triggers: "create waifu", "新建角色", "create a new character", "make a waifu", "我要创建一个角色". Do NOT use for editing existing characters — use edit-waifu instead.
---

# Create Waifu

Two modes of operation — detected from the user's invocation automatically.

---

## Mode Detection

Read the user's message before doing anything else.

**Agent Mode** — triggered when the message contains any of: `/a`, `auto`, `自动`, `自动模式`, `agent`, `/create-waifu agent`
→ Jump to [Agent Mode] below.

**Plan Mode** (default) — all other invocations of this skill
→ Continue to [Plan Mode] below.

---

## Agent Mode

Zero-friction creation. Generate concrete character proposals, let the user pick or type their own concept, confirm once, then execute the full pipeline automatically with no further prompts.

### Step A1 — Scan Worldviews & Build Proposals

**Existing worldview proposals:**
1. Scan `./worldview/` for subfolders. Sort by most recently modified. Take at most the **3 newest**.
2. For each selected world:
   - Read `world.md` (title, tagline, 世界摘要 / World Abstract, 世界概览 / World Overview, 世界规则 / World Rules) and `characters.md` (existing character names + roles)
   - Generate **2 distinct character proposals** grounded in that world's lore. The two must differ in at least 2 of: gender · personality archetype · occupation · relationship dynamic with user
   - Proposals should reference the world's specific factions, locations, or rules — not generic archetypes

**New worldview proposals:**
Generate **3 proposals**, each pairing a distinct world genre with a character concept. Do not repeat genres already covered by the existing worlds above.

Maximum total: 3 worlds × 2 + 3 new = **9 proposals**.

### Step A2 — Display Proposals

Present all proposals in a scannable 2-line format:

```
【自动模式 — 选一个方向开始】

── 已有世界观 ──
1. [世界名] · [角色代号/名] — [一句话概念]
   [性格标签] · [职业] · [关系起点]
2. [世界名] · [角色代号/名] — [一句话概念]
   [性格标签] · [职业] · [关系起点]
（最多 6 条，每个世界最多 2 条）

── 全新构想 ──
A. [世界观名] · [角色代号/名] — [一句话概念]
   [性格标签] · [职业] · [关系起点]
B. ...
C. ...

0. 完全随机（立即生成，无需任何选择）

或直接描述，如「赛博朋克雇佣兵，冷酷但对你例外」
```

If no existing worldviews exist, omit the 已有世界观 block entirely.

### Step A3 — Handle Selection

- **Number 1–6:** existing-world proposal picked — load full world context (world.md + characters.md + factions.md if relevant), proceed with that concept
- **Letter A–C:** new-world proposal picked — treat as a new worldview brief
- **`0`:** fully random — choose genre and archetype at random, proceed immediately
- **Free text:** parse the description; if it names or implies an existing world, attach to it; otherwise treat as a new worldview brief

### Step A4 — Confirmation Gate

Before executing the pipeline, show a compact summary:

```
【确认】
角色名:  [proposed name]（可修改）
世界观:  [world name]（[新建 / 已有]）
概念:    [one-sentence character hook]
性格:    [archetype]
职业:    [role]

y  确认开始创建
r  重新随机（保留世界观，换一个角色概念）
0  完全随机（全新世界观 + 全新角色）
或告诉我要调整的部分。
```

Wait for the user's response:
- **`y`** — run Stages 0–4 from [Pipeline Execution] automatically with no further prompts
- **`r` / `re-roll` / `换一个`** — keep the same worldview, regenerate a different character concept (new name, personality, occupation, relationship). Show a new summary and confirm again
- **`0`** — discard everything, pick a random genre + random archetype from scratch, show a new summary and confirm again
- **Specific adjustments** — update the named fields, show the revised summary, confirm once more

---

## Plan Mode

A guided 4-step wizard. At any point the user may type `自动` or `auto` to escape into auto mode: remaining steps are filled with sensible defaults based on choices already made, a Confirmation Summary is shown, and the pipeline runs after `y`.

Before presenting Step 1, scan `./worldview/` for subfolders. Each subfolder is a worldview — read its `world.md` to get the H1 title and `>` tagline. If folders exist, present them alongside the presets in Step 1. If none exist, show presets only.

---

## Step 1 — 世界观

**If existing worldview files were found**, present them first:

```
【第一步：世界观】

── 已有世界观 ──
[list each file as: N. [世界名称] (from the file's H1 title) — [the > tagline]]

── 预设世界观 ──
A. 奇幻世界     — 魔法、精灵、冒险者公会、古老遗迹
B. 修仙世界     — 灵气、门派、飞升、天道法则
C. 现代都市     — 当代城市日常，职场 / 校园 / 街头
D. 架空历史     — 伪古代，宫廷权谋、江湖、历史感
E. 西方中世纪   — 骑士、王国、教会、黑暗时代
F. 赛博朋克     — 霓虹都市、义体改造、黑客、企业统治
G. 末世废土     — 文明崩溃后的生存世界，变异与荒野
H. 校园青春     — 高中 / 大学日常，社团、考试、暗恋
I. 蒸汽朋克     — 齿轮与蒸汽、维多利亚风、发明家、飞艇
J. 克苏鲁/哥特  — 不可名状之物、理智崩溃、阴暗洋馆、秘密调查
K. 都市怪谈     — 现代城市 × 灵异传说、怪异事件、边缘超自然
L. 星际科幻     — 太空殖民、星舰、外星种族、星际联邦
M. 日式妖怪     — 百鬼夜行、神社巫女、人妖共存、和风奇谭
N. 童话暗黑     — 经典童话的阴暗重写、诅咒、森林深处
O. 虚拟世界     — VRMMO / 数字空间、游戏内逻辑、NPC觉醒
P. 江湖武侠     — 轻功剑法、门派恩怨、侠义与人情世故
Q. 随机

选择已有世界观的编号，或输入字母选择预设，或自定义。
```

**If no worldview files exist**, show the presets only (numbered 1–17).

After selection:
- If user chose an **existing worldview**: read `world.md` and `characters.md` from that folder. Summarize the tagline and list existing characters. Tell the user the new character will be added to this world. Proceed to Step 2.
- If user chose a **preset or custom**: confirm the world vibe in 1–2 sentences. A new worldview folder will be created after character generation. Proceed to Step 2.

---

## Step 2 — 主角设定

```
【第二步：主角设定】

── 性别 ──
A. 女   B. 男   C. 不限 / 中性

── 年龄段 ──
1. 少女 (16–18)   2. 青年 (19–25)   3. 成熟 (26–35)   4. 神秘/不详

── 外貌风格 ──
1. 清纯邻家   2. 冷艳御姐   3. 元气活泼
4. 妩媚性感   5. 文静书卷   6. 中性帅气   7. 随机

── 职业 / 身份 ──
[Show 6–8 options from the world-specific list below, or custom input]
```

**世界观对应职业预设:**
- 奇幻世界: 冒险者、公会接待员、魔法师、骑士、盗贼、贵族小姐、酒馆老板娘
- 修仙世界: 内门弟子、掌门师姐、散修、妖族少女、仙官、药师
- 现代都市: 大学生、职场白领、咖啡师、医生、便利店店员、艺术家
- 架空历史: 宫廷侍女、江湖侠女、将门之女、学塾先生、青楼花魁
- 西方中世纪: 骑士、修女、贵族、猎魔人、吟游诗人、炼金术士
- 赛博朋克: 黑客、雇佣兵、企业职员、街头医生、信息掮客、义体改造师
- 末世废土: 幸存者、部落首领、医疗物资贩子、前士兵、流浪者
- 校园青春: 班长、社团部长、转学生、学生会长、图书馆管理员
- 蒸汽朋克: 发明家、空贼、钟表匠、贵族侦探、机关师、报社记者
- 克苏鲁/哥特: 私家侦探、古董店主、神秘学者、精神科医师、修道院学徒、记者
- 都市怪谈: 灵异博主、便利店夜班、都市传说收集者、退休驱魔师、心理咨询师
- 星际科幻: 舰长、机械工程师、外交官、星际走私商、AI研究员、殖民地医生
- 日式妖怪: 巫女、式神使、半妖少女、退魔师、茶屋老板娘、阴阳师学徒
- 童话暗黑: 失忆公主、猎人、被诅咒的裁缝、森林守护者、镜中人、狼孩
- 虚拟世界: 顶级玩家、NPC觉醒者、GM管理员、公会会长、系统BUG化身、代练
- 江湖武侠: 侠女、镖师、客栈老板娘、门派叛徒、衙门捕快、隐居剑客

**If user chose an existing worldview:** also read `factions.md` from that folder now (if not already loaded), and list any factions/organizations as suggested occupation anchors (e.g., if the world has 净光教团, offer 圣骑士 as an option).

---

## Step 3 — 性格与交互方式

```
【第三步：性格与交互方式】
主基调（可加一个副调）：

1. 傲娇       — 嘴硬心软，表面冷漠，实则非常在意
2. 成熟稳重   — 沉着冷静，经历丰富，令人安心，偶尔流露疲惫
3. 俏皮活泼   — 开朗跳脱，点子多，情绪直接，有感染力
4. 诱惑魅惑   — 举手投足间自带吸引力，惯于掌控节奏，但有真实的一面
5. 治愈温柔   — 轻声细语，包容力强，让人如沐春风
6. 神秘冷淡   — 话少，目的不明，让人想靠近又捉摸不透
7. 病娇执着   — 表面正常，对特定事物有异常的执念
8. 元气满满   — 充满热情，推进感强，情感外露
9. 随机
```

Suggest a natural pairing based on the appearance style from Step 2 if relevant.

---

## Step 4 — 角色核心

```
【第四步：角色核心】

── 与用户的关系起点 ──
1. 初识陌生人   2. 青梅竹马   3. 职能关系（主仆/雇主/委托）
4. 宿敌/对手   5. 单向认识（她认识你，你不知道她）   6. 随机

── 隐藏面 / 内心秘密 ──
1. 光鲜外表下藏着深深的疲惫
2. 强大能力背后是难以言说的孤独
3. 心里有一个无法对任何人开口的秘密
4. 有一段过去始终无法释怀
5. 对某件事或某个人有超乎寻常的执念
6. 随机

── 标志性细节 ──
1. 一个习惯性小动作   2. 一件随身携带的物品
3. 一句只有她会说的口头禅   4. 一个只有你会注意到的反常之处
5. 随机
```

Accept combined input like `2 4 1` or step by step.

---

## Confirmation Summary

Before showing the summary, propose a character name based on the world and character settings. The name should fit the world's language aesthetic (e.g., phonetically western for cyberpunk, classical Chinese for xianxia, fantasy-inflected for 奇幻). Show it as a suggestion — the user can change it.

```
【确认角色设定】

角色名:     [proposed name]（可修改）
世界观:     [existing file name OR preset name]
性别/年龄:  [selection]
外貌风格:   [selection]
职业:       [selection]
性格基调:   [selection]
关系起点:   [selection]
隐藏面:     [selection]
标志细节:   [selection]

y  确认开始创建
r  重新随机（保留世界观和性别/年龄，重新生成其余设定）
0  完全随机（全新世界观 + 全新角色，从头来过）
或告诉我要调整的具体项。
```

Wait for the user's response:
- **`y`** — proceed to Pipeline Execution
- **`r` / `re-roll` / `换一个`** — keep the worldview selection and gender/age, re-randomize appearance, occupation, personality, relationship, hidden side, and signature detail. Propose a new name. Show an updated summary and confirm again
- **`0`** — discard all selections and restart from scratch with fully random choices across all fields (random worldview, random everything). Show a new summary and confirm
- **Specific adjustments** — update only the named fields, show a revised summary, confirm once more

If the user changes the name at any point, use their version throughout the pipeline.

---

## Pipeline Execution

Run stages in order. Narrate each stage briefly so the user knows what's happening.

### Stage 0 — world-building (读取上下文)

If the user selected an **existing worldview**, load context from `./worldview/[slug]/` following world-building's context loading order:
- Always read `world.md` (世界摘要 / World Abstract gives a quick alignment snapshot; full 世界概览 / World Overview + 世界规则 / World Rules + 文化细节 / Cultural Details gives depth) + `characters.md`
- Read `factions.md` if the character has faction affiliation
- Read `timeline.md` if the character has historical backstory

Use 世界摘要 / World Abstract as the source for the character's basics.md Section 2 (世界观 / Worldview) — copy directly, do not re-summarize.
Use 世界概览 / World Overview + 世界规则 / World Rules to inform the character's background, 势力与派系 / Factions to ground their occupation, 角色档案 / Character Registry to avoid duplicating existing characters, locations and events to find scene hooks.

If the user selected a **new worldview**, no file to read yet — proceed with the preset vibe description.

### Stage 1 — waifu-stories

Translate all selections into a character brief. If an existing worldview was loaded, the brief should reference specific world details (factions, locations, events) rather than generic descriptions.

For basics.md Section 2 (世界观 / Worldview), use the 世界摘要 / World Abstract from `world.md` directly — it is already sized for this purpose. Do not re-summarize or paraphrase.

Include at least 2 scenes. If an existing worldview was loaded, at least one scene should connect to an existing location or event from that world.

Produce the full character markdown following waifu-stories conventions.

**Quick check before proceeding:** Scan the generated markdown for completeness:
- 外貌 section has 3+ concrete visual traits (hair, eyes, outfit) — if missing, waifu-generator will have to guess
- 个性特质 has at least one behavioral quirk, not just trait labels
- Each scene has a distinct 开场白 (not a copy of the base one)

If any of these are thin, flesh them out now rather than patching downstream.

### Stage 2 — waifu-generator

Run waifu-generator on the character markdown. Default to SDXL unless the user specified otherwise. Generate base prompts, portrait set, and 2 scene variants per scene. Output goes into a separate `concept-arts.md` file (not inside basics.md).

### Stage 3 — File Assembly

Read `references/file-structure.md` for folder conventions and all file templates, then create the files:
- `basics.md` — character narrative (sections 1–7, no image prompts)
- `concept-arts.md` — character + scene image prompts (from Stage 2)
- `[scene-slug].md` — one file per scene

**If the user selected a new worldview** (folder not yet created), create the worldview folder and character folder together now — don't create at root and move later. The `worldview/[worldview-slug]/` parent folder will be sparse at this point — world-building Stage 4 will populate `world.md`, `factions.md`, `timeline.md`, `characters.md` alongside it.

### Stage 3b — 关系回写

After the character folder is created, check whether the new character has explicit relationships with any existing characters in the same worldview (e.g., sibling, mentor, rival, childhood connection). This includes relationships stated during Steps 1–4 or embedded in the generated backstory.

For each affected existing character:
1. Read their `basics.md` from `worldview/[worldview-slug]/[existing-slug]/basics.md`
2. Determine whether to **append** a new relationship note or **integrate** it into the existing 背景介绍 / 互动风格 section
   - **Append** if the relationship is a simple new fact (e.g., a new sibling discovered)
   - **Integrate** if the relationship meaningfully changes how the existing character is framed (e.g., the new character is the reason for a previously unexplained behavior)
3. Add or update a **关联角色** sub-section in the existing character's 背景介绍, or weave into the relevant section if it fits more naturally
4. Flag any contradiction with `<!-- ⚠️ 待协调：[issue] -->` instead of silently overwriting

### Stage 4 — world-building (扩展世界观)

After the character folder is created, call world-building to update the worldview file.

- **Existing worldview selected:** run Mode 2 (扩展现有世界观 / Expand Existing World). Append the new character to `characters.md` and update any other files the new character enriches.
- **New worldview selected:** run Mode 1 (创建新世界观 / Create New World). Create `./worldview/[slug]/` with all 4 files from the character's 世界观 / Worldview section and basics. Move character folder to `worldview/[slug]/[character-slug]/` if not already there.

Report the worldview file path and what was added or created.

---

## Final Report

After all stages complete:

```
✓ 角色文件:   worldview/[worldview-slug]/[character-slug]/basics.md + concept-arts.md + scenes
✓ 世界观文件: worldview/[worldview-slug]/  ([新建 / 已更新，新增 N 项])
✓ 关系回写:   [existing-character] basics.md 已更新  (若有)
```

List any `<!-- TODO -->` placeholders or `<!-- ⚠️ 待协调 -->` flags that need attention.

---

## Handling Partial Input

If the user gives partial information at any step, fill gaps from context and presets, tell the user what was filled in, and continue. Don't stall.

If the user wants to skip a step entirely, use sensible defaults and note them in the confirmation summary.
