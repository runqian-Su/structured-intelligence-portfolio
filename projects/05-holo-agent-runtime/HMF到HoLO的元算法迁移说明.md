# HMF 到 HoLO 的元算法迁移说明

HoLO 的源头是 HMF（Holographic Mesh Framework，全息织网）的结构化演算思想。HMF 的基本判断是：复杂任务不应先在语义层“讲故事”，而应先还原为 Source、Sink、Flow、Field 之间的结构关系。HoLO 将这个元算法迁移到 code-agent runtime：把自然语言任务转成可裁决、可执行、可记忆的 HoloUnit 网络。

## 1. HMF 中的结构原子

HMF 把一次有效运动定义为一个 Source -> Sink 的闭合单元。Source 是逻辑运动的起点，Sink 是逻辑运动的终点；中间的 Flow 表示被转移、加工或约束的对象，Field 表示环境阻力、边界、梯度和拓扑。

在 HMF 中，一个没有 Source 的流是无源运动，一个没有 Sink 的流是有始无终。对 agent 系统来说，这对应两类常见失败：

```text
没有 Source:
  agent 不知道任务从哪里被触发，容易泛泛规划。

没有 Sink:
  agent 不知道何时算完成，容易无限展开。
```

HoLO 将这条规则迁移为 runtime 的第一约束：任何可执行任务都必须先形成 HoloUnit。

## 2. 从 Holo-Unit 到 HoloUnit Runtime

HMF 中的 Holo-Unit 是 Source -> Sink 的结构原子。HoLO 将它变成 agent runtime 的基本控制单元：

```text
HoloUnit =
  Source / Sink 边界
  局部目标
  局部约束
  当前状态
  证据引用
  可用 skill / tool
  可分配 agent
  记忆引用
  BoundaryGate 裁决记录
```

HoloUnit 不等于 prompt、skill、tool 或 subagent。它位于用户意图和执行资源之间，负责限定一次局部任务的边界、目标、证据和权限。

## 3. 三类基础 Unit 的工程含义

HMF 中的基础单元可以映射到工程任务：

```text
Conduit Unit:
  结构源 -> 结构汇
  对应维护、迁移、同步、清理、流水线类任务。

Task Unit:
  触发源 -> 功能汇
  对应一次明确交付：修复 bug、实现功能、完成验证。

Domain Unit:
  梯度源 -> 滞留汇
  对应长期积累：知识库、结构化记忆、可复用 skill、系统能力沉淀。
```

在 code-agent 场景中，很多失败来自把 Domain Unit 当成 Task Unit：例如把“长期提升代码库理解能力”误写成一次性回答。HoLO 的作用是让 runtime 在任务进入执行前先识别 unit 类型，避免边界错配。

## 4. 三种组织行为迁移

HMF 中的组织行为在 HoLO 中变成 HoloMesh 的 transform chain：

```text
Downward Fractal:
  当一个 unit 太大、太黑盒、无法直接执行时，向下拆成多个 child units。

Upward Isomorphism:
  当当前 unit 的边界太窄、sink 错误或局部优化无效时，向上生成 parent unit。

Cross Fusion:
  当历史 unit、skill、memory 或当前子任务之间存在可复用接口时，建立 socket 连接。
```

这三种行为不是一次性分类器，而是运行时可连续发生的结构变换。例如复杂工程任务常见链路是：

```text
先向上同构，确定真正目标边界；
再交叉融合，召回历史经验或已有 skill；
最后向下分形，把新增黑盒部分拆成可执行单元。
```

## 5. 迁移后的控制原则

HoLO 不是把 HMF 写进一段 system prompt，而是把 HMF 的结构约束变成 runtime 控制权：

```text
Agent proposes.
HoloMesh validates.
BoundaryGate constrains.
Runtime executes.
Memory learns over time.
```

Agent / LLM 可以提出计划、调用工具建议、subunit 分工建议或 memory 融合建议，但不能独占组织行为的裁决权。HoloMesh 要判断这些提案是否闭合、是否过度分形、是否边界错误、是否值得融合、是否越权、是否产生不可控成本。

这就是 HMF 到 HoLO 的核心迁移：从“用元算法分析问题”，迁移为“用元算法约束 agent runtime 的组织行为”。
