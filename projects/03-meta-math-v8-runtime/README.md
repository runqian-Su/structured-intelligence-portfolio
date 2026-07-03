# META-MATH-V8：ideon 与 Guarded Runtime / Ideon and Guarded Runtime

> Transformer is Flat. The World is Deep.

META-MATH-V8 是 META-MATH 路线中的第八个大版本。它不是把 LLM 包装成“多想几步”的聊天流程，而是尝试在 LLM 外部建立一个 guarded runtime：LLM 负责提供世界知识、候选概念和自然语言转译，runtime 负责窗口、容器、跃迁、目标评估和 guard 判定。

日常工作中，我更常使用的是 META-MATH-V4。V4 是一个 Rust 实现的概念图运行分支，围绕 `Ideon`、`Morphism`、HMF flow、Pomegranate sheaf、metabolism audit、causal chaos 和 Ouroboros fixed-point iteration 形成较稳定的工程底座。V8 则是在 V4 之后继续上探的架构版本，重点是把 ideon、五维波包、运行窗口、容器跃迁和解释权控制组织成 agent-in-loop 的外挂式 System 2。

需要明确区分的是：原始目录中的 `Concept World` 属于 V8 之后下一轮重构版本的开头，不属于 V8 已完成范围。这个项目页只说明 V8 本身，不把后续 Concept World 计入 V8。

## 目录 / Documents

- [Ideon_概念状态单元说明.md](Ideon_概念状态单元说明.md)：补充说明 ideon 为什么不是 token / 静态 graph node，以及“内封时间”如何让概念拥有局部生命周期。
- [META_MATH_V8_架构说明.md](META_MATH_V8_架构说明.md)：说明 ideon、五维波包、四大元算法、代谢算子、因果混沌算子与解释权边界。
- [Agent_in_loop_Runtime说明.md](Agent_in_loop_Runtime说明.md)：说明播种器、运行协调器、受限解经器，以及 window / container / transition / guard 的闭环。
- [META_MATH版本演化说明_V4到V8.md](META_MATH版本演化说明_V4到V8.md)：说明 V4 与 V8 的版本差异、工程侧重和迭代关系。
- [diagrams/runtime-structure.md](diagrams/runtime-structure.md)：V8 guarded runtime 的 Mermaid 图示。

## 一句话定位 / One-Line Positioning

META-MATH-V8 的核心不是“让 LLM 自己成为最终裁决者”，而是把 LLM 放进一个受约束的 guarded runtime：LLM 提供材料和表达，runtime 决定哪些状态可以继续推进、哪些候选只能保留为中间解释、哪些结果通过 guard 后才拥有最终解释权。

## 当前边界 / Current Boundary

这个目录只保留公开可读的架构说明、运行逻辑和图示，不包含原始脚本、私有运行快照、大型 JSON 报告或未清理源码。V8 当前更适合被理解为 ideon 与 guarded runtime 的架构原型；V4 更接近日常可运行的工程分支。
