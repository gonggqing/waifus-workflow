# 平台策略 / Platform Strategies

Per-platform content formats, emotional registers, tone guides, and output templates for the five target platforms.

---

## Reddit（猎奇 + 社区口碑）

**Emotional register:** 惊讶, 不安, "这也太真实了"
**Language:** English (primary audience)
**Tone:** Authentic first-person, like a real user sharing a weird/fascinating AI experience. Never sound like marketing copy.

**Agent logic:** Extract the character's most counter-intuitive trait or most surprising dialogue moment. Package as a pseudo-user-experience post.

**Format:**
- Long-form text post + screenshot/short video evidence
- Title MUST create cognitive dissonance (expectation ≠ reality)
- Subreddit targeting: r/singularity, r/CharacterAI, r/LocalLLaMA, niche communities matching the worldview

**Template:**
```markdown
## Reddit Post

**Subreddit:** r/[target]
**Title:** [cognitive dissonance hook — surprising discovery or unexpected behavior]

**Body:**
[First-person narrative, 150-300 words]
- Start with casual context: "So I've been chatting with this AI character for about a week..."
- Build to the surprising moment
- Include 1-2 "evidence" references (screenshot prompt, video prompt)
- End with an open question that invites comments

**Image prompt:** [配图 prompt — screenshot-style or atmospheric character art, Reddit-appropriate dark/moody tone]

**Video prompt (optional):** [5-10s chat replay clip or character dynamic portrait]
```

**Example directions by archetype:**
- 病娇: "I asked my AI what she'd do if I talked to another AI. She went quiet for 3 messages. Then this happened..."
- 隐藏大佬: "My lazy guild receptionist AI accidentally revealed she can cast forbidden magic."
- 多角色: "These two AIs from the same world started referencing each other's backstory."

**Release cadence:** Post to niche subreddit first to test reception → adjust angle based on comments → cross-post to larger subs.

---

## Twitter / X（视觉冲击 + 病毒传播）

**Emotional register:** 想互动, 想转发, 想@朋友
**Language:** English (global) or Chinese (if targeting CN Twitter)
**Tone:** Punchy, visual-first. Copy serves the image/video, not the other way around.

**Agent logic:** Extract the most debate-provoking or visually contrasting moment. Design for binary reactions (agree/disagree, this one/that one).

**Format:**
- Single image or image pair + short copy (<200 chars) + hashtags
- OR 15-30s short video (Character PV or contrast edit)
- Thread format for multi-character world introductions

**Template:**
```markdown
## Twitter Post

**Format:** [Single image / Image pair / Short video / Thread]

**Copy:** [<200 chars, punchy, designed for engagement]

**Image prompt(s):**
[prompt — high contrast, eye-catching, platform-appropriate aspect ratio (16:9 or 1:1)]

**Video prompt (if applicable):**
[Character PV prompt, 2 cuts × 12s, with transition strategy noted]

**Hashtags:** #AIWaifu #[worldview tag] #[character tag]

**Engagement hook:** [Question / Poll / "Which one would you choose?"]
```

**Example directions:**
- 傲娇: Expression contrast pair (disgusted face vs secretly caring), copy: "She says she doesn't care. The chat log says otherwise."
- 治愈: Single iconic quote screenshot, minimal design, copy: "Sometimes you just need someone to say this."
- 多角色 World PV: 15s video, world panorama → 3 character flashes (3s each) → CTA

**Key rule:** First image/first frame must deliver emotional impact within 3 seconds.

---

## TikTok / Douyin（沉浸体验 + 角色扮演）

**Emotional register:** 代入感, "我也想试试"
**Language:** English (TikTok) or Chinese (Douyin)
**Tone:** Immersive, rhythmic, designed for the vertical feed. BGM drives pacing.

**Agent logic:** Turn a character dialogue or worldview scene into a rhythmic short video script.

**Format:**
- Vertical video script (15-60s)
- Must include: BGM style, subtitle timeline, visual instructions per beat, camera movement
- This is where AI video is the *core content*, not supplementary

**Template:**
```markdown
## TikTok / Douyin Script

**Duration:** [seconds]
**BGM style:** [genre/mood — e.g., "lo-fi ambient", "epic orchestral build", "nostalgic piano"]
**Aspect ratio:** 9:16 (vertical)

### Beat-by-Beat Breakdown

**[0:00-0:03] Hook:**
[What stops the scroll — first 3 seconds are everything]
Video prompt: [...]
Subtitle: [none / one-line hook text]

**[0:03-0:12] Cut 1:**
Video prompt: [full cut prompt]
Subtitle: [character dialogue or narration, timed to action]

**[0:12-0:24] Cut 2:**
Video prompt: [full cut prompt]
Subtitle: [...]

**[final 2s] CTA:**
Subtitle: [engagement hook — "你想先和谁说话？" / "Part 2?"]
```

