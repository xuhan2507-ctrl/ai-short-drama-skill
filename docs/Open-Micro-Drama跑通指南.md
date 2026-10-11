# Open-AI-Micro-Drama 本地跑通指南

> 来源：`Anil-matcha/Open-AI-Micro-Drama-Generator`（MIT）—— 最清晰的「想法→成片」8 阶段代码实现，单一 MuAPI Key 跑通全流程。本文基于**真实仓库 README** 整理。
> 本地克隆路径：`H:\xhy work\2026-10-11-09-54-32\_research_omd\`

---

## 一、项目定位

- **Agentic 微短剧生成器**：Screenwriter → CharacterExtractor → StoryboardArtist → MuAPI 图像/视频工具 → moviepy 拼接，全自动。
- **一个 Key 跑全程**：所有 LLM、图像、视频生成都走 MuAPI，只需 `MUAPI_KEY`。
- **两种模式**：`idea2video`（8 阶段，从想法起）/ `script2video`（6 阶段，跳过写剧本，从脚本起）。
- 前端 Next.js 14，可选；后端 FastAPI 提供 SSE 实时进度。

---

## 二、8 阶段流水线（idea2video，Stage / Agent·Tool / Output）

| # | 阶段 | Agent / Tool | 输出 |
|---|------|-------------|------|
| 1 | Story Development | Screenwriter (MuAPI LLM `claude-sonnet-4-6`) | 散文式故事大纲 |
| 2 | Character Extraction | CharacterExtractor (LLM) | 角色静态+动态视觉特征 |
| 3 | Scene Scripting | Screenwriter (LLM) | 2–4 个独立场景脚本 |
| 4 | Character Portraits | MuAPI `flux-dev-image` (T2I) | 每角色参考肖像 — **并行** |
| 5 | Storyboard Design | StoryboardArtist (LLM) | 每场景 3–5 镜（视觉+动作+音频描述） |
| 6 | Frame Generation | MuAPI `flux-kontext-dev-i2i` (I2I) | 用角色参考图生成每镜**首帧** |
| 7 | Video Generation | MuAPI `kling-v2.1-standard-i2v` (I2V) | 每镜 5 秒视频 — **并行** |
| 8 | Concatenation | moviepy | 拼接成 `final_video.mp4` |

**script2video 短流水线**（跳过 1–3）：角色提取 → 肖像 → 分镜 → 首帧 → 视频 → 拼接。

---

## 三、架构与数据流

```
Idea/Script → Screenwriter ─► CharacterExtractor ─► StoryboardArtist ─► MuAPI Tools ─► moviepy ─► final_video.mp4
                 (LLM)           (LLM)              (LLM)        ①flux-dev-image(T2I肖像)
                                                           ②flux-kontext-dev-i2i(I2I首帧)
                                                           ③kling-v2.1-standard-i2v(I2V视频)
```
所有 LLM 调用走 MuAPI `/claude-sonnet-4-6`；图像/视频走对应 MuAPI 端点；任务统一异步：`submit → 拿 request_id → 每 3s poll → completed 返回`。

---

## 四、本地跑通步骤（已克隆，下一步填 Key 即可）

### 前置条件
- Python 3.10+、Node.js 18+、一个 **MuAPI API Key**（https://muapi.ai 注册获取）。

### 1. 进入 server，建虚拟环境并装依赖
```bash
cd "H:\xhy work\2026-10-11-09-54-32\_research_omd\server"
python -m venv venv
.\venv\Scripts\activate        # Windows
pip install -r requirements.txt
cp .env.example .env           # 然后编辑 .env，填入 MUAPI_KEY=你的key
```

### 2. 启动后端
```bash
uvicorn api:app --reload --port 8000
```
健康检查：`GET http://localhost:8000/api/health` → `{"status":"ok"}`

### 3. 启动前端（可选）
```bash
cd "H:\xhy work\2026-10-11-09-54-32\_research_omd\client"
npm install
npm run dev
```
浏览器开 http://localhost:3000 → 输入 idea、选风格（Cinematic/Realistic/Anime/Fantasy/Documentary）、点 Generate。

---

## 五、不依赖前端：直接 POST 触发

```bash
curl -X POST http://localhost:8000/api/generate \
  -H "Content-Type: application/json" \
  -d '{"idea":"一个侦探在霓虹赛博城市追逐影子","user_requirement":"不超过3个场景,适合全年龄","style":"Cinematic","mode":"idea2video"}'
# 返回 {"job_id":"..."}
```
进度：`GET /api/status/{job_id}`（SSE，自动重连）；结果：`GET /api/result/{job_id}`。
流水线阶段顺序：`screenwriter → characters → storyboard → portraits → frames → video → concat`。

---

## 六、关键实现细节（可直接抄）

- **数据模型先定义**：`interfaces/character.py`（`CharacterInScene`：idx/name/static/dynamic features）、`interfaces/shot.py`（`ShotBriefDescription`/`ShotDescription`）——先定结构再写流程，是它能稳定串联的关键。
- **并行生成**：阶段 4 肖像并行、阶段 7 视频并行（同一任务的多个镜头同时提交）。
- **首帧锚定**：阶段 6 用 `flux-kontext-dev-i2i` 把角色参考图作为条件生成每镜首帧，再交给 I2V——正是 Lock-Then-Animate 的「参考图→首帧」实践。
- **分层**：`agents/`（LLM 角色）/ `interfaces/`（Pydantic）/ `tools/`（MuAPI 集成）/ `pipelines/`（idea2video.py / script2video.py 编排）/ `utils/video.py`（moviepy 拼接）。

---

## 七、还差什么才能真出片

- **必须 `MUAPI_KEY`**：用户尚未提供。我已克隆仓库并准备好 server 环境骨架；你拿到 Key 后填入 `.env` 即可一键跑通 8 阶段 Demo。
- 费用：按 MuAPI 各模型定价实时计费（flux-dev-image / flux-kontext / kling 等），建议在 Web UI 先看预览再确认。

---

## 八、与我们短剧 Skill 的对照

| Open-Micro-Drama | 我们的 SKILL.md |
|---|---|
| 顺序 8 阶段（Stage→Tool→Output 显式映射） | 第一节通用 8 阶段主线 |
| 单一 MuAPI Key 降门槛 | 第四节「供应商凭据不进项目」 |
| 首帧锚定（I2I→I2V） | 第六节 Lock-Then-Animate 四机制 |
| 数据模型先定义 | 第七节 11 路由（建议每步先定产物结构） |

**可补强点**：把「数据模型先定义」「肖像/视频并行」「首帧锚定」三条明确写进我们 SKILL 的第六/七节，使流水线更可工程化。
