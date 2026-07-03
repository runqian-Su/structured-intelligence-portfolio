# Structured Intelligence Portfolio / 结构化智能作品集

这是一个围绕 **概念计算、结构化智能、模型结构实验与 Agent Runtime** 的个人研究与工程作品集。

This repository is a curated research and engineering portfolio on concept computation, structured intelligence, model-structure experiments, and agent runtime design.

核心问题是：

> 概念、自然语言与长程任务，如何从平面文本变成可计算、可执行、可复盘的结构？

The common question is:

> How can concepts, natural language, and long-horizon tasks become computable structures rather than only flat text sequences?

这条路线从一个面向概念计算的元引擎开始，随后发展出 H/L/P/O 四大元算法、Dify chatflow agents、HLPO 交互注意力、META-MATH-V8 外挂式 System 2 runtime、SDFN 结构决策场网络，以及 HoLO code-agent runtime。当前仓库以整理后的说明材料为主，重点展示每个项目的核心问题、结构机制、已验证证据和明确边界。

## 技术路线 / Research Route

```text
Meta Engine / 元引擎
  -> H/L/P/O Meta Algorithms / 四大元算法
  -> Dify Chatflow Agents / 元算法 Agent 化验证
  -> HSE / HMF-IC Engineering Agents / 工程领域投影
  -> HLPO Interactive Attention / HardGate
  -> META-MATH-V8 Agent-in-loop Runtime
  -> SDFN Structured Decision Field Network
  -> HoLO Agent Runtime
```

项目成熟度概览见：[PROJECT_STATUS.md](PROJECT_STATUS.md)。

## 项目目录 / Projects

### 01. Meta Engine and H/L/P/O Agent Validation / 元引擎与四大元算法 Agent 验证

四大元算法的早期 agent 化验证。重点不是模型分数 benchmark，而是观察算法文档、节点规则和阶段化中间表示，是否会改变通用 LLM 进入复杂问题、重定义问题和组织推理路径的方式。当前材料补充了 HMF / Laplace chatflow 在复杂任务、空间推理和社会创新压力测试中的解构行为观察。

Path: [projects/01-meta-engine-and-meta-algorithm-agents](projects/01-meta-engine-and-meta-algorithm-agents)

### 02. HLPO Interactive Attention and HardGate / HLPO 交互注意力与 HardGate

HLPO 来自元引擎中的“交互”问题：概念之间不仅是并列关系，还存在作用方向、边界约束和反馈修正。当前公开证据重点是 Dense vs HardGate sparse attention A/B test。

Path: [projects/02-hlpo-hardgate-attention](projects/02-hlpo-hardgate-attention)

### 03. META-MATH-V8 Runtime / Guarded Runtime

META-MATH-V8 基于判断：**Transformer is Flat. The World is Deep.** 它尝试在 LLM 外部构建一个外挂式 System 2 / guarded runtime，让 LLM 提供候选、知识和自然语言转译，而让 runtime 负责窗口、容器、跃迁、目标评估和 guard 判定。当前材料补充说明了 ideon 作为概念状态单元，以及内封时间如何让概念拥有局部生命周期。原始目录中的 Concept World 属于 V8 之后的下一轮重构方向，不计入 V8 已完成范围。

Path: [projects/03-meta-math-v8-runtime](projects/03-meta-math-v8-runtime)

### 04. SDFN: Structured Decision Field Network / 结构决策场网络

SDFN 是为 TAAC 2026 推荐排序任务设计的结构化决策模型。它将推荐样本组织为 structure token、可学习 wave-packet parameters、decision field graph、HLPO structural routing 和 GraphOnlyReadout，而不是直接压成单个 dense vector。SDFN 不证明 ideon 世界模型成立，但它为“带状态参数的结构单元可以被训练并参与决策”提供了任务侧反向支撑。

Path: [projects/04-sdfn-structured-decision-field](projects/04-sdfn-structured-decision-field)

### 05. HoLO Agent Runtime / 面向 Code-Agent 的结构演算运行时

HoLO 用于解决 code-agent 在长程工程任务中的任务解构、结构化长期记忆、subagent 边界控制和工具调用审计问题。它把 agent、skill、tool、memory 都放到 HoloMesh 和 BoundaryGate 的结构控制下。当前材料包含 5 个 SaaS 后端诊断场景的 HoloUnit 拆解结果，以及 Phase 3-6 runtime snapshot 的摘要。

Path: [projects/05-holo-agent-runtime](projects/05-holo-agent-runtime)

## 这个仓库是什么 / What This Repository Is

- 一个公开可读的个人研究与工程作品集。
- A public research and engineering portfolio.
- 几个独立原型之上的整理文档层，而不是原始大仓库转储。
- A curated documentation layer over several independent prototypes.
- 一条连续技术路线的记录：从概念建模，到模型结构实验，再到 agent runtime。
- A record of a continuous route from concept modeling to model-structure experiments and agent runtime.
- 对每个项目尽量区分：已验证证据、设计假设、研究延展。
- A portfolio that separates validated evidence, design hypotheses, and research extensions.

## 这个仓库不是什么 / What This Repository Is Not

- 不包含 API keys、私有配置、Dify YAML、checkpoint、parquet 数据、模型权重或原始内部日志。
- It does not include API keys, private configs, Dify YAML exports, checkpoints, parquet datasets, model weights, or raw internal logs.
- 不包含 raw note、原始对话转储或未经整理的高风险压力测试细节。
- It does not include raw notes, raw conversation dumps, or uncurated high-risk stress-test details.
- 不声称所有早期探索都是标准 academic benchmark。
- It does not claim every exploratory test is a standardized academic benchmark.
- 不声称任何单一项目已经替代成熟模型架构或生产级 agent framework。
- It does not claim any single project already replaces mature model architectures or production agent frameworks.

## 建议阅读顺序 / Reading Order

1. 先读根目录 README，理解整条路线。
2. 读 Meta Engine，理解 H/L/P/O 的来源，以及 HMF / Laplace chatflow 如何改变问题入口。
3. 读 HLPO 和 SDFN，理解模型结构实验。
4. 读 META-MATH-V8 和 HoLO，理解 runtime 和 agent-system 方向。

## License / 许可说明

The documentation, diagrams, research notes, and curated explanations in this repository are licensed under CC BY-NC 4.0.

本仓库中的文档、图示、研究说明和整理后的项目解释材料采用 CC BY-NC 4.0 许可：允许非商业分享和改编，但必须署名。

Source code, model weights, private datasets, raw notes, workflow exports, and unpublished implementation materials are not included unless explicitly stated.

See [LICENSE.md](LICENSE.md).
