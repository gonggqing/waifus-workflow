---
name: create-campaign
description: "Generate cross-platform marketing campaigns for waifu worldviews — strategy docs, platform-specific copywriting, image prompts, and AI video prompts with cut-based composition and transition design. Use this skill whenever the user wants to create promotional content, marketing materials, video PVs, social media posts, or ad campaigns for their characters or worlds. Trigger on: \"campaign\", \"宣发\", \"推广\", \"PV\", \"预告片\", \"广告\", \"marketing\", \"promotion\", \"social media\", \"Reddit post\", \"Twitter post\", \"TikTok\", \"小红书\", \"Discord\", \"video prompt\", \"视频提示词\", \"角色PV\", \"世界观预告\", \"情绪短片\", \"mood piece\", \"trailer\", or when the user wants to promote a character or world across platforms. Also use when the user asks about cut transition strategies, video ad design, or AI video generation for their waifus. Do NOT use for character creation (use create-waifu), character editing (use edit-waifu), or world-concept-art generation (use world-concept-art)."
---

# create-campaign — 自动化宣发矩阵 / Campaign Matrix

Generate cross-platform, multi-character marketing campaigns from worldview + character assets. Each campaign produces strategy docs, platform-tailored copywriting, image prompts, and AI video prompts with cut-based composition.

## Core Principles

**世界观优先，角色组合出击。** The input unit is a **worldview + character subset**, not a single character. Choose the optimal character combination based on campaign purpose:
- **拉新 / Acquisition:** 1-2 characters with the strongest visual contrast or narrative tension
- **世界观展示 / Brand:** 3+ characters showing relationships and world depth
- **留存 / Retention:** Already-known characters in new scenes or hidden facets

**角色驱动，不是模板驱动。** Templates decide format; the character's inner tension decides content. Two tsundere characters from different worlds produce completely different campaigns because their core contradictions differ.

**每个平台卖的是不同的情绪。** Not "same character, different packaging" but "different genuine facets of the same character":
- Reddit → 好奇心 ("This AI said something I wasn't expecting")
- Twitter → 想互动 ("She says she doesn't care. The chat log says otherwise.")
- TikTok → 代入感 ("I want to try this")
- 小红书 → 归属感 ("Finally someone gets me")
- Discord → 养成感 ("My character is unique")

**视频不是图片的附属品。** AI video has its own rhythm, camera language, and narrative structure — it conveys emotional *change over time* that static images cannot.

---

## Prerequisites / 上游依赖

Before running a campaign, ensure these exist for the target worldview:

| Upstream | Files | Purpose |
|---|---|---|
| world-building | `world.md`, `factions.md`, `locations.md`, `timeline.md`, `characters.md` | World context + character registry |
| world-concept-art | `concept-art/overview.md`, `locations.md`, `atmosphere.md`, `details.md` | Environment/scene visual base for video backgrounds |
| waifu-generator | Each character's `concept-arts.md` | Character visual base for image/video prompts |

If `concept-art/` doesn't exist, recommend running world-concept-art first — campaign video prompts inherit scene/atmosphere keywords from it to ensure visual consistency.

---

## Workflow

### Step 0 — 读取资产 / Load Assets

Read the worldview and character files:

1. **World context:** `worldview/[slug]/world.md` (世界摘要 + 世界概览 + 世界规则)
2. **Factions:** `worldview/[slug]/factions.md`
3. **Locations:** `worldview/[slug]/locations.md`
4. **Character registry:** `worldview/[slug]/characters.md`
5. **Selected characters' `basics.md`** — extract: 角色简介, 背景介绍 (外貌 + 个性特质 + 互动风格), 开场白, 场景
6. **Selected characters' `concept-arts.md`** — extract: character base prompts + scene illustration prompts
7. **World visuals:** `worldview/[slug]/concept-art/` — extract: composable keywords (背景层, 光线层, 氛围层) from `overview.md` and location prompts from `locations.md`
8. **Scene files** as needed for dialogue material

