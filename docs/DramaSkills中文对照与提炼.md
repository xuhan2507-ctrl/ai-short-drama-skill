# Drama Skills 中文对照与提炼

> 来源：`zenstory-ai/drama-skills`（MIT，GitHub 约 1.8k⭐）—— 唯一开源 + 全流程 + Agent Skills 标准的 AI 短剧产线。本文基于**真实仓库**（README / docs / examples）逐条对照，非二手博客。
> 定位：面向 Claude Code / Codex 的 11 个 skill，从点子或原著做到分集剧本、分镜、图片/视频提示词与成片。**文字优先**——每阶段把决策写成 Markdown 落进 `剧集/<EP>/`，媒体生成只在创作者**明确确认预览批次后**才执行。

---

## 一、11 个技能对照表（可直接抄的产线分工）

| 技能（命令） | 英文职责 | 中文职责 | 产物落哪个文件 |
|---|---|---|---|
| `short-drama` | Init / route / Look Dev / Dashboard | 初始化、路由、视觉方向、本地看板 | 项目骨架 + `视觉设定.md` |
| `short-drama-novel-analyze` | Read-only novel analysis | 原著抽样快评、章节索引、改编价值、分集候选 | 分析笔记 |
| `short-drama-develop` | Traceable adaptation plan | 故事引擎、分集地图、导演阐述、钩子手册 | `剧本.md` |
| `short-drama-write` | Shootable per-episode script | 单集因果节拍、可拍剧本 | `剧本.md` |
| `short-drama-assets` | Characters/looks, locations, props, locks | 人物/造型、地点/视图、道具/状态、连续性锁 | `视觉设定.md` |
| `short-drama-image-prompts` | Lookdev + reference-board prompts | 风格帧、角色/场景/道具参考板提示词 | `图片提示词.md` |
| `short-drama-storyboard` | Shot list + frozen keyframes | 镜头表、空间连续、冻结关键帧 | `分镜.md` |
| `short-drama-video-prompts` | Per-shot motion/camera/sound | 单镜动作、表演、摄影、声音、起止状态 | `视频提示词.md` |
| `short-drama-produce` | Run confirmed jobs via adapters | 预览批次 → 明确确认 → 外部 adapter 执行 | 生产结果（不入事实） |
| `short-drama-edit` | In/out points, subtitles, render | 入出点取舍、镜序、字幕、响度、渲染成片 | `剪辑单.md` → 成片 |
| `short-drama-review` | Structural/continuity review | 结构/内容/连续性审查，写结论不替你改 | 审查结论 |

> 路由写法：Claude Code 用 `/short-drama`，Codex 用 `$short-drama`；也可自然语言。

---

## 二、5 份创作事实文件（核心范式 · 我们 SKILL.md 第二节的来源）

**不使用数据库**——每集只维护 5 份 Markdown，改哪份文件就是改哪一层决定：

1. `剧本.md` — 故事发生什么（剧情单元、因果节拍）。
2. `视觉设定.md` — 跨镜保持的造型锁（连续性锁登记表）。
3. `分镜.md` — 每镜起止 + 冻结关键帧 + 镜头表。
4. `图片提示词.md` — 直接喂图模型。
5. `视频提示词.md` — 直接喂视频模型。
6. `剪辑单.md` — 剪成片时多一份，逐刀写画面发生了什么，字幕逐字取自剧本。

**真实样例（仓库 examples/creator-first/EP001/）**：同一镜穿过 4 份文档各管一层——
- `剧本.md` 只写发生了什么：`桌后，周薄森对着屏幕坐得笔直，脸绷着。桌前，江晨双手撑着桌沿，猛地回神。`
- `视觉设定.md` 给造型上「连续性锁」，锁面是能原样贴进提示词的短语：
  `LOCK-JIANGCHEN-DRESS《江晨松枝绿常服》（镜头：SHOT-002、003、007…；图片提示词项：IMG-JIANGCHEN-SHEET）· 锁面：pine-green lapel service jacket`
- `分镜.md` 写起点/终点 + 冻结关键帧（只画起点那一格），锁面原样出现在关键帧正文。
- `视频提示词.md` 只写起点→终点之间模型要执行的动作，整段复制进生成界面即可用。

---

## 三、三层一致性机制（流水线命脉 · 我们 SKILL.md 第三/六节来源）

