# GitHub 短剧 & AI 技能 · 1 小时学习汇报

> 时间：2026-10-11 ｜ 方式：Web 抓取高星标 repo 的 README / SKILL.md 结构与设计说明（本机未装 gh，走只读调研）
> 范围：① 高星标有参考价值 ② 偏向 AI 短剧 ③ 能增强 AI 思考/读取的 skill

---

## 一、一句话结论

GitHub 上 AI 短剧赛道已经**高度同质化**——主流项目几乎都收敛到同一条 7 步流水线，真正的差异在**"资产一致性"和"人类审核闸"**两件事上。而能"让 AI 思考/读取变强"的 skill，本质是一套**上下文工程（Context Engineering）+ 持续自我学习（Continuous Learning）**的方法论。两者可以合成我们自己的 WorkBuddy 短剧 skill 的升级蓝图。

---

## 二、高星标 · 值得学的清单（按参考价值排序）

| 项目 | 星标 | 类型 | 一句话定位 | 为什么值得学 |
|---|---|---|---|---|
| **sickn33/antigravity-awesome-skills** | ~43k | 元技能库 | 2675+ 个 agentic skill 的范文库 | 学"怎么写好 SKILL.md"的最佳语料，含推理/记忆/提示词/元技能全光谱 |
| **Agent-Skills-for-Context-Engineering** | ~16.5k | 思考增强 | 上下文工程+多智能体+记忆系统 | 直击"AI 为什么会忘记/读错"，有 `context-degradation`、`memory-systems` 等可直接抄 |
| **Toonflow-app (HBAI-Ltd)** | ~16k | AI 短剧 | 无限画布 + ScriptAgent/ProductionAgent | 多 Agent 协作 + MCP/插件扩展的工业级范例 |
| **huobao-drama (chatfire-AI)** | ~14–16k | AI 短剧 | 一句话→成品短剧，全栈 TS | 4 个 Mastra agent 分工 + `@character` 引用锚点 + 可在线编辑的 SKILL.md |
| **waoowaoo (saturndec)** | ~14k | AI 短剧 | 好莱坞式全流程可控 | 工业级影视链路，已有人接单盈利 |
| **ViMax (HKUDS)** | ~12.3k | AI 短剧/学术 | 导演+编剧+制片+生成 四智能体 | 8 层多智能体流水线，Idea2Video/Novel2Video/Script2Video/AutoCameo 四种模式 |
| **NarratoAI** | ~11k | 解说视频 | 脚本+剪辑+配音+字幕一条流 | 非短剧但流水线设计可借鉴 |
| **Jellyfish (Forget-C)** | ~6.3k | AI 短剧 | 角色/场景/道具/服装一致性工作台 | **"角色漂移"问题的标准解法**——四大实体共享模型 + 镜头就绪状态机 |
| **dramaclaw** | ~6.6k | AI 短剧 | 小说→分集+成片 | 网关接入、可换任意模型，部署简单 |
| **printfilm (yi1108)** | ~4.8k | AI 短剧 | 结构化 PrintFilmScript | 分镜原子化、资产引用化、模型可配化、流水线回溯化 |
| **Claudeception (blader)** | ~2.4k | 自我学习 | 自动把"踩过的坑"存成新 skill | 持续学习闭环，正好对应"让 AI 越用越聪明" |

> 注：MoneyPrinterTurbo（120k）虽星标最高，但它是"素材库拼短视频"的**上一代思路**，不是 AI 生成镜头，对短剧参考价值有限，仅作背景了解。

---

## 三、AI 短剧方向：我拆出的「通用流水线方法论」（核心学习成果）

几乎所有高星标短剧项目都收敛到这条流水线——这是我们自己 skill 的骨架：

```
输入(创意/小说/剧本)
   ↓
① 剧本理解 / 角色提取   → 拆集、拆场景、抽角色/道具
   ↓
② 分镜拆解             → 每镜 2–5 秒，组 15 秒内段
   ↓
③ 资产锁定(一致性)      → 角色/场景/道具 参考图 + ID 引用  ← 关键护城河
   ↓
④ 提示词生成           → 每镜视频/图像提示(@角色引用)
   ↓
⑤ 图/视频生成          → 多供应商模型(Seedance/Wan/Kling/MiniMax)
   ↓
⑥ FFmpeg 合成          → 拼接+字幕+口播
   ↓
⑦ 审查/质检            → 结构+内容+合规检查
```

**从源码里挖出的 6 个可抄设计原则：**

1. **角色化 Agent 分工**（huobao-drama）
   把生产拆成 `script_rewriter / extractor / storyboard_breaker / prompt_generator` 四个 agent，各管一环，模块化可替换。
2. **资产一致性是第一公民**（Jellyfish / huobao / ViMax）
   Jellyfish 维护 `角色/场景/道具/服装` 四大共享实体模型，跨镜复用；huobao 用 `@character` 锚点自动绑定参考图；ViMax 用 VLM 做一致性校验。**这是"watchable vs publishable"的分水岭。**
3. **镜头就绪状态机**（Jellyfish）
   `script breakdown → shot prep → candidate confirm → ready → generate`：每个镜头有"就绪"标记，未就绪不生成，强制人工审核闸。