**Example video styles:**
- 对话实录: Slow typing → character reply → expression change → "wait what" pause → cliffhanger
- 角色 PV: World establishing shot → character close-up → dialogue (TTS/subtitle) → second character → "Who do you want to talk to first?"
- 世界观预告: Epic cinematic, wide→medium→close, world rules revealed through visuals
- ASMR 情绪向: Character soft-light close-up + whispered dialogue (TTS), ambient sound, extreme close-up

---

## 小红书（种草 + 情绪共鸣）

**Emotional register:** 治愈, 陪伴, "被懂的感觉"
**Language:** Chinese
**Tone:** Warm, personal, lifestyle-oriented. Not tech showcase — emotional value and life resonance.

**Agent logic:** Extract the character's warmest or most piercing dialogue fragment. Pair with character art in soft emotional register.

**Format:**
- 3-6 image cards (unified color palette) + heartfelt copy + tags
- OR 15-30s emotional short video (mood piece)
- Image cards should tell a micro-story or build an emotional arc

**Template:**
```markdown
## 小红书笔记

**格式:** [图文卡片 / 情绪短片]
**色调:** [warm/cool/muted — unified across all cards]

### 图文卡片 (3-6张)

**卡片 1 (封面):**
Image prompt: [角色柔光立绘, 治愈向, 小红书风格色调]
文字叠加: [一句话 hook — 情绪共鸣句]

**卡片 2-5:**
Image prompt: [对话截图风格 / 场景插图]
文字叠加: [角色台词或旁白]

**卡片 6 (尾页):**
Image prompt: [角色全身 / 场景远景, 留白]
文字叠加: [CTA 或情感结语]

### 文案
[走心文案, 200-400字, 第一人称或第二人称叙事]

### 标签
#AI角色 #虚拟陪伴 #[世界观标签] #[情绪标签] #治愈 #[角色名]

### 情绪短片 (可选)
Video prompt: [Mood Piece, 1 cut × 12s, 柔光+角色日常动作]
BGM 建议: [温暖/伤感/治愈向]
字幕: [角色台词, 后期叠加]
```

**Example directions:**
- "下班后不想说话的时候，她发来了这条消息" + dialogue screenshot + soft character art
- "我给AI讲了今天被甲方骂的事，她的回复让我哭了"
- Video: 10s soft-light character close-up + dialogue subtitle fading in + fade out

**Key rule:** 小红书 sells emotional value and lifestyle, not tech curiosity.

---

## Discord / 社区播种（深度粘性 + 社群构建）

**Emotional register:** 归属感, 养成感, "我的角色与众不同"
**Language:** English or bilingual
**Tone:** Community-native, conversational, like messages from a character-bot or community manager.

**Agent logic:** Generate character "daily push" content — morning messages, random events, topic reactions. Build parasocial connection through routine.

**Format:**
- Bot message templates / community post drafts / character Q&A material
- Short video teasers for new character launches or world events
- Daily content rotation from character's personality + backstory

**Template:**
```markdown
## Discord 素材

### 日常推送 / Daily Push (5-7 variants)

**早安消息 · Morning Message:**
[角色第一人称, 用角色的说话风格, 从 basics.md 互动风格中提取语气特征]
[每条 50-100 字, 不能太长, bot 消息感]

**随机事件 · Random Event:**
[世界观内的小事件 + 角色反应]
"[角色名在世界里遇到了什么有趣的事，用她的口语风格讲出来]"

**话题反应 · Topic Reaction:**
[如果社区在讨论 X 话题, 角色会怎么回应]

### 新角色预告 / Character Reveal Teaser

Video prompt: [10s — silhouette reveal → character appearance → core line]
Image prompt: [神秘剪影 → 局部特写 → 全貌, 3 张递进式图片]
Community post: "[预热文案, 制造悬念, 引导讨论猜测]"

### Ask Me Anything 素材

[5-8 个预设 Q&A, 问题从角色最有趣的侧面出发, 回答用角色的声音]
```

**Release cadence:**
- Daily: 1 morning message (rotate between 7 variants)
- Weekly: 1 random event + 1 topic reaction
- Event-based: Character reveal teaser series (3 days → silhouette → detail → full reveal)

---

## Cross-Platform Visual Consistency

All platforms share the same visual base but adjust tone:

| Platform | Image Tone | Video Tone |
|---|---|---|
| Reddit | Dark, moody, slightly unsettling | Chat replay, candid feel |
| Twitter | High contrast, eye-catching | Punchy PV, fast cuts |
| TikTok | Dynamic, trend-aligned | Full cinematic, BGM-driven |
| 小红书 | Soft, warm, pastel-adjacent | Mood piece, lo-fi |
| Discord | Clean, character-focused | Teaser, silhouette reveals |

**Shared visual base:** Character appearance tags from `concept-arts.md` stay consistent across all platforms — only lighting, color grading, and composition change to match platform tone.
