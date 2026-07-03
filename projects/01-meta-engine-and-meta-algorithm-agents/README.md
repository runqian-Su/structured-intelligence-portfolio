# 元算法 Agent 早期验证 / Meta-Algorithm Agent Validation

本目录说明 H/L/P/O 四大元算法如何从方法论文档进入可运行的 Dify chatflow agent，并通过早期测试观察其对通用大模型推理路径的影响。

This folder explains how the H/L/P/O meta algorithms were organized as runnable Dify chatflow agents, and how early tests observed their effect on LLM reasoning paths.

## 主要文档 / Main Document

- [理论体系概览_元引擎与四大元算法.md](理论体系概览_元引擎与四大元算法.md)
- [元算法Agent早期验证_综合说明.md](元算法Agent早期验证_综合说明.md)
- [HMF与Laplace_Chatflow解构测试补充说明.md](HMF与Laplace_Chatflow解构测试补充说明.md)

## 核心问题 / Core Question

> 当 HMF、Laplace 等元算法被做成 Dify chatflow agent 后，通用大模型是否会在算法文档、节点规则和阶段化中间表示的约束下，呈现出不同的问题重定义、结构化拆解、约束识别和复杂博弈推理路径？

In short: can conceptual algorithms become runnable agent workflows that shape how an LLM enters, decomposes, and reasons about complex problems?

## 阅读方式 / Reading Order

建议先读理论体系概览，再读 Agent 早期验证，最后看 HMF / Laplace 的补充测试说明：

1. `理论体系概览_元引擎与四大元算法.md`：说明元引擎是什么，HMF / Laplace / Pomegranate / Ouroboros 分别负责什么。
2. `元算法Agent早期验证_综合说明.md`：说明这些元算法如何被做成 Dify chatflow agent，以及早期测试观察到什么。
3. `HMF与Laplace_Chatflow解构测试补充说明.md`：说明新增 raw note 中 HMF / Laplace chatflow 的解构方式、行为证据和边界。

## 公开范围 / Public Scope

这个公开版只保留综合说明，不包含：

- 原始 raw notes
- Dify YAML / workflow exports
- 私有配置或账号信息
- 过程性评分记录

The public version keeps only the integrated explanation and excludes raw notes, Dify exports, private configs, and process-level scoring records.

## 与后续项目的关系 / Relation to Later Projects

这组早期验证是后续项目的起点之一：

- HSE / HMF-IC：来自 HMF 的工程领域投影。
- HLPO：来自元引擎“交互”问题的模型结构化尝试。
- META-MATH-V8：进一步处理 LLM 外部解释权、运行窗口和 guard。
- HoLO：进一步处理 code-agent 的长程任务解构、结构化记忆和边界裁决。