| 层 | 锁什么 | 怎么衔接 |
|---|---|---|
| **第一层：视觉设定 + 连续性锁** | 区分身份（跨集不变）/ 变体（服装、伤势、时段改变，不是新角色）/ 锁（只锁不随剧情变、观众看得出不一致的可见事实，写成最小名词短语如「松枝绿立领常服」） | `assets` 从剧本拆出，落 `视觉设定.md` |
| **第二层：参考图三状态** | `IMG-*` 只是提示词（图未出）/ `PLAN-*` 创作者自供参考图 / `REF-*` 项目里已存在且已检查过的图。**只有 `REF-*` 能进视频提示词的参考槽**；被锁点名的条目锁面原样出现在参考图提示词正文 | `image-prompts` 写提示词时登记 |
| **第三层：冻结关键帧 + 视觉依据** | 每镜先写「起点→唯一动作→终点」；冻结关键帧只写起点那格能看见的；成稿后反查本格需保持身份/造型/地理的人物地点道具，逐项写进「视觉依据」；关键帧点名条目必须出现在视觉依据里 | `storyboard` 写 `分镜.md`，`video-prompts` 翻译 |

> 边界：以上都是**文本约束**，不承诺零穿帮；价值是让「哪些必须一致」变成可检查事实，让穿帮在出图前可见。

---

## 四、预览确认闸 + 取回机制（我们 SKILL.md 第四节来源）

- 任何图片/视频/TTS/音乐任务，**先在文件看准确内容和参数，明确确认后才调外部接口花钱**；预览、`continue`、预算备注都**不算**确认。
- 视频任务提交即计费：进程断了**不要重投**，跑 `audit` 报成 `orphaned_provider_job`，再用 `collect --job-id <id>` 取回（取回不花钱、不走确认闸）；只有 `collect` 也确认失败才重新确认重投。
- 供应商凭据不进项目文件；适配模型：Seedance / GPT Image 2 / MiniMax H3 视频 / MiniMax Music。

---

## 五、自带核对脚本（机器化一致性 · 我们 SKILL.md 第五节来源）

`creator_markdown_check.py` 核对 5 份文档引用。把样例故意改坏两处（删 SHOT-002 视觉依据里的周薄森、MOTION-003 时长 2s→3s），它报的是**原因**：
```
ERROR: SHOT-EP001-002: 冻结关键帧提示词写到人物「周薄森」，视觉依据没有覆盖；…
ERROR: SHOT-EP001-003: 分镜时长 2 秒与视频提示词 3 秒不一致；视频提示词只能原样照抄已接受的镜头时长
```
`edit_tool.py check` 核对剪辑单（v0.7.1 起「未采用镜头」逐个写镜号+理由）。

---

## 六、对我们短剧 Skill 的提炼结论（一一对应）

| Drama Skills 的真实做法 | 我们 SKILL.md 已落地的章节 |
|---|---|
| 5 份 Markdown 创作事实 | 第二节 |
| 连续性锁短语（LOCK-<角色>-<部件>《中文名》→ 英文锁面） | 第三节 |
| 预览确认闸 + 不重投/取回 | 第四节 |
| 核对脚本硬规则（来源必引视觉设定、时长一致、未采用写理由） | 第五节 |
| Lock-Then-Animate 四机制 + 参考图三状态 | 第六节 |
| 11 路由子流程 | 第七节 |

**可直接补强的点**：① 把 `IMG-/PLAN-/REF-*` 三状态也写进我们 SKILL 的锚点技术；② 加一个轻量「核对脚本」思想（AI 自检引用与时长）；③ 把 MiniMax H3 方言（6 段提示词、4.1 字/秒估时、说话人绑定）列为可选视频模型范例。

---

## 七、注意事项 / 坑

- 依赖 Claude Code / Codex 订阅（核心依赖 Agent 环境）。
- 预览确认比「一键生视频」慢——**这是设计不是 bug**。
- 视频任务提交即计费；H3 中文小样本约 **4.1 字/秒**，分镜先估对白时长再定镜长。
- 说话人绑定：台词期间画里还有别的正脸时，明写那个人闭嘴不说话，避免口型落错脸。
- 静态漫剧（关键帧切换+配音）可跳过视频提示词层。

---

## 八、延伸（同组织相关 skill 包）

`oh-story-claudecode`（网文写作）/ `novel-to-game`（原著改游戏）/ `video-recap-skills`（视频解说）/ `zenstory`（写作工作台）。若要把我们的短剧 Skill 进一步对齐 Agent Skills 规范，可参考本仓库 `skills/` 目录的 SKILL.md 写法。
