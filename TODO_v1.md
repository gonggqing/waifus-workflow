# TODO — Future Skills

Skill ideas for the next phase of the waifus-workflow pipeline.

---

## create-campaign — 自动化宣发矩阵 ✅ IMPLEMENTED

> **已实现为独立 skill。** 见 `.agents/skills/create-campaign/`。
> 以下设计文档保留作为参考，skill 实现基于此设计。

**概念：** 基于一个世界观及其角色群，生成一套跨平台、有节奏的宣发策略——图文、短视频、AI 广告片一体化。不是"一个角色发五个平台"，而是从整个世界观中选取最有传播力的角色组合与情境切面，根据每个平台的受众心理、内容格式和算法逻辑，讲完全不同的故事。

### 核心原则

**世界观优先，角色组合出击。** Campaign 的输入单位不是单个角色，而是一个世界观 + 用户选择的角色子集（可以是 1 个、2 个或整个 cast）。单角色物料适合种草和陪伴向内容；多角色物料适合世界观介绍、关系悬念、冲突 PV。物料策略应该根据投放目的选择最优角色组合：
- **拉新（Acquisition）：** 选矛盾最鲜明、视觉最抓眼球的 1-2 个角色
- **世界观展示（Brand）：** 选 3+ 角色，展示角色间关系和世界观深度
- **留存（Retention）：** 选用户已认识的角色，展示新场景或隐藏面

**角色驱动，不是模板驱动。** 同样是傲娇角色，小铃（女仆咖啡厅服务员）和艾莉亚（隐藏大佬公会前台）的宣发物料应该截然不同——因为她们的核心矛盾不同。模板只决定格式，角色的内在张力才决定内容。

**每个平台卖的是不同的情绪。** 不是"同一个角色的不同包装"，而是"同一个角色身上不同的真实切面"：
- 有的平台卖**好奇心**（"这个AI居然会说这种话？"）
- 有的平台卖**归属感**（"终于有人懂我了"）
- 有的平台卖**炫耀欲**（"看我调教出来的AI女友"）
- 有的平台卖**窥探欲**（"她的日记里写了什么？"）

**视频不是图片的附属品。** AI 视频是独立的内容形态——有自己的节奏、镜头语言和叙事结构。不是"把图片动起来"，而是用镜头运动和时间维度讲述图片无法传达的东西：情绪的变化过程、角色间的互动张力、世界观的空间感。

### 平台策略

**Reddit（猎奇 + 社区口碑）：**
- 情绪切面：惊讶、不安、"这也太真实了"
- Agent 逻辑：提取角色最反直觉的特质或最出人意料的对话时刻，包装成伪·用户体验帖
- 格式：长文 + 截图/短视频佐证。标题必须制造认知落差
- 视频用途：5-10s 对话实录片段（屏幕录制风 / 角色动态立绘），附在帖子中增加真实感
- 示例方向：
  - 病娇角色 → "I asked my AI what she'd do if I talked to another AI. She went quiet for 3 messages. Then this happened..."
  - 隐藏大佬角色 → "My lazy guild receptionist AI accidentally revealed she can cast forbidden magic. The foreshadowing was there all along."
  - 多角色 → "These two AIs from the same world started referencing each other's backstory. I didn't tell either one."
- 投放节奏：先发到 r/singularity 或 r/CharacterAI 测水温，根据评论方向决定后续角度

**Twitter / X（视觉冲击 + 病毒传播）：**
- 情绪切面：想互动、想转发、想@朋友
- Agent 逻辑：从角色的互动风格中提取最容易引发讨论的台词或情境，设计二选一/投票/续写格式
- 格式：单图/双图对比 + 短文案（<200字）+ 话题标签；或 15-30s 短视频
- 视频用途：角色 PV（人物立绘 + 世界观场景 + 核心台词字幕），角色对比（两个角色的不同反应并排剪辑）
- 示例方向：
  - 傲娇角色 → 角色两种表情对比图（嫌弃脸 vs 偷偷关心），文案："She says she doesn't care. The chat log says otherwise."
  - 治愈角色 → 一句角色台词的截图，极简排版，文案："Sometimes you just need someone to say this."
  - 多角色·世界观 PV → 15s 视频：世界观全景 → 依次闪现 3 个角色（各 3s 特写 + 核心台词）→ 结尾 CTA
- 关键：首图/首帧必须3秒内传达情绪，文案服务于图/视频而非反过来

