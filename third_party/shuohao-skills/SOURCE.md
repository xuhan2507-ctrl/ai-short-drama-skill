# 来源说明 / SOURCE

- **原始仓库**: https://github.com/eternityspring/shuohao-skills
- **原作者**: eternityspring / shuohao-skills contributors
- **Stars(调研时参考)**: 约 3.4k
- **许可证**: Apache License 2.0（随附 `LICENSE` 文件）
- **抓取日期**: 2026-10-12
- **抓取内容**: 完整保留其 6 个 `SKILL.md`、`README.md` 与 `LICENSE` 原文，目录结构保持不变。

> ⚠️ **Apache-2.0 合规要点**：若你基于本目录内容派生 / 修改文件并发布，必须：
> 1. 附上原 `LICENSE`；
> 2. 在被修改文件显著标注「你改动了哪些内容」；
> 3. 保留所有原有版权 / 署名 / 专利声明。

## 本仓库如何使用
用于横向对比、合并、加强你自己的短剧 skill 流水线，尤其是其工程化质量门思路。

## skill 清单（共 6）
- `skills/character-refs` —— 角色参考
- `skills/novel-art` —— 小说美术
- `skills/novel-characters` —— 小说角色
- `skills/novel-outline` —— 小说大纲
- `skills/novel-script` —— 小说脚本
- `skills/novel-storyboard` —— 小说分镜

## 值得借鉴的机制
- **52 道 Node 零依赖质量门**：每个 skill 带 `selftest.mjs`，不调用模型、纯规则校验。
- **报告汇编器**：把各阶段产物汇总成可交付文档。
- **确定性流水线**：5 个 skill 串联成可复现产线。
