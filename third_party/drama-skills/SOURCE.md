# 来源说明 / SOURCE

- **原始仓库**: https://github.com/zenstory-ai/drama-skills
- **原作者**: zenstory-ai / drama-skills contributors
- **Stars(调研时参考)**: 约 2,734
- **许可证**: MIT License（随附 `LICENSE` 文件，版权归 drama-skills contributors）
- **抓取日期**: 2026-10-12
- **抓取内容**: 完整保留其 12 个 `SKILL.md`（含 `maintainers/` 下的 knowhow）、`README.md` 与 `LICENSE` 原文，目录结构保持不变。

## 本仓库如何使用
这是「第三方参考合集」的一部分，用于横向对比、合并、加强你自己的短剧 skill 流水线。
- 勿直接冒充原创发布；保留 `LICENSE` 与版权声明。
- 基于其中内容派生新 skill 时，请在派生文件显著标注来源与修改点（MIT 要求保留署名）。

## skill 清单（共 12）
- `skills/short-drama` —— 总入口
- `skills/short-drama-novel-analyze` —— 原著分析
- `skills/short-drama-write` —— 剧本创作
- `skills/short-drama-storyboard` —— 分镜拆解
- `skills/short-drama-assets` —— 视觉设定 / 资产
- `skills/short-drama-image-prompts` —— 图片提示词
- `skills/short-drama-video-prompts` —— 视频提示词
- `skills/short-drama-produce` —— 生产
- `skills/short-drama-edit` —— 剪辑成片
- `skills/short-drama-review` —— 审查
- `skills/short-drama-develop` —— 开发
- `maintainers/skills/short-drama-knowhow` —— 维护者知识库

## 值得借鉴的机制
- **连续性锁（continuity lock）**：跨镜角色 / 场景一致性控制。
- **五份 Markdown 即真相**：每集产出标准化中间文件。
- **人审闸门**：关键阶段需人工确认后才进入生产。
- **Python 引用核对脚本**：校验角色名 / 设定前后一致。