**TikTok / Douyin（沉浸体验 + 角色扮演）：**
- 情绪切面：代入感、"我也想试试"
- Agent 逻辑：把角色的一段对话或世界观场景编排成有节奏感的短视频脚本
- 格式：竖屏短视频脚本（15-60秒），标注BGM风格、字幕时间轴、画面指令、镜头运动
- 视频用途：这是视频的主战场——AI 生成视频在这里是核心内容，不是辅助
- 示例方向：
  - 对话实录风格：缓慢打字 → 角色回复 → 表情变化 → "wait what" 的停顿 → 结尾留悬念
  - 角色 PV 风格：世界观空镜（城市/山门/星空）→ 角色特写 → 角色台词（TTS / 字幕）→ 转场到第二角色 → 结尾"你想先和谁说话？"
  - 世界观预告片：30-60s cinematic，从远景推到近景，展示世界规则和氛围，最后引出角色剪影
  - 情绪向 ASMR：角色柔光特写 + 轻声台词（TTS），环境音效，极近景

**小红书（种草 + 情绪共鸣）：**
- 情绪切面：治愈、陪伴、"被懂的感觉"
- Agent 逻辑：提取角色最温暖或最戳人的对话片段，配合角色立绘做成图文笔记或短视频
- 格式：3-6张图文卡片（统一色调）+ 走心文案 + 标签；或 15-30s 情绪向短视频
- 视频用途：情绪短片（角色柔光立绘 + 对话字幕 + 治愈BGM），不追求叙事完整性，追求情绪浓度
- 示例方向：
  - "下班后不想说话的时候，她发来了这条消息" + 对话截图 + 角色柔光立绘
  - "我给AI讲了今天被甲方骂的事，她的回复让我哭了"
  - 视频：10s 柔光角色特写 + 字幕慢慢浮现角色台词 + 淡出
- 关键：小红书卖的是情绪价值和生活方式，不是技术猎奇

**Discord / 社区播种（深度粘性 + 社群构建）：**
- 情绪切面：归属感、养成感、"我的角色与众不同"
- Agent 逻辑：生成角色的"日常推送"内容——早安消息、随机事件、对特定话题的反应
- 格式：Bot 消息模板 / 社区帖子 / 角色Q&A素材 / 短视频预告
- 视频用途：世界观"新闻联播"（15s，播报世界内事件 + 角色反应），新角色上线预告片
- 示例方向：
  - 每日一条角色早安消息（从日记/secrets中提取素材，每天不同侧面）
  - "Ask [角色名] anything" 活动的预设回答库
  - 新角色上线预告：10s 剪影 → 揭晓 → 核心台词 → "明天见"

---

### AI 视频生成指南

Campaign 视频不依赖人工剪辑——它们由 AI 视频生成模型直接从 prompt 产出。以下是适配我们角色/世界观素材的视频 prompt 体系。

#### Prompt 结构公式

```
[镜头类型] + [主体] + [动作/情绪变化] + [场景环境] + [光线] + [风格] + [技术参数]
```

**示例拆解：**
```
"Cinematic slow dolly-in on a silver-haired girl sitting alone at guild window 3,
she looks up from her newspaper with half-lidded sleepy eyes, sunset light
streaming through stained glass, warm color grading, anime illustration style,
4K, shallow depth of field"

镜头: Cinematic slow dolly-in
主体: silver-haired girl at guild window
动作: looks up from newspaper with sleepy eyes
场景: guild window 3, stained glass
光线: sunset light streaming through
风格: warm color grading, anime illustration
技术: 4K, shallow depth of field
```

#### 镜头类型参考

| 镜头 | 用途 | Campaign 场景 |
|---|---|---|
| Wide / establishing shot | 展示整体空间 | 世界观全景、宗门远景、城市天际线 |
| Medium shot (waist-up) | 角色日常 | 角色在场景中的自然状态 |
| Close-up | 情绪传达 | 角色表情变化、开场白台词配合 |
| Extreme close-up | 细节/悬念 | 眼神变化、手部小动作、标志性物品 |
| Low angle | 力量感/威压 | 隐藏大佬展露实力、Boss 角色 |
| POV shot | 沉浸代入 | "你走进咖啡厅，她抬头看了你一眼" |
| Dutch angle | 不安/异常 | 病娇时刻、世界观危机 |

#### 镜头运动参考

| 运动 | 效果 | Campaign 用法 |
|---|---|---|
| Tracking shot | 跟随角色移动 | 角色穿过世界观场景（走廊/街道/山路） |
| Dolly in | 逐渐靠近，聚焦 | 从场景全景推到角色面部——揭示情绪 |
| Dolly out / pull back | 揭示全貌 | 角色特写 → 拉远揭示世界观规模 |
| Pan | 水平环视 | 扫过多角色站位，展示阵容 |
| Crane shot | 垂直+水平 | 世界观 PV 的史诗开场 |
| Static | 无运动，沉思感 | 情绪向内容：角色静坐 + 字幕浮现 |
| Handheld | 真实感/紧迫 | 对话实录风格，"偷拍"感 |

