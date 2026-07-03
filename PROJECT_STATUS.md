# Project Status / 项目状态

这个文件用于说明各项目当前成熟度。作品集同时包含已运行验证的原型和仍在发展的研究方向，因此这里把 **证据、边界、下一步** 分开写。

This file summarizes the current maturity of each project. Each item separates evidence, limitations, and next useful steps.

| Project / 项目 | Current evidence / 当前证据 | Current boundary / 当前边界 | Next useful step / 下一步 |
| --- | --- | --- | --- |
| Meta Engine and H/L/P/O Agents / 元引擎与四大元算法 Agent | Dify chatflow agent design；HMF / Laplace 在复杂任务、空间推理和社会创新压力测试中的定性解构记录 | 行为观察，不是标准 benchmark；raw notes 不作为公开材料发布 | 设计固定 prompt、固定输出和评分 rubric 的小型可复现实验 |
| HLPO HardGate Attention / HLPO HardGate 注意力 | Mac Studio 上的 Dense vs HardGate A/B test；约 100M tokens / 6000 steps；50% sparse attention；loss gap 0.0836 | 不能证明普遍替代 dense attention，也不能证明真实 kernel-level speedup | 在更多规模和任务上复跑，并区分算法稀疏与实现开销 |
| META-MATH-V8 Runtime / Guarded Runtime | ideon、内封时间、五维波包、guarded runtime、agent-in-loop flow 的架构与原型 | 主要是 runtime 架构和概念原型；Concept World 是 V8 之后的重构方向，不计入 V8 已完成范围 | 设计可复现实验，比较 guarded runtime 与直接 LLM 输出在可追溯性和错误控制上的差异 |
| SDFN | TAAC 2026 推荐实验；GraphOnlyReadout；local AUC 约 0.8465；HardSkip official AUC 0.825194；可学习 wave-packet parameters 对 ideon 状态单元路线形成任务侧反向支撑 | 任务特定原型，不是通用推荐框架；不能证明 ideon 世界模型成立 | 清理小型可复现实现或 ablation report |
| HoLO Agent Runtime | 5 个 SaaS 后端诊断场景均生成 HoloUnit / HoloMesh / DecisionFrame / BoundaryGate / snapshot；Phase 3-6 验证 memory graph、tool adapter、runtime API、daemon、MCP adapter、workbench 与 approval protocol | 阶段性验证，不是成熟 IDE agent 替代品；第一阶段 diagnosticReport 仍偏结构化，业务建议字段不完整 | 在长程 coding tasks 上评估 memory reuse 与 boundary-control 收益，并强化 task intent gating 与业务诊断字段 |

## Evidence Labels / 证据标签

- **Validated / 已验证**：有运行、指标、报告、图片或实现痕迹支持。
- **Prototype / 原型**：已经实现到可以测试 runtime flow 或系统行为，但还没有广泛 benchmark。
- **Design hypothesis / 设计假设**：机制或架构已经定义，但仍需要更强外部验证。
- **Indirect support / 间接支撑**：一个项目不能直接证明另一条理论路线，但能在更窄任务中支持其中某个工程假设。

这个仓库的目标不是把所有想法包装成同等成熟，而是把不同成熟度讲清楚。

The goal is to make maturity explicit rather than presenting all ideas as equally validated.
