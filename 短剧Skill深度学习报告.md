# 短剧 Skill 深度学习报告

> 调研目标：研究 GitHub 上成熟的 AI 短剧生产 skill，提炼"流水线化 + 高星标"的方法论，为自建一套高星标短剧流水线 skill 提供蓝图。
> 调研时间：2026-10-12
> 调研方式：克隆/解析仓库结构 + 通读 SKILL.md、提示词库、README、质量门脚本设计

---

## 一、研究概览（Executive Summary）

我深度研究了 3 个最具代表性的开源短剧 skill 项目，覆盖"skill 形态"下的三种不同打法：

| 项目 | 星标 | Fork | 形态 | 定位 | 语言/协议 |
|---|---|---|---|---|---|
| **zenstory-ai/drama-skills** | **2,734⭐** | 574 | 11 个 Agent Skill 合集 | 全链路、最成熟、工作室产线出身 | Python 3.9+ / MIT |
| **eternityspring/shuohao-skills** | **约 3.4k⭐**（第三方收录） | — | 6 个 Skill + 零依赖脚本 | 工程化质检、确定性流水线 | Node ≥18 / Apache-2.0 |
| **sunchaosz/short-drama-maker** | 新发布（星标尚少） | — | **WorkBuddy 原生 Skill** | 最贴合本机环境、最简可跑 | Python / MIT |

**一句话结论**：高星标短剧 skill 的" winning formula " = **真实产线背书 + 全链路覆盖 + 显式解决"跨镜一致性"这一头号痛点 + 工程化质量门 + 人审成本闸门 + 开箱即用的安装与演示**。"一键成片"反而是减分项——诚实架构（每镜人审）才是高星标项目的共同标签。

---

## 二、三个标杆项目深度解析

### 2.1 zenstory-ai/drama-skills（2,734⭐ · 高星标标杆）

**出身与定位**：来自真实漫剧工作室产线（2025 至今累计上千个 AI 短剧项目），把近 8 万行前后端代码蒸馏成 11 个 Agent Skill。适配 Claude Code / Codex / 任何支持 Agent Skill 规范的环境。

**11 个技能（流水线）**：
```
原著分析 → 故事开发 → 分集剧本 → 资产决策 → 图片提示词 → 分镜/关键帧 → 视频提示词 → 确认后生产 → 剪辑成片 → 审查 → 文本交付包
```

**核心设计（值得抄作业的点）**：

1. **"五份 Markdown 就是创作事实"**：每集只维护 `剧本.md / 视觉设定.md / 分镜.md / 图片提示词.md / 视频提示词.md`，剪辑时多一份 `剪辑单.md`。无数据库，改哪份文件就是改哪一层决策——**文件即真相（file-as-source-of-truth）**。
2. **连续性锁（Continuity Lock）—— 一致性命门**：需要跨镜保持的造型写成一条"能原样贴进提示词的短语"（如 `LOCK-JIANGCHEN-DRESS：pine-green lapel service jacket`），每个镜头冻结关键帧时原样嵌入。这是比"资产护照"更工程化的表达。
3. **引用核对脚本 `creator_markdown_check.py`**：脚本核对五份文档之间的引用一致性。故意改坏两处（删视觉依据、改时长），它报的是**原因**而非"校验失败"：
   ```
   ERROR: SHOT-EP001-002: 冻结关键帧提示词写到人物「周薄森」，视觉依据没有覆盖……
   ERROR: SHOT-EP001-003: 分镜时长 2 秒与视频提示词 3 秒不一致……
   ```
4. **"先预览、确认，再生产"**：任何图片/视频/TTS/音乐任务，先在文件里看到准确内容和参数，明确确认后才调外部 adapter 执行。供应商凭据不进项目。**成本闸门 = 星标友好型设计**。
5. **工程化配套**：本地创作台 Dashboard（分镜视图/节奏条）、`project_tool.py export` 带校验和的交付目录、CI 回归、CHANGELOG、CONTRIBUTING、完整 examples（《让你管账号》EP001 四份原文全公开）。
6. **模型无关 + 方言适配**：视频提示词支持 MiniMax H3 六段式、Seedance、GPT Image 2、MiniMax Music，通过"adapter"解耦供应商。