### Step 1 — 策略分析 / Strategy

Analyze the worldview-character combination and produce a campaign strategy. Ask the user:

1. **Campaign 目的:** Acquisition / Brand / Retention / Mixed?
2. **角色子集:** Which characters? (default: all, or recommend based on purpose)
3. **目标平台:** Which platforms? (default: all five)
4. **Campaign slug:** e.g., `launch-2026`, `yunsheng-reveal`, `summer-event`

Then generate `strategy.md`:

```markdown
# [Campaign Name] — 策略总览 / Strategy

## 目的 / Purpose
[Acquisition / Brand / Retention — what this campaign is trying to achieve]

## 角色组合 / Cast Selection
[Why these characters — what combination creates the most tension/interest]

## 情绪切面分析 / Emotional Facet Analysis
[Per-character: what facet of this character sells on which platform]

## 投放节奏 / Release Cadence
[Which platform first, what sequence, timing between posts]

## A/B 变体 / A/B Variants
[2-3 angle variants per platform for testing]
```

### Step 2 — 角色传播切面 / Cast Analysis

Generate `cast.md` — for each selected character, analyze:

```markdown
## [角色名]

**核心矛盾 / Core Tension:** [the internal contradiction that makes this character interesting]
**传播切面 / Promotional Facets:**
- Reddit 切面: [curiosity hook — most counter-intuitive trait or surprising dialogue moment]
- Twitter 切面: [visual hook — expression contrast, iconic quote, debate-provoking moment]
- TikTok 切面: [immersion hook — POV scenario, role-play setup]
- 小红书 切面: [emotional hook — warmest or most piercing dialogue moment]
- Discord 切面: [engagement hook — daily message angle, community interaction style]

**视觉基底 / Visual Base:** [key appearance tags from concept-arts.md for image/video consistency]
```

### Step 3 — 平台物料生成 / Platform Content