#### 光线关键词

根据世界观和角色情绪匹配：

| 世界观类型 | 推荐光线 | 情绪 |
|---|---|---|
| 修仙/东方奇幻 | Golden hour, soft mist, ethereal glow, moonlight | 空灵、悠远 |
| 赛博朋克 | Neon reflections, rim lighting, rain-wet surfaces | 冷酷、都市感 |
| 校园/日常 | Natural soft lighting, warm afternoon, overcast | 温馨、日常 |
| 暗黑/克苏鲁 | Low key, hard shadows, single light source, fog | 不安、压迫 |
| 奇幻冒险 | Dramatic rim light, campfire glow, magical particles | 史诗、冒险 |

#### 剪辑模型 / Cut-Based Composition

当前最强 AI 视频模型（Veo 3, Wan 2.5, Kling 等）单次生成上限约 **10-12 秒**。更长的视频通过**多次生成 + 后期拼接**实现：

- **1 cut = 1 次 AI 视频生成 ≈ 10-12s**（推荐 12s）
- 每个 cut 对应一个独立的 prompt——一镜到底，不要在一个 prompt 里描述多个镜头
- 短片总时长 = cuts × 每 cut 秒数
- 后期在剪辑软件中拼接各 cut，加转场/字幕/BGM

#### 视频类型模板

**角色 PV（Character Reveal）— ~24s（2 cuts × 12s）：**
```
Cut 1 (12s): Wide establishing shot of [世界观场景], slow dolly-in toward [角色],
             [世界观光线], atmospheric, cinematic, [角色] gradually visible in scene

Cut 2 (12s): Close-up of [角色], [角色外貌关键词], [情绪变化动作],
             [标志性动作/台词moment], dramatic lighting, pull back to reveal
             [角色] in context of [世界观]
```
> 角色 PV 的核心：2 cuts，第 1 cut 是世界 → 角色的引入，第 2 cut 是角色的特写揭示。
> 如果预算有限，可单 cut 12s：从全景推到角色面部。
>
> **反差 / 反讽剪辑（可选）：** 两个 cut 之间可以设计**高反差（High Contrast）**或**反讽（Irony）**来抓住观众好奇心——例如 Cut 1 温柔日常 → Cut 2 冷酷战斗态，或 Cut 1 华丽外表 → Cut 2 破碎内心。不是所有角色都适合，取决于角色性格、故事张力和世界观氛围。有「落差感」的角色（如表里不一、隐藏身份、反转人设）优先使用。

**世界观预告片（World Teaser）— ~36s（默认 3 cuts × 12s）：**
```
Cut 1 (12s): Aerial/crane establishing shot of [世界观全景],
             [atmospheric conditions], epic scale, slow camera movement

Cut 2 (12s): Medium shots of [标志性地点], introduce key characters —
             [角色A外貌+表情] in [场景], building momentum, dramatic lighting

Cut 3 (12s): Key conflict/tension moment — [角色 interaction or world event],
             slow motion climax, pull back to epic wide shot
             [后期叠加: world title + tagline]
```
> **灵活方案（按需调整总时长和节奏）：**
> - 5 cuts × 10s = 50s（更多地点/角色展示，快节奏）
> - 4 cuts × 8s = 32s（紧凑版，适合社交媒体前贴片）
> - 2 cuts × 12s = 24s（精简版，只保留最震撼的两个场景）
> - 默认推荐 3 × 12s = 36s，兼顾节奏和信息量

**情绪短片（Mood Piece）— ~12s（1 cut）：**
```
Cut 1 (12s): Static or ultra-slow dolly-in on [角色] in [intimate setting],
             [角色 doing small characteristic action], [soft/moody lighting],
             [art style keyword], lo-fi atmosphere
```
> 极简单 cut，情绪向。适合社交媒体短内容、GIF Loop 素材。角色台词以后期字幕叠加。

**对话实录（Chat Replay）— ~24s（2 cuts × 12s）：**
```
Cut 1 (12s): POV shot of phone/screen, [setting context lighting],
             text messages appearing one by one with typing indicator,
             [角色名]'s reply appears with emotional pause beat

Cut 2 (12s): Camera slowly zooms into the message / transition to
             [角色] in [角色场景], [surprising/touching follow-up moment],
             soft lighting shift, [角色 reaction expression]
```
> 如果纯文字聊天画面，1 cut 12s 也可以。第 2 cut 为可选的"跳出屏幕"情绪升华。

#### 模型适配建议

