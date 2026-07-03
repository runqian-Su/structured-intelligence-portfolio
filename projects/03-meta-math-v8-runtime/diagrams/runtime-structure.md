# META-MATH-V8 运行结构图 / Runtime Structure

## 1. 总体结构

```mermaid
flowchart TB
  A[现实输入层<br/>用户问题 / 背景事实 / 约束 / 目标] --> B[播种器 Seeder<br/>观测视角 / 概念域 / 初始张力 / 边界]
  B --> C[结构运行状态<br/>ideon / 五维波包 / 关系张力 / 边界]
  C --> D[Runtime Window<br/>有限窗口内推进状态演化]
  D --> E[Selection & Convergence<br/>dominant kernel / stability / obstruction]
  E --> F[Transition Supervisor<br/>固化 / 升级 / 回退 / 修正 / 停止]
  F --> G[Container Snapshot<br/>规则 / 合法概念域 / 当前边界]
  G --> D
  E --> H[Guarded Output<br/>goal evaluation / guard 判定]
  H --> I[受限解经器 Constrained Interpreter<br/>自然语言解释 / 图示 / 行动建议]
```

## 2. Agent-in-loop

```mermaid
flowchart LR
  LLM[LLM<br/>知识候选 / 隐关系 / 自然语言转译] -.提供候选.-> Runtime[Guarded Runtime]
  Agent[Agent<br/>播种 / 观察窗口 / 有限解释] -.受限介入.-> Runtime
  Runtime --> WR[Window Report]
  Runtime --> TR[Transition Report]
  Runtime --> CS[Container Snapshot]
  WR --> Guard[Guard]
  TR --> Guard
  CS --> Guard
  Guard -->|通过| Final[可解释输出]
  Guard -->|未通过| Candidate[候选 / 中间状态 / 继续演化]
```

## 3. LLM 与 Runtime 的边界

```text
LLM:
  提供可能性、背景知识、候选补全、自然语言表达

Runtime:
  执行窗口推进、结构校验、目标评估、跃迁监督和 guard 判定

Agent:
  负责播种、观察、有限解释和必要时重播种

Guard:
  决定当前状态是否拥有解释权
```

## 4. V4 到 V8 的位置关系

```mermaid
flowchart TB
  V4[META-MATH-V4<br/>Rust 概念图运行底座] --> A[Ideon / Morphism]
  V4 --> B[HMF Flow]
  V4 --> C[Pomegranate Sheaf]
  V4 --> D[Causal Chaos / Metabolism]
  V4 --> E[Ouroboros Fixed-Point Iteration]

  A --> V8[META-MATH-V8<br/>第八个大版本 / Guarded Runtime]
  B --> V8
  C --> V8
  D --> V8
  E --> V8

  V8 --> W[Window]
  V8 --> Co[Container]
  V8 --> T[Transition]
  V8 --> G[Guard]
  V8 --> O[Constrained Output]

  V8 -.后续重构方向，不计入 V8.-> Next[Concept World]
```
