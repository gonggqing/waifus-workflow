---
name: create-waifu
description: Interactive waifu creation wizard. Guides the user through 4 steps to define a character, checks for existing worldviews, then runs the full pipeline: world-building (context) → waifu-stories → waifu-generator → waifu-flows → world-building (expand). Use when the user says "create waifu", "新建角色", "create a new character", "make a waifu", "我要创建一个角色", or wants to start a waifu from scratch with guided input.
---

# Create Waifu

A guided 4-step wizard that collects character parameters, checks for existing worldviews, then runs the full creation pipeline.

---

## Before Step 1 — Check Existing Worldviews

Before presenting any steps, scan `./worldview/` for subfolders. Each subfolder is a worldview — read its `world.md` to get the title (H1) and tagline (`>` line).

If worldview folders exist, present them alongside the preset options in Step 1. If the directory is empty or doesn't exist, proceed with presets only.

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
I. 随机

选择已有世界观的编号，或输入字母选择预设，或自定义。
```

**If no worldview files exist**, show the presets only (numbered 1–9).

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
- 架空历史: 宫廷侍女、江湖侠女、将门之女、学塾先生、青楼花魁（名义）
- 西方中世纪: 骑士、修女、贵族、猎魔人、吟游诗人、炼金术士
- 赛博朋克: 黑客、雇佣兵、企业职员、街头医生、信息掮客、义体改造师
- 末世废土: 幸存者、部落首领、医疗物资贩子、前士兵、流浪者
- 校园青春: 班长、社团部长、转学生、学生会长、图书馆管理员

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

确认后开始创建。需要调整任何一项吗？
```

Wait for confirmation or adjustments before proceeding. If the user changes the name, use their version throughout the pipeline.

---

## Pipeline Execution

Run stages in order. Narrate each stage briefly so the user knows what's happening.

### Stage 0 — world-building (读取上下文)

If the user selected an **existing worldview**, load context from `./worldview/[slug]/` following world-building's context loading order:
- Always read `world.md` + `characters.md`
- Read `factions.md` if the character has faction affiliation
- Read `timeline.md` if the character has historical backstory

Use 世界概览/世界规则 to inform the character's background, 势力与派系 to ground their occupation, 角色档案 to avoid duplicating existing characters, locations and events to find scene hooks.

If the user selected a **new worldview**, no file to read yet — proceed with the preset vibe description.

### Stage 1 — waifu-stories

Translate all selections into a character brief. If an existing worldview was loaded, the brief should reference specific world details (factions, locations, events) rather than generic descriptions.

Include at least 2 scenes. If an existing worldview was loaded, at least one scene should connect to an existing location or event from that world.

Produce the full character markdown following waifu-stories conventions.

### Stage 2 — waifu-generator

Run waifu-generator on the character markdown. Default to SDXL unless the user specified otherwise. Generate base prompts, portrait set, and 2 scene variants per scene.

### Stage 3 — waifu-flows

Read `references/file-structure.md` for folder conventions and all file templates, then create the files.

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

- **Existing worldview selected:** run Mode 2 (扩展现有世界观). Append the new character to `characters.md` and update any other files the new character enriches.
- **New worldview selected:** run Mode 1 (创建新世界观). Create `./worldview/[slug]/` with all 4 files from the character's 世界观 section and basics. Move character folder to `worldview/[slug]/[character-slug]/` if not already there.

Report the worldview file path and what was added or created.

---

## Final Report

After all stages complete:

```
✓ 角色文件:   worldview/[worldview-slug]/[character-slug]/
✓ 世界观文件: worldview/[worldview-slug]/  ([新建 / 已更新，新增 N 项])
✓ 关系回写:   [existing-character] basics.md 已更新  (若有)
```

List any `<!-- TODO -->` placeholders or `<!-- ⚠️ 待协调 -->` flags that need attention.

---

## Handling Partial Input

If the user gives partial information at any step, fill gaps from context and presets, tell the user what was filled in, and continue. Don't stall.

If the user wants to skip a step entirely, use sensible defaults and note them in the confirmation summary.