4. **模型与提示词解耦**（huobao / printfilm）
   通过环境变量/后台配置 `MODEL_LLM / MODEL_IMAGE / MODEL_VIDEO`，**不改代码换模型**——skill 跨平台适配的关键。
5. **原子化可重试任务**（printfilm）
   每个镜头是独立可重试单元；上游 429/5xx 退避、幂等入账、终态对账——直接借鉴到 skill 的鲁棒性设计。
6. **引用锚点语法 / 多语言 skill 变体**（huobao）
   `@character` 在 prompt 里建立语义链接；`SKILL.<lang>.md` 随语言加载、缺失回退——适合做多语言短剧。

---

## 四、让 AI 思考 / 读取增强的 skill（你的第 3 个方向）

这类 skill 不是"生成内容"，而是**改造 AI 本身的工作方式**，最值得长期吸收：

### A. Agent-Skills-for-Context-Engineering（16.5k）—— 上下文工程
- **核心认知**：上下文窗口的瓶颈不是 token 容量，而是"注意力机制"。上下文越长，模型越容易 **lost-in-the-middle（中间丢失）/ 中毒 / 分心 / 冲突**。
- **可抄的 skills**：`context-fundamentals`（上下文解剖）、`context-degradation`（失败模式诊断）、`context-compression`（长会话压缩）、`multi-agent-patterns`（编排/对等/分层架构）、`memory-systems`（短/长/图记忆）、`bdi-mental-states`（信念-愿望-意图认知建模）。
- **设计哲学**：Progressive Disclosure——启动只加载 name+description（~100 token），命中才读全文。这对我们管理 skill 加载极有用。

### B. antigravity-awesome-skills（43k）—— 推理 / 记忆类精选
- `falsify`：科学推理协议（假设→对抗检验→证据分级→校准结论）
- `dsh-deepread`：证据优先的文章/书/PDF 分析，带 claim 追踪
- `session-handoff` / `recursive-context-pruning-token-budgeting`：上下文交接与令牌预算
- `compile-knowledge`：带出处/过期/秘密边界的持久知识库
- `yao-meta-skill` / `manage-skills`：怎么造 skill、管 skill 的元技能

### C. Claudeception（2.4k）—— 持续自我学习（最贴合"让 AI 越用越强"）
- **机制**：AI 在工作中发现"非显然的解法"时，自动写成新 skill，下次同场景自动加载。
- **质量门**：可复用 / 非显然 / 触发条件具体 / 已验证——不满足就不存。
- **学术根基**：Voyager（技能库自积累）、Reflexion（自我反思）、CASCADE（元技能）。
- **可直接借鉴**：给我们自己的短剧 skill 加一个"踩坑即沉淀"的钩子。

---

## 五、最有价值的「抄作业」短名单（Top 8）

1. **huobao-drama** —— 学 4-agent 分工 + `@character` 锚点（AI 短剧落地范本）
2. **Jellyfish** —— 学"资产一致性 + 镜头就绪状态机"（解决角色漂移）
3. **ViMax** —— 学 8 层多智能体编排 + 四种创作模式（架构天花板）
4. **Agent-Skills-for-Context-Engineering** —— 学上下文工程（防 AI 读错/忘）
5. **Claudeception** —— 学持续自我学习闭环（让 AI 越用越聪明）
6. **drama-skills / shuohao-skills**（awesome-ai-micro-drama 收录）—— 学"11 个 agent 串成短剧流水线"的现成打包
7. **seedance-2.0 / seedance-prompt-skill** —— 学可直接套用的视频提示词模板结构
8. **antigravity-awesome-skills 的 `falsify` + `dsh-deepread`** —— 学"让 AI 推理更严谨、读文档更靠谱"

---

## 六、对我们自己「短剧 skill」的升级建议

把上面所学映射回我们之前写的那份《短剧创作学习清单》，建议补三块：

1. **加一个"资产一致性"模块**：用 角色/场景/道具/服装 四实体 + 参考图 ID 引用，替代现在"凭感觉写提示词"。
2. **加一个"镜头就绪审核闸"**：每集拆镜后强制走 `提取→确认→就绪→生成`，避免"一键出片但不可发"。
3. **加一个"学习沉淀钩子"（Claudeception 思路）**：每次跑通/踩坑都自动存一条可复用 skill，让这个短剧 skill 自我进化。

---

## 七、下一步可以做什么（你挑）

- **A. 直接写**：基于上面方法论，把我们的 WorkBuddy「短剧 skill」升级成一个生产级 SKILL.md（含资产一致性 + 审核闸 + 提示词模板）。
- **B. 抄现成**：我把 `drama-skills` / `shuohao-skills` 的 SKILL.md 拉下来做对照，提炼中文版。
- **C. 装思考增强**：把 `Agent-Skills-for-Context-Engineering` 和 `Claudeception` 的核心理念固化成我们自己的元 skill。
- **D. 跑 Demo**：挑 huobao-drama 或 ViMax，本地/云端跑通一集，验证这套方法论。

> 说明：本次为只读调研（本机未装 `gh`，通过网页抓取 README/SKILL 结构）。若你想真正 clone 下来运行或改造，我可以先装 `gh` + 配凭据，再帮你拉取 Top 3 仓库。
