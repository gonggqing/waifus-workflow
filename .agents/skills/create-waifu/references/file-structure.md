# Waifu File Structure Reference

## Folder Path Conventions

**Slug convention:** Chinese names → pinyin (e.g., `小铃` → `xiao-ling`), English names → lowercase hyphenated. When unsure of romanization, ask or approximate and note it.

**Path depends on whether a worldview exists:**

If the character belongs to an existing worldview, create the folder inside the worldview directory:

```
worldview/[worldview-slug]/[waifu-slug]/
├── basics.md              ← character core: sections 1–6 + image prompts
├── [scene-slug].md        ← one file per scene
└── ...
```

If the character has no worldview (standalone / legacy), create at project root:

```
[waifu-slug]/
├── basics.md
├── [scene-slug].md
└── ...
```

Standalone is the exception. Most characters belong to a worldview — use the worldview path.

**Scene file slugs:** derive from the scene name the same way — e.g., 场景 A · 三号窗口 → `san-hao-chuangkou.md`, 场景 B · 魔物逼近王都 → `mowu-bijin-wangdu.md`. If the name is short and clear in Chinese, a simple romanization is fine — ask the user if unsure.

---

## basics.md Structure

Contains all character core data plus the image prompts. Section headers in Chinese only.

```
# [角色名]

---

## 1. 基本信息

## 2. 世界观          （如有）

## 3. 角色简介

## 4. 背景介绍

## 5. 开场白

## 6. 初始对话

## 8. 图像提示词
```

Section 2 (世界观) is omitted if the character has no world context. Section 7 (场景) lives in the individual scene files, not in basics.md.

---

## Scene File Structure

Each scene gets its own file using the four-field format:

```markdown
# [场景名称]

**场景描述：** [one-line premise]

**开场白：**
"[opening line]"

**场景内容：**
[scene background — max 600 chars]
```

Leave `<!-- TODO -->` on incomplete fields rather than omitting them.

---

## Section Formatting Rules

### 1. 基本信息

```markdown
### **1. 基本信息**

- **名字:** [name]
- **标签:** [tag1, tag2, ...]
```

### 2. 世界观

Write as prose paragraphs or a short bullet list. No tables. Keep to ~500 chars.

### 3. 角色简介

Single **简述:** line. One or two sentences — the elevator pitch.

### 4. 背景介绍

Four sub-sections, all Chinese labels:

```markdown
**角色定义**
[character definition paragraphs]

**外貌（不主动提及）**
- **[trait label]:** ...

**个性特质**
- **[trait label]:** ...
- [quirk bullets without sub-labels]

**互动风格**
- **(语气)**：...
- **(动作)**：...
- **(情绪)**：...
```

### 5. 开场白

Single quoted line. No label needed beyond the section header.

### 6. 初始对话

Each exchange labeled **场景N：[topic]**, using `{{user}}:` / `**[角色名]:**` format.

### 7. 场景

Each scene uses the four-field structure:

```markdown
#### 场景 [X] · [name]

**场景描述：** [one-line premise]

**开场白：**
"[opening line]"

**场景内容：**
[scene background — max 600 chars]
```

Leave a `<!-- TODO -->` comment on incomplete scenes rather than omitting the block.

### 8. 图像提示词

If multiple platforms were generated, use a sub-header per platform. Within each platform:

```markdown
## 8. 图像提示词

### [Platform]

#### 角色基底

**Base A · [label]**
```
[base prompt]
```

**Base B · [label]**
```
[base prompt]
```

---

#### 立绘组

> [正面立绘 · description]
```
[prompt]
```

> [四分之三侧 · description]
```
[prompt]
```

> [便服 · description]
```
[prompt]
```

> [关键情绪 · description]
```
[prompt]
```

---

#### 场景插图

##### [场景名称]

> [variant 1 · description]
```
[prompt]
```
**负向提示词:**
```
[negative prompt]
```

> [variant 2 · description]
```
[prompt]
```

> [variant 3 · description]
```
[prompt]
```

---
```

**Negative prompts:**
- Include under the relevant prompt variant as **负向提示词:** block
- Midjourney has no negative prompts — omit the block entirely for MJ prompts

**Missing prompts:** use `<!-- TODO: run waifu-generator -->` rather than an empty section.

---

## After Writing

Report to the user:
- The folder path created and files inside it
- Which sections in basics.md have TODO placeholders
- Number of scene files created
- Number of portrait variants and scene prompt variants in the image section
- Platforms covered

---

## Path Examples

- 赛博朋克·璃光市 world + 薇拉 → `worldview/cyberpunk-liguang/wei-la/`
- 奇幻七王国 world + 小铃 → `worldview/fantasy-seven-kingdoms/xiao-ling/`
- Standalone (no worldview) + Elara → `elara/`
