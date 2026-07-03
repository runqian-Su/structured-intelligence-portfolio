# HoLO 架构说明 / Architecture Note

HoLO 是一个以 HoloMesh 为核心的结构演算运行时。agent、skill、tool、memory 都不是最高主体，而是被 HoloMesh 组织、裁决和约束的资源。

## 1. 系统定位

普通 agent 框架通常以模型为中心：

```text
LLM / Agent
  -> plan
  -> call tools
  -> observe
  -> continue
```

HoLO 以结构为中心：

```text
HoloMesh
  -> 判断 Source / Sink
  -> 维护 HoloUnit 网络
  -> 裁决 TransformChain
  -> 管理 HoloMemoryGraph
  -> 约束 agent / skill / tool
  -> 写入 runtime snapshot
```

因此，HoLO 更接近一个 Structure Runtime for Agentic Systems：它不追求让 agent 自由扩张，而是让 agent 的行为被放进可追踪、可闭合、可复盘的结构网络。

## 2. 核心组件

```text
HoloMesh:
  运行时主控层，维护 HoloUnit 网络、socket 拓扑、transform chain 和调度顺序。

HoloUnit:
  Source -> Sink 的局部闭合单元，持有目标、约束、证据、状态、可用资源和 memory 引用。

HoloTransform:
  改变 HoloMesh 拓扑的组织行为，包括 downward_fractal、upward_isomorphism、cross_fusion。

HoloMemoryGraph:
  结构化长期记忆。它记录历史任务如何拆解、如何裁决、哪些路径有效，而不只是保存聊天记录。

DecisionFrame:
  对 agent / LLM 提案进行裁决的记录，包含 accepted / rejected / delayed / rewritten transform。

BoundaryGate:
  工具、记忆写入、subunit 执行和外部动作的边界裁决器。

SubUnitRuntime:
  由 parent HoloUnit 分配目标、证据和边界的局部执行资源，不是自由对话模型。
```

## 3. 分层结构

HoLO 可以分为五层：

```text
L0 用户输入层
L1 HoloMesh 主控层
L2 HoloUnit 元控层
L3 Agent / Skill / Tool 执行层
L4 HoloMemoryGraph 记忆层
L5 Workbench / CLI / Daemon / MCP / VSIX 外壳
```

L4 不是简单下游日志，而是会重新参与未来任务的结构记忆；L5 不是 HoLO 本体，只是 runtime 的交互外壳。

## 4. 与普通 code-agent 的差异

HoLO 关注的问题不是“agent 怎么多调几次工具”，而是：

```text
任务是否形成 Source / Sink 闭合？
当前 unit 是否已经足够原子？
是否需要向下分形为 child units？
是否需要向上同构修正任务边界？
是否应当从历史 memory 交叉融合可复用结构？
agent / skill / tool 是否被约束在 parent unit 的目标和证据内？
工具调用是否经过 BoundaryGate？
执行结果是否能回写成可复用结构记忆？
```

这使 HoLO 更适合长程工程任务：它把一次任务的组织过程显式化，让后续任务可以复用“过去如何拆解、如何裁决、哪些路径有效”的结构经验。

## 5. BoundaryGate 与权限边界

HoLO 中 agent 没有直接越过边界执行动作的权力。工具调用、记忆写入、subunit 执行都需要经过 BoundaryGate。

典型边界模式包括：

```text
preview:
  只预览 tool / memory / subunit，不产生真实副作用。

safe:
  允许受控 mock tool 或受限 memory write，拒绝未知工具和越界操作。

permissive:
  允许更多 mock subunit 或受控工具，但仍不等于任意 shell / 文件写入 / 网络访问。
```

BoundaryGate 的意义不是“保守”，而是让 agent runtime 的每一次副作用都有结构来源、裁决记录和审计路径。

## 6. 当前实现边界

HoLO 已完成多阶段 runtime 验证，包括 mock runtime chain、BoundaryGate、HoloMemoryGraph、readonly tool adapter、skill registry、mock SubUnitRuntime、Runtime API、daemon、MCP adapter、CLI Workbench、snapshot viewer 和 VSIX contract 等阶段性能力。

当前更准确的结论是：HoLO 已经把 HMF 的 Source / Sink / HoloUnit / transform chain 迁移成了 code-agent runtime 的结构控制层，并形成了可运行、可审计、可继续扩展的系统骨架。它还不是成熟 IDE agent 替代品，也不声称已经拥有完整 Claude-Code 工具能力。
