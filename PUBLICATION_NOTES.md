# Publication Notes / 公开说明

这个仓库是专门整理过的公开阅读版，不是原始项目目录的完整复制。

This repository is intentionally curated for public reading. It is not a full copy of the original working directories.

## Included / 包含内容

- 项目级 README。
- Project-level README files.
- 架构说明与运行逻辑说明。
- Architecture and runtime descriptions.
- Mermaid 图示。
- Mermaid diagrams.
- 精选实验结论和可公开图片。
- Selected experiment summaries and public visualization images.
- 整理后的定性压力测试摘要与行为观察。
- Curated qualitative stress-test summaries and behavior observations.
- 每个项目的已验证内容、边界和后续方向。
- Boundaries between validated evidence, limitations, and next steps.

## Excluded / 不包含内容

- API keys、tokens、私有配置、账号信息和凭证。
- API keys, tokens, private configs, account identifiers, and credentials.
- Dify YAML exports 和 workflow 文件。
- Dify YAML exports and workflow files.
- raw notes、原始对话转储和未整理的压力测试过程记录。
- Raw notes, raw conversation dumps, and uncurated stress-test transcripts.
- 未清理的完整源码仓库。
- Full source repositories before cleanup.
- 原始私有日志、大型 JSON snapshots、checkpoint、parquet 数据和模型权重。
- Raw private logs, large JSON snapshots, checkpoints, parquet datasets, and model weights.
- 构建产物、缓存和本地环境文件。
- Build outputs, caches, and local environment files.

## Evidence Standard / 证据标准

每个项目尽量区分：

- **Validated evidence / 已验证证据**：有具体运行、报告、图片、指标或实现痕迹。
- **Design hypothesis / 设计假设**：已经设计出机制或方向，但还未充分 benchmark。
- **Indirect support / 间接支撑**：某个任务侧结果不能直接证明更大理论成立，但能支撑其中一个工程化假设。
- **Research extension / 研究延展**：下一步可能推进的方向，不应和当前结果混为一谈。

## Safety Boundary / 安全边界

部分早期压力测试使用对抗性、灰度或高风险场景来观察 agent 的结构解构能力。公开版本只保留行为观察和方法论总结，不发布可执行操作细节，也不把这些场景视为行动建议。