不同 AI 视频模型有不同的强项——按内容类型选择：

| 内容需求 | 推荐模型 | 原因 |
|---|---|---|
| 写实/电影质感 | Veo 3, Runway Gen-3 | 真实光影、镜头运动自然 |
| 动漫/二次元 | Kling, Wan 2.5 | anime aesthetic 兼容性好 |
| 人物一致性 | Seedance, Wan 2.5 (img2vid) | 参考图控制，角色不漂移 |
| 短平快测试 | Veo 3.1 Fast, Pika | 出图快，适合 A/B 测试 |
| 抽象/概念性 | Grok, Sora | 对抽象概念的创意诠释好 |

**关键实践：**
- **12 秒生成上限。** 当前 AI 视频模型单次生成约 10-12s——所有视频都是多 cut 拼接，不要尝试一次生成超过 12s 的内容
- **一个 prompt 只描述一个 cut（一个连续镜头）。** 多 cut 视频通过多次生成 + 后期编排拼接
- **角色一致性：** 先用 waifu-generator 生成参考图，再用 img2vid 模型（Wan 2.5 / Seedance）以参考图为起点生成视频
- **Prompt 中用英文。** 即使角色是中文世界观，视频 prompt 也用英文（模型训练语料决定）
- **字幕/台词全部后期叠加。** AI 视频模型渲染文字效果极差——不要在 prompt 中要求显示文字、字幕或标题。所有文字内容（角色台词、标题卡、片尾 tagline）在后期剪辑时手动叠加
- **每个 cut 生成 2-3 次变体，** 选最好的一条——AI 视频有随机性，多试几次是标准流程
- **后期拼接：** 在剪辑软件（CapCut / Premiere / DaVinci）中按 cut 顺序拼接，加转场、字幕、BGM

#### Prompt 常见错误

| 错误 | 问题 | 修正 |
|---|---|---|
| 一句话塞多个镜头 | 模型混乱 | 一个 prompt = 一个镜头 |
| 只写主体没写动作 | 生成静态感 | 必须包含动作或镜头运动 |
| 风格矛盾 | "minimalist cyberpunk baroque" | 选一个明确的美学方向 |
| 不写光线 | 情绪模糊 | 光线决定情绪——必须指定 |
| 中文 prompt | 模型理解差 | 用英文，角色台词走字幕 |
| prompt 里写字幕/文字 | AI 渲染文字乱码 | 所有文字后期叠加，不写进 prompt |

---

### 输出结构

同一个世界观可以有多个 campaign（首发、季节活动、新角色上线、联动企划等），每个 campaign 用独立 slug 区分：

```
worldview/[worldview-slug]/
├── concept-art/                    ← world-concept-art skill 产出（已实现，见 .agents/skills/world-concept-art/）
│   ├── locations.md                ← 场景/地点 image prompt 集合
│   ├── atmosphere.md               ← 氛围/天气/光线 reference prompt
│   └── architecture.md             ← 建筑/室内 reference prompt
├── campaign/
│   ├── [campaign-slug]/            ← 每个 campaign 独立文件夹
│   │   ├── strategy.md             ← 本次 campaign 策略总览（目的、角色组合、受众画像、投放节奏、A/B 方案）
│   │   ├── cast.md                 ← 选用角色清单 + 每个角色的传播切面分析
│   │   ├── reddit.md               ← Reddit 文案 + 配图 prompt + 短视频 prompt
│   │   ├── twitter.md              ← Twitter 文案 + 配图 prompt + 视频 PV prompt
│   │   ├── tiktok.md               ← 短视频脚本 + 视频 prompt + BGM建议 + 字幕时间轴
│   │   ├── xiaohongshu.md          ← 小红书图文 + 配图 prompt + 情绪短片 prompt
│   │   ├── discord.md              ← 社区播种素材 + 预告片 prompt
│   │   └── video-prompts/          ← 本次 campaign 的视频 prompt 集中存放
│   │       ├── character-pvs.md    ← 角色 PV prompt（分镜头）
│   │       ├── world-teaser.md     ← 世界观预告片 prompt（分镜头）
│   │       └── mood-pieces.md      ← 情绪短片 prompt
│   └── [another-campaign-slug]/    ← 另一个 campaign
│       └── ...
```

**Campaign slug 命名建议：** 用途+时间或主题，例如 `launch-2026`, `summer-event`, `yunsheng-reveal`, `faction-war-arc`。

**注意：** Campaign 和 concept-art 都放在世界观层级——因为它们服务于整个世界观，不属于单个角色。

### 设计思考