**为什么它能拿 2.7k⭐**：真实产出样例 + 文档即证据 + 把"一致性/口型错绑/对白赶读"等真实坑写成 FAQ + 开源全套 + 维护活跃（几乎每天 commit）。

---

### 2.2 eternityspring/shuohao-skills（约 3.4k⭐ · 工程化标杆）

**定位**：Agent-agnostic 的 AI 短剧前期制作 skill 集，"从一本小说到能直接喂给生成管线的制作素材"。

**6 个技能**：`novel-outline`（改编大纲，14 道门）/ `novel-characters`（角色圣经）/ `character-refs`（**真出角色参考图**）/ `novel-art`（场景+道具美术圣经，10 道门）/ `novel-script`（剧本，10 道门）/ `novel-storyboard`（分镜，18 道门）。**合计 52 道质量门**。

**核心设计（工程纪律典范）**：

1. **零依赖确定性脚本**：每个 skill 的 `scripts/*.mjs` 只用 Node 标准库，**不装 npm 包、不调模型、不耗额度**。质量门 100% 脚本化。
2. **`selftest.mjs` 硬要求**：每个 skill 必须有不调模型的自测，覆盖全部确定性逻辑。`for f in skills/*/scripts/selftest.mjs; do node "$f"; done` 一键全跑。
3. **汇编器 `report.mjs`**：把 5 个阶段报告合并成单页 HTML（左侧导航、作用域隔离防样式/脚本串味、资源路径重定位）。这是"把多个 skill 的产出当组件拼装"的高级思路。
4. **明确的设计哲学**：*"大纲收敛结构；剧本、场景、角色一起迭代；分镜只输出、不做新决策"*——**职责单一、数据单向流动**，避免 skill 之间互相越界。
5. **安装优雅**：`install.sh` 用 symlink 把 skill 装进 Claude Code/Codex，支持单装/卸载，`git pull` 即时生效。

**为什么它能拿 ~3.4k⭐**：把"衔接"这一最痛点（大纲改了角色没同步、分镜和画面对不上）用确定性脚本彻底解决；零依赖让任何人 1 秒跑通；报告可视化漂亮。

---

### 2.3 sunchaosz/short-drama-maker（WorkBuddy 原生 · 最贴合你环境）

**定位**：直接是 **WorkBuddy skill**，把"做 AI 短剧"拆成可复用流水线。**你当前环境零改造即可用**。

**六阶段 + 9 道质量门（Q0–Q5）**：
```
0 创意立项(00_concept) → 1 剧本开发(01~04 + 14门) → 2 分镜拆解(05~08 + 压力测试)
→ 3 美术素材(ImageGen) → 4 视频生成(VideoGen) → 5 剪辑成片 → 6 配套图表(可选)
```

**核心设计**：
- **资产护照（Asset Passport）—— 一致性命门**：为每个角色/场景/道具写一段锁定不变的视觉描述，生成每个镜头时**逐字复制**进提示词，变化只改动作/机位。
- **爆款 7 条规则 + 概念卡**做创意自检；**14 道剧本清单**（钩子/冲突/口语化/集钩/节奏/合规等）。
- **工具映射表**：ImageGen / VideoGen 对应 Seedream、Seedance 2.0、Kling、Veo 3.1 等外部模型，护照段保持原样只改方言。
- **`scripts/init_project.py`** 一键生成标准目录与空白模板。

**价值**：它是三者里**唯一原生适配 WorkBuddy** 的，且结构最精简、最易上手。缺点是星标尚少、质检偏"清单式"而非"脚本式"、未覆盖真实出图/出视频的 adapter 层。