For each target platform, read `references/platform-strategies.md` and generate the platform-specific content file. Each file includes:
- Platform-tailored copywriting (in the platform's primary language and tone)
- Image prompt(s) matching the platform's emotional register
- Video prompt(s) with cut-based composition (see Step 4)

Output one `.md` file per platform: `reddit.md`, `twitter.md`, `tiktok.md`, `xiaohongshu.md`, `discord.md`.

### Step 4 — 视频 Prompt 生成 / Video Prompt Generation

This is the most technical step. Read `references/video-guide.md` for the full prompt system, and `references/cut-strategies.md` for cut transition design.

**Key constraints:**
- **12s generation cap.** 1 cut = 1 AI video generation ≈ 10-12s (recommend 12s)
- **All text in post-production.** Never put captions, subtitles, titles, or taglines in video prompts — AI renders text badly. All text is manually overlaid in editing software.
- **English prompts only.** Even for Chinese worldviews — localize via subtitles and BGM.

**Video types and default cut counts:**

| Type | Duration | Cuts | Use |
|---|---|---|---|
| 角色 PV / Character Reveal | ~24s | 2 × 12s | Character introduction |
| 世界观预告 / World Teaser | ~36s | 3 × 12s | World showcase (flexible: 2-5 cuts) |
| 情绪短片 / Mood Piece | ~12s | 1 × 12s | Emotional micro-content |
| 对话实录 / Chat Replay | ~24s | 2 × 12s | Dialogue-driven content |

**Cut transition strategy:** For each multi-cut video, select a transition strategy based on the character's nature, story tension, and campaign goal. Read `references/cut-strategies.md` for the full catalog. Common strategies:

- **高反差 / High Contrast** — dramatic tonal shift between cuts (gentle → fierce, beautiful → broken)
- **反讽 / Irony** — the second cut subverts expectations set by the first
- **渐进揭示 / Progressive Reveal** — each cut peels back a layer (world → character → inner self)
- **节奏对比 / Tempo Contrast** — slow contemplative cut → fast action cut
- **情绪升温 / Emotional Escalation** — each cut intensifies the same emotion

Not every video needs a special strategy — straightforward "world → character → reveal" works for many characters. Strategies like High Contrast and Irony are most effective for characters with 落差感 (gap between appearance and reality, hidden identity, persona reversal).

**Prompt structure for each cut:**
```
[Shot Type] + [Subject] + [Action/Emotion] + [Setting] + [Lighting] + [Style] + [Technical]
```

Inherit environment keywords from `concept-art/overview.md` (composable keywords) and location prompts from `concept-art/locations.md`. Inherit character appearance from `concept-arts.md` base prompts.

Output: `video-prompts/character-pvs.md`, `video-prompts/world-teaser.md`, `video-prompts/mood-pieces.md`.

### Step 5 — 汇总报告 / Campaign Report

Print a summary:

```
═══ Campaign 完成 / Campaign Complete ═══

📁 worldview/[slug]/campaign/[campaign-slug]/
   ├── strategy.md        ✅ [purpose] · [N characters] · [M platforms]
   ├── cast.md            ✅ 传播切面 for [character names]
   ├── reddit.md          ✅ [N posts] · [N image prompts] · [N video prompts]
   ├── twitter.md         ✅ [N posts] · [N image prompts] · [N video prompts]
   ├── tiktok.md          ✅ [N scripts] · [N video prompts]
   ├── xiaohongshu.md     ✅ [N cards] · [N image prompts] · [N video prompts]
   ├── discord.md         ✅ [N templates] · [N video prompts]
   └── video-prompts/
       ├── character-pvs.md   ✅ [N PVs] · cut策略: [strategies used]
       ├── world-teaser.md    ✅ [N cuts] · [total duration]
       └── mood-pieces.md     ✅ [N pieces]

🎬 Cut 策略总览:
   [character/video → strategy used, e.g., "云笙 PV → 高反差 (serene → battle)"]

⚡ 后期提醒 / Post-Production Notes:
   - 所有字幕/标题在剪辑软件中手动叠加
   - 每个 cut 建议生成 2-3 次变体，选最佳
   - 角色一致性：先用 concept-arts.md 生成参考图 → img2vid
```

---

## Output Structure

```
worldview/[worldview-slug]/campaign/[campaign-slug]/
├── strategy.md             ← campaign 策略总览
├── cast.md                 ← 角色传播切面分析
├── reddit.md               ← Reddit 文案 + image/video prompts
├── twitter.md              ← Twitter 文案 + image/video prompts
├── tiktok.md               ← TikTok 脚本 + video prompts
├── xiaohongshu.md          ← 小红书图文 + image/video prompts
├── discord.md              ← Discord 素材 + video prompts
└── video-prompts/
    ├── character-pvs.md    ← 角色 PV prompt（per-cut, with transition strategy）
    ├── world-teaser.md     ← 世界观预告片 prompt（per-cut）
    └── mood-pieces.md      ← 情绪短片 prompt
```

**Campaign slug convention:** purpose + time or theme — e.g., `launch-2026`, `summer-event`, `yunsheng-reveal`, `faction-war-arc`.

---

## Campaign Lifecycle

Campaigns can be marked as: **draft** → **active** → **archived**.

- **draft:** Being written, not yet published. Default state at creation.
- **active:** Published and in use. Strategy should not change; content can be A/B tested.
- **archived:** Finished. Preserved as reference for future campaigns.

If a worldview has 3+ campaigns, consider creating `campaign/index.md` to overview all campaigns with timeline and status.

---

## Reference Files

Read these on demand during generation:

- `references/platform-strategies.md` — per-platform formats, emotional registers, tone guides, and example structures for all 5 platforms
- `references/video-guide.md` — AI video prompt system: prompt formula, shot types, camera movement, lighting keywords, model recommendations, common errors
- `references/cut-strategies.md` — cut transition strategy catalog: when to use each strategy, how to design cut pairs, character archetype matching