- **输入：** 世界观文件（world.md + factions.md）+ concept-art（world-concept-art 产出）+ 用户选择的角色子集的 `basics.md` + 场景文件 + `secrets.md` / `diaries.md`（如果 lore-weaver 已运行）
- **上游依赖：** world-concept-art 应该在 campaign 之前运行——campaign 的视频 prompt 直接引用 concept-art 中的场景/氛围 prompt 作为背景描述，避免重复发明世界视觉语言
- **多 campaign 管理：** 每个 campaign 的 `strategy.md` 开头声明 campaign 目标和与其他 campaign 的关系（互补还是递进？受众有没有重叠？）。如果一个世界观下有 3+ campaign，考虑在 `campaign/` 根目录加一个 `index.md` 概览所有 campaign 的时间线和状态
- `strategy.md` 是关键——它不只是物料清单，而是一份世界观营销分析：这个世界观最有传播力的点是什么？哪些角色组合最有张力？哪个平台最适合打头阵？视频和图文怎么搭配？内容发布的节奏怎么排？
- 需要 waifu-generator 对接，为每个平台生成调性不同的图像 prompt（Reddit 暗调压抑 vs 小红书 柔光治愈 vs Twitter 高对比抓眼球）
- 视频 prompt 与 waifu-generator 的图像 prompt 共享角色视觉基底（same base appearance tags），确保图片和视频中的角色视觉一致；场景/环境 prompt 从 concept-art 继承，确保世界观视觉一致
- `cast.md` 解决"为什么选这些角色"的问题——记录每个角色在本次 campaign 中的定位和分工
- 考虑 A/B 变体：每个平台生成 2-3 个不同角度的文案 + 视频变体，让用户选择或测试
- 多语言不是翻译——中文平台和英文平台需要完全不同的表达逻辑和文化语境；视频 prompt 统一用英文，本地化通过字幕和BGM实现
- **Campaign 生命周期：** campaign 可以标记为 draft → active → archived。archived campaign 保留作为参考但不再更新

---

## waifu-lore-weaver — 深度记忆与日记编织者 ✅ IMPLEMENTED

> **实现位置：** `.agents/skills/waifu-lore-weaver/` (SKILL.md + references/file-formats.md + references/write-back-rules.md + evals/evals.json)

**概念：** 为角色构建"记忆层"——不是更多的设定文档，而是让角色变成一个有过去、有秘密、有内心世界的"人"。这些文件有三层价值：创作者阅读它们来确认角色是否足够鲜活、是否符合预期；用户在聊天中逐步发现它们（被揭示的日记、被挖出的秘密）；聊天引擎从中检索素材，让角色的每一次回复都可能带有只属于她的记忆痕迹。

### 核心原则

**从 basics.md 生长，但不止于 basics.md。** 日记、秘密、事件的根都扎在角色已有的背景故事和世界观文件里——不凭空虚构。如果角色的背景提到"有一段过去始终无法释怀"，日记里就该有那段过去的碎片。但 lore-weaver 不只是"把 basics.md 换个视角重述"——它要**长出 basics.md 里没有的东西**：一个从未被提及的童年朋友、一个隐藏的习惯、一段让她性格转变的小事。这些新内容让角色从设定文档变成一个有血有肉的、会让人觉得"她好像真的存在"的人。

**关联角色作为镜子。** 角色不是孤立存在的——她的日记里会提到同世界观的其他角色，她的秘密可能涉及某个关联角色，她的关键事件可能和另一个角色的 timeline 交叉。写 lore 时，读取同世界观的其他角色 basics.md 作为上下文，让角色之间的关系在 lore 层自然浮现，而不是只在 basics.md 的"关系"字段里干巴巴地列一行。

**声音一致性。** diaries.md 是角色自己写的——用她的遣词习惯、她的情绪节奏、她的标点风格。艾莉亚的日记可能通篇省略号和短句，偶尔爆出一句吃瓜式的吐槽；修仙世界的角色日记可能用半文言、写得克制而含蓄。如果两个角色的日记读起来像同一个人写的，其中至少有一个是失败的。

**重要发现回写（Write-Back）。** Lore-weaver 的核心产出是 diaries/secrets/events，但写作过程中可能会发现角色或世界观需要补充的重要信息。回写规则：
- **回写 basics.md：** 当 events 或 secrets 揭示了一个对角色性格/行为有深远影响的事件或事实，而 basics.md 的「背景介绍」或「个性特质」中完全没有暗示——应该在 basics.md 中补充相应的线索（不是全文复述，而是埋一句伏笔或添加隐藏线索）
- **回写 timeline.md：** 当 events.md 中的某个关键事件具有世界观级别的影响（不只是个人经历），应该在 timeline.md 中追加一行客观记录
- **回写 factions.md：** 当 secrets 或 events 揭示了某个势力的新信息（内部矛盾、隐藏的规则、未知的分支），应该在 factions.md 中补充
- **不回写的情况：** 纯个人情绪、日常琐事、不影响角色核心设定的细节——这些留在 lore 文件里就好