---

## 三、横向对比与共性公式

### 3.1 架构对比

| 维度 | drama-skills | shuohao-skills | short-drama-maker |
|---|---|---|---|
| 阶段数 | 10+（11 skill） | 6 skill（大纲→分镜） | 6 阶段 |
| 一致性机制 | 连续性锁（短语锚定） | 前期设定图作参考图传递 | 资产护照（逐字复制） |
| 质量门 | Python 引用核对脚本 | **52 道 Node 脚本门** | 9 道清单门（Q0–Q5） |
| 人审闸门 | 确认后生产（adapter） | 报告预览 | 先小样再量产 |
| 出图/出视频 | 外部 adapter（多模型） | character-refs 真出图 | ImageGen/VideoGen |
| 安装 | `npx skills add` / symlink | `install.sh` symlink | git clone → WB |
| 演示资产 | 样片+Dashboard+样例 | 报告 webp+汇编器 | 暂无 |
| 星标 | 2.7k | ~3.4k | 少 |

### 3.2 高星标共性公式（提炼）

把三者叠加，高星标 = 以下要素的加权和：

1. **真实产线背书（权重最高）**：drama-skills（千个项目）、shuohao（真实生产）。*"watchable, not publishable"*——诚实承认自动化上限，反而赢信任。
2. **显式解决跨镜一致性**：这是 AI 短剧的#1 痛点。三者都有自己的一致性机制（锁/护照/参考图）。**不解决一致性 = 不专业**。
3. **工程化质量门**：脚本化、可离线、零模型额度消耗的校验，是"可复现、可信任"的象征。shuohao 的 52 门是极致。
4. **人审/成本闸门**：预览→确认→生产。对成本敏感的用户极友好，也是差异化卖点。
5. **Agent 无关 + 极简安装**：`npx`/symlink/clone 即装；SKILL.md 规范通用（Claude Code/Codex/WB 通吃）。
6. **视觉演示与文档证据**：样片视频、Dashboard 截图、star-history 图、公开完整样例。
7. **开源协议 + 持续维护**：MIT/Apache、活跃 commit、CI、CHANGELOG。
8. **细分市场权威**：精准卡位"中文 AI 短剧"这一 2026 爆火赛道，比泛泛的"video generator"更易突围。

---

## 四、给你的高星标流水线蓝图（建议）

结合你的现有资产（**Seedance 2.5 文生视频提示词 skill v02/v03，其中 v03 含 SPATIAL_WEIGHT_CAMERA 防穿帮模块**），建议做一套 **WorkBuddy 原生、但 Agent 规范通用** 的短剧流水线 skill，差异化锚定 **Seedance 2.5 + 多人戏防穿帮**。

### 4.1 推荐流水线（融合三者优点）

```
S0 创意立项      爆款7规则 → 概念卡(00_concept)            [门Q0: 前三秒钩子+第一幕冲突]
   │
S1 剧本开发      大纲(四幕)→角色→美术→剧本(04)            [门Q1a/b/c: 14道清单]
   │
S2 分镜拆解      ├ 场景拆解(05)
                ├ 参考板(06)
                ├ 资产护照/连续性锁(07)  ← 融合 drama-skills 锁 + 资产护照
                ├ 镜头卡(08)  ← 接你的 Seedance2.5 提示词模板(v03 站位权重/机位总则)
                └ 压力测试(Q2b: 相邻镜头护照抽检)
   │
S3 美术素材      ImageGen 出角色/场景/道具定妆图           [门Q3: 同角色多图一致]
   │
S4 视频生成      Seedance 2.5 图生视频 + 续帧
                ← 调用你的 v03 防穿帮模块(SPATIAL_WEIGHT_CAMERA)处理多人戏  [门Q4]
   │
S5 剪辑成片      字幕/配音/配乐/转场/9:16导出              [门Q5]
   │
S6 (可选)配套图  路演/PPT 配图
```

