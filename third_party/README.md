# 第三方短剧 Skill 合集（third_party）

本目录聚合了多个高星标开源短剧 / AI 视频生成 skill 项目，用于**横向研究、合并与加强**本仓库自己的流水线。

## 来源总览

| 项目 | 原始仓库 | Stars(调研时参考) | 许可证 |
|------|----------|------------------|--------|
| drama-skills | zenstory-ai/drama-skills | ~2.7k | MIT |
| shuohao-skills | eternityspring/shuohao-skills | ~3.4k | Apache-2.0 |
| short-drama-maker | sunchaosz/short-drama-maker | 较新 | MIT |

各子目录均保留原作者的 `LICENSE`、`README.md` 与 `SOURCE.md`（本文件说明来源与合规要求）。

## 合规须知
- 所有文件保持原作者版权与许可证，**不得冒充原创发布**。
- 派生 / 修改时须显著标注来源与改动；**Apache-2.0 额外要求在被修改文件标注变更**。
- 本合集仅作研究 / 参考；对外发布派生作品须遵守对应许可证。

## 与本仓库自有 skill 的关系
- 本仓库根目录 `SKILL.md`（production v2）是**主线**；
- 本 `third_party/` 目录是**外部参考**，便于合并共性能力、补齐质量门与一致性机制（连续性锁、资产护照、人审闸门等）。

## 推荐整合路线（供你合并 / 加强）
1. 抽取 drama-skills 的「连续性锁」+ 五份 Markdown 标准化产物机制。
2. 抽取 shuohao-skills 的「零依赖质量门脚本」思路，做成可离线运行的检查层。
3. 对照 short-drama-maker 的「资产护照」校验本仓库主线 `SKILL.md` 的一致性描述。
4. 锚定你自有的 Seedance 2.5 方言 + v03 多人防穿帮模块（`SPATIAL_WEIGHT_CAMERA`）作为差异化核心。