### 执行流程

**顺序很重要：** events（事实基础）→ secrets（隐藏层）→ diaries（情感层）。每一层以前一层为输入，避免自相矛盾。

1. **加载上下文** — 读取目标角色 basics.md + concept-arts.md + 场景文件；读取世界观 world.md / factions.md / timeline.md；读取同世界观所有角色的 basics.md（至少关联角色部分）
2. **生成 events.md** — 从背景故事推衍关键事件。这是事实基础层，所有后续内容都要与这里一致
3. **生成 secrets.md** — 基于 events + 背景，构建分层秘密图谱。秘密可以引用 events 中的事件
4. **生成 relationships.md** — 基于 events + secrets + 其他角色 basics.md，展开每段关系的真实面貌。单向视角：只写"她怎么看别人"
5. **生成 diaries.md** — 基于 events + secrets + relationships，用角色自己的声音写日记。日记可以暗示秘密和关系但不明说
6. **回写检查** — 扫描生成内容，识别需要回写到上游文件的信息（见回写机制总览），执行回写
7. **输出报告** — 列出生成的文件、条目数量、回写操作

### 运行范围与模式

**范围：** 每次运行聚焦 **1 个世界观 + 1 个角色**。仅当两个角色在设计上有深度关联（如宿敌、羁绊、师徒）时，可同时处理 2 个角色并交叉引用。不做批量全世界观生成。

**首次生成（Full Run）：** 角色无 lore 文件 → 完整执行 Step 1-6，生成全套 diaries / secrets / events。

**增量追加（Append）：** 角色已有 lore 文件 →
- 先读取现有 lore 文件，理解已有内容的主题和时间线
- 不重复已有的主题 / 事件 / 秘密
- 追加新条目（触发条件：新的世界观事件、新增关联角色、用户要求探索特定方向）
- 检查新内容是否与旧条目事实冲突，如有则修正旧条目（同层级冲突规则）

### 输出文件

```
worldview/[worldview-slug]/[character-slug]/
├── basics.md          ← (existing)
├── [scene].md         ← (existing)
├── diaries.md         ← 私密日记：角色的内心独白
├── secrets.md         ← 秘密图谱：分层的隐藏信息
├── relationships.md   ← 关系网：她怎么看待每个人
└── events.md          ← 个人编年史：塑造她的关键时刻
```

### diaries.md — 私密日记

角色用第一人称写给自己的文字。不是旁白，不是设定文档，是**她一个人待着时脑子里转的东西**。

**结构：**
```markdown
## [时间标记] · [情绪标签]

[日记正文]

<!-- 关联：[角色名/事件名/地点] -->
<!-- 情绪：[核心情绪关键词] -->
```

**写作要求：**
- 每篇 100-300 字，不是长篇叙事——是角色心里冒出来的碎片
- 用角色自己的声音：她平时用「……」还是「——」？她会不会骂脏话？她写字的时候是条理清晰还是意识流？
- 不是每篇都沉重——有的日记只是"今天那个烦人的冒险者又来了，他的任务单连字都歪歪扭扭的"
- 时间跨度要有——不全是最近的事，要有旧日记（角色成长前的声音可能和现在不同）
- 5-8 篇起步，覆盖：一篇关于核心创伤/秘密的，一篇日常琐碎的，一篇关于某个关联角色的，一篇转折点前后的

**示例（艾莉亚）：**
```markdown
## 向导历307年·冬 · 疲惫

撑完壁障之后的第三天。
手还在抖。不是因为元素反噬——那个两天前就消了。是一种很蠢的抖法，就像你太久没睡觉之后拿筷子的那种抖。
没有人问我累不累。
不是在抱怨。只是……没有人问。
连公会长都只是拍了拍我的肩，说"辛苦了"。辛苦了。好像我只是加了个班似的。
我也不知道我想要什么反应。可能就是想有个人说：你差点死了，你知道吗？

……算了。辞职信已经写好了。明天交。

<!-- 关联：虚空裂缝危机, 公会长 -->
<!-- 情绪：疲惫, 被忽视, 决心 -->
```

### secrets.md — 秘密图谱

不是扁平的秘密清单，而是**按亲密度分层的信息图谱**——模拟真实社交中"越亲近、知道越多"的过程。