### 4.2 你的独家差异化（决定是否拿星）

1. **Seedance 2.5 原生方言**：绝大多数开源项目押 MiniMax H3 / Kling / Veo；**中文市场 Seedance 原生流水线仍是空白**，这是你最大的卡位机会。
2. **多人戏防穿帮（你的 v03 模块）**：`SPATIAL_WEIGHT_CAMERA`（站位权重 + 机位总则）专门解决多人同框穿帮——这是所有竞品都没单独做成模块的"硬骨头"，做成显式质量门就是杀手锏。
3. **工程化质量门**：借鉴 shuohao，把一致性抽检、时长对齐、护照覆盖写成**可离线运行的 Python/Node 脚本**（不调模型），并配 `selftest`。
4. **人审成本闸门**：视频生成前展示"镜头数/参考图/参数/预估成本"，确认后才跑——契合你"先预览后生产"的习惯。
5. **WorkBuddy 原生但通用**：SKILL.md 同时兼容 Claude Code/Codex；`init_project.py` 一键脚手架；`npx skills add` 或 clone 即装。
6. **演示资产**：用你自己的 Seedance 2.5 跑 1 集真实样片 + Dashboard 截图 + README 里的 star-history 图。

### 4.3 落地步骤（建议优先级）

| 步骤 | 动作 | 说明 |
|---|---|---|
| 1 | 先 `clone` 三个 repo 本地通读（已为你拉取 short-drama-maker 全文；另两个因代理 502 用 WebFetch 取了 README/结构） | 建议本机再 clone 一次做离线精读 |
| 2 | 把 v02/v03 提示词模板接入 S2 镜头卡 + S4 视频生成 | 复用你已有 IP |
| 3 | 抽离"资产护照 + 连续性锁"为统一一致性层 | 融合 drama-skills 与 short-drama-maker |
| 4 | 写质量门脚本（Python，零依赖，含 selftest） | 抄 shuohao 的工程纪律 |
| 5 | `init_project.py` 脚手架 + 工具映射表 | 对齐 short-drama-maker |
| 6 | 跑 1 集端到端样例，录样片 | 演示即证据 |
| 7 | MIT 开源 + README + star-history + ClawHub/npx 分发 | 拿星关键 |

---

## 五、风险与避坑

- **一致性不是靠运气，靠纪律**：三者一致强调"护照/锁逐字复制"。任何"自动改写提示词"都会让长片崩坏。
- **不要跳过质量门**：short-drama-maker 明言"跳过 Q2 直接生视频，90% 会在长片翻车"。
- **诚实架构 > 一键神话**：pixo.video 评测指出"one-click output is watchable, not publishable"，保留人审门的项目反而更被信任、更持久。
- **安全审计**：若要把上述仓库 `clone` 后直接装进 `~/.workbuddy/skills/`，按你的约定必须先做安全审计（正则扫网络/命令执行/凭据/提示词注入），再安装。
- **代理/网络**：本次调研中 GitHub 直连出现间歇性 502（ClawHub 代理），clone 大仓（drama-skills 75MB）易失败，建议用 WebFetch 取单文件或换网络重试。

---

## 六、结论

三个标杆项目证明：**AI 短剧赛道的"高星标"不属于"一键成片"的玩具，而属于"把真实产线经验工程化、把一致性做成可核查事实、把成本闸门交还给人"的严肃工具**。你的独特优势（Seedance 2.5 原生 + v03 多人防穿帮模块 + WorkBuddy 原生环境）正好卡在竞品空白区。按本报告第四节蓝图落地，是一套有真实突围机会的高星标 skill。

> 下一步建议：确认是否要我**直接基于本报告蓝图，起草你自己的短剧流水线 skill 骨架**（SKILL.md + 资产护照层 + 质量门脚本 + init_project.py 脚手架），复用你已有的 v02/v03 提示词模板。