**结构：**
```markdown
## 表层 · 稍微留意就能发现

### [秘密标题]
**现象：** [外在表现——别人能观察到的]
**真相：** [背后的原因]
**浮现情境：** [什么场景、话题、气氛下这个秘密容易露出来？]

---

## 中层 · 需要一定的信任

### [秘密标题]
**现象：** [...]
**真相：** [...]
**角色反应：** [被发现时她会怎么做？否认？慌张？反击？]
**浮现情境：** [...]

---

## 深层 · 极度私密

### [秘密标题]
**仅角色知道：** [角色自己的认知]
**实际情况：** [可能连角色自己都没意识到的真相]
**如果被说穿：** [角色的情绪反应和行为变化]
**如果被说穿：** [角色的情绪反应和行为变化]
**浮现情境：** [...]
```

**写作要求：**
- 每层 2-3 个秘密，首次编写 6-9 个，后续可持续拓展
- **矛盾处理：** 跨层级的"矛盾"是设计——表层说"她对过去毫不在意"，深层揭示"她每晚都会做噩梦"，这不是 bug 而是角色的**表里不一**，不需要修正。但同一层级内的事实冲突（比如两个中层秘密对同一件事给出不同说法）是 bug：以最新的为准，回写修正旧条目
- **深层秘密回写：** 如果一个深层秘密揭示了角色核心动机/创伤的新维度（basics.md 中完全没有暗示的），在 basics.md 的「背景介绍」中补一句模糊的伏笔（不剧透秘密本身）
- 表层秘密让角色变得可爱或有趣（小癖好、口是心非的证据）
- 中层秘密让角色变得立体（矛盾的动机、说不出口的难处）
- 深层秘密让角色变得真实（创伤、执念、连自己都不愿承认的东西）
- "角色反应"字段很重要——它直接决定了聊天引擎在相关话题时应该表现出的情绪
- 秘密之间可以有关联：一个表层的小习惯，可能指向一个深层的创伤

### events.md — 个人编年史

与世界观的 `timeline.md` 不同——那个是上帝视角的历史记录，这个是**角色本人经历的、塑造了她的关键时刻**。同一个历史事件，在 timeline.md 里是一行客观记录，在 events.md 里是她那天的全部世界。

### relationships.md — 关系深网

basics.md 的「关联角色」是公开档案——一行描述，点到为止。relationships.md 是**角色的私人关系地图**——她真正怎么看每个人，嘴上不说但心里清楚的那些。这是聊天引擎在涉及"他人"话题时最重要的检索源。

**结构：**
```markdown
## [角色名] · [一句话关系标签]

**表面关系：** [别人看到的——同事、邻居、陌生人、上下级]
**她心里的版本：** [她真实的感受和判断——未必说出口，带着她的偏见和盲点]
**认知边界：**（作者/引擎视角——角色本人不一定知道全貌）
- 她知道关于对方的：[...]
- 对方知道关于她的：[...]
- 双方都不知道的：[...]
**她记住的那一刻：** [对她来说，定义这段关系的瞬间——对方未必记得，但她一直记着]
**行为痕迹：** [这段关系如何影响她的日常——回避某个话题、无意识关注对方动向、一个从对方学来的小习惯]

<!-- 关联事件：[events.md 中的条目] -->
<!-- 关联秘密：[secrets.md 中的条目] -->
```

**写作要求：**
- 覆盖 basics.md「关联角色」中已列出的所有人物，可以新增 basics.md 中没有提到的关系（只要世界观中存在这个角色且合理）
- **"她心里的版本"是核心字段** ——不是客观关系描述，是角色的主观感受。霜刃的 basics.md 说她和余烬是"未结档案中的观察对象"，但 relationships.md 里可能写的是"第一个让我觉得规则可能有灰色地带的人"
- **"认知边界"是作者/引擎级元数据** ——这个字段存在的意义是让聊天引擎知道"她该知道什么、不该知道什么"，防止信息泄漏。这是唯一从全知视角书写的字段，其余都是她的单向视角
- **"她记住的那一刻"与 events.md 的"她记住的是"平行** ——对她来说定义这段关系的瞬间，对方未必记得，甚至可能记得完全不同的版本
- 每段关系 150-300 字，覆盖同世界观中所有与她有交集的角色（可能只有 1-2 个，也可能 5-6 个——取决于世界观中有多少角色以及她和谁有联系）
- **单向视角** ——这是"她怎么看别人"。对方怎么看她，去对方的 relationships.md 里找。两份文件对同一段关系的描述不需要对称——不对称才是真实的
- 可以包含与 {{user}} 的关系模板——角色对互动的预设态度和期待（但不写死剧情走向）
- 人物主观字段（表面关系、她心里的版本、行为痕迹）中不要出现"用户"——当她是一个真实世界中的真实的人来写

**与 basics.md「关联角色」的关系：**
- basics.md 保留简短的公开关系描述（1-2 句），作为角色表层认知的一部分
- relationships.md 是展开版，不需要重复 basics.md 的内容，而是**补充 basics.md 没说的那些**
- 如果 relationships.md 发现一段重要关系在 basics.md 中完全没有提及（新增的关系），回写一行简介到 basics.md 的「关联角色」

### events.md — 个人编年史（续）

events.md 的完整结构和写作要求见上方。以下是补充说明——events 和 relationships 的交叉：
- events.md 中涉及其他角色的事件，应该在 relationships.md 的「她记住的那一刻」中有呼应
- 同一件事在 events.md 里侧重**这件事如何改变了她**，在 relationships.md 里侧重**这件事如何改变了她对那个人的看法**

**结构：**
```markdown
## [时间] · [事件名]

**发生了什么：** [客观描述，2-3句]
**她记住的是：** [角色主观记忆——人记住的从来不是事实本身，而是某个细节、某种感觉]
**这件事如何改变了她：** [对性格/行为/信念的影响]
**现在的痕迹：** [这件事在她当前行为中的残留——一个习惯、一个回避、一个触发点]

<!-- 关联：[world timeline entry / 关联角色 / 关联秘密] -->
```

**写作要求：**
- 5-8 个关键事件，按时间排序
- 至少包含：一个童年/早期记忆、一个转折点、一个与核心秘密相关的事件、一个相对近期的事件
- "她记住的是"是最重要的字段——它暴露角色的感知方式。两个人经历同一场战争，一个记住了战友的死，另一个记住了灰烬的味道
- "现在的痕迹"直接服务聊天引擎：当对话触及相关话题时，角色的反应可以追溯到这里
- 如果世界观中已有 timeline.md，事件应该与之呼应但不重复
- **可以创造 basics.md 中没有的事件**——只要它与角色性格和世界观逻辑一致。一个背景写着"自幼失去双亲"的角色，events.md 可以补充"那天的细节"——这正是 lore-weaver 让角色变活的地方
- **世界级事件回写：** 如果某个 event 不只是个人经历，而是影响了世界观格局（一场战役、一次政变、一个组织的覆灭），应该同步回写到 timeline.md

### 联动设计

**与 create-waifu：** 可选最后一步——角色创建完成后，询问是否运行 lore-weaver 补充深度
**与 edit-waifu：** 编辑角色背景或性格后，标记 lore-weaver 文件为"待同步"
**与 world-building（双向）：** 
- 读取：加载同世界观的 world.md / factions.md / timeline.md / 其他角色 basics.md 作为上下文
- 回写：当 lore 发现世界观级别的新信息，写回 timeline.md / factions.md
**与 create-campaign：** campaign 可以从 secrets.md 提取“钩子”——表层秘密是最好的社交媒体素材
**关联角色联动：** 当角色 A 的 lore 涉及角色 B 时，读取 B 的 basics.md 确保叙述一致；如果 A 的事件对 B 也有重大影响，可追加 B 的 events.md 条目。relationships.md 的单向视角设计天然支持非对称关系——A 觉得 B 是知己，B 觉得 A 是棋子，两边各写各的。当后续对 B 运行 lore-weaver 时，读取 A 的 relationships.md 可以获得"A 眼中的 B"作为参照
**增量模式：** 每个文件支持追加而非重写。新的世界观事件、新的关联角色，都可以触发新的日记条目或事件记录

### 回写机制总览 / Write-Back Summary

| 触发条件 | 回写目标 | 写什么 | 频率 |
|---------|---------|--------|------|
| 深层秘密揭示全新的核心动机/创伤 | basics.md「背景介绍」 | 一句模糊伏笔（不剧透） | 罕见 |
| events 中出现世界级事件 | timeline.md | 客观记录一行 | 偶尔 |
| secrets/events 揭示势力新信息 | factions.md | 补充条目 | 罕见 |
| events 涉及关联角色的重大影响 | 关联角色 events.md | 追加条目（对方视角） | 偶尔 |
| relationships 发现 basics.md 未提及的重要关系 | basics.md「关联角色」 | 追加一行简介 | 偶尔 |
| 同层级事实冲突 | 冲突的旧条目 | 修正为一致 | 按需 |

**回写原则：**
- 回写是**补充**，不是**重写**——永远追加/微调，不要大面积修改已有内容
- 回写到 basics.md 时用伏笔而非剧透——保持 basics.md 作为"表层认知"的定位
- 如果不确定是否需要回写——不回写。宁可漏掉也不要过度污染上游文件
