# HoLO Agent Runtime / 面向 Code-Agent 的结构演算运行时

HoLO 是一个面向 code-agent 的结构演算运行时，用于处理长程任务解构、结构化长期记忆、subagent 边界控制和工具调用审计。

它的出发点不是“让 agent 更自由地调用工具”，而是反过来问：

> 一个 code-agent 的任务、工具、记忆、subagent 和副作用，能不能被放进可闭合、可裁决、可复盘的结构网络？

普通 agent 链路通常是：

```text
user task -> main model -> tool call -> result
```

HoLO 链路是：

```text
user task
  -> HoloMesh controller
  -> HoloUnit network
  -> BoundaryGate / DecisionFrame
  -> agent / skill / tool / subunit resources
  -> runtime events / snapshot
  -> memory write-back
  -> HoloUnit network update
```

## 主要文档 / Main Documents

- [HoLO_架构说明.md](HoLO_架构说明.md)
- [HMF到HoLO的元算法迁移说明.md](HMF到HoLO的元算法迁移说明.md)
- [diagrams/state-machine.md](diagrams/state-machine.md)
- [evidence/第一阶段SaaS场景测试摘要.md](evidence/第一阶段SaaS场景测试摘要.md)
- [evidence/Phase3-6_Runtime_Snapshot摘要.md](evidence/Phase3-6_Runtime_Snapshot摘要.md)

## 核心组件 / Core Components

- **HoloMesh**：runtime controller，维护 HoloUnit network、socket topology、transform chain 和 scheduling。
- **HoloUnit**：局部 Source -> Sink 闭合单元，包含 goal、constraints、evidence、state、resources 和 memory references。
- **HoloTransform**：改变拓扑的组织行为，如 downward fractal、upward isomorphism、cross fusion。
- **DecisionFrame**：记录 accepted、rejected、delayed、rewritten transforms。
- **BoundaryGate**：对 tool、memory write、subunit execution 和外部副作用进行边界裁决。
- **HoloMemoryGraph**：结构化长期记忆，记录历史任务如何拆解、如何裁决、哪些路径有效。
- **SubUnitRuntime**：由 parent HoloUnit 分配目标、证据和边界的局部执行资源。

## 当前验证 / Current Evidence

HoLO 已经形成多阶段 runtime skeleton：

```text
Phase 3:
  BoundaryGate2, HoloMemoryGraph, session / compact memory, controlled write audit.

Phase 4:
  ToolAdapter2, PatchPreview2, controlled file write, verification tool, memory writeback.

Phase 5:
  Runtime API, local daemon, CLI local API, MCP adapter, session / remote boundary.

Phase 6:
  CLI Workbench, snapshot viewer, approval protocol, static workbench viewer, VSIX contract.
```

第一阶段 SaaS 场景测试中，5 个后端诊断问题均完成 bare DeepSeek baseline 与 HoLO Runtime 对照。HoLO 的主要增益不是更会“给业务建议”，而是把诊断任务转成 HoloUnit / HoloMesh 结构，并记录 BoundaryGate、DecisionFrame、runtime snapshot 和 rejected capabilities。

当前边界也很明确：第一阶段 HoLO-derived diagnostic report 仍然偏结构化，业务建议字段还不完整；PatchPreview 对诊断任务有时会形成额外 friction。因此这里不把 HoLO 描述为成熟 IDE agent 替代品，而是一个已经能跑通结构控制链、可审计 snapshot 和边界裁决的 agent runtime 骨架。

## 公开边界 / Public Scope

本目录只保留架构说明、状态机图示、场景测试摘要和 snapshot 摘要。不包含源码、真实密钥、完整 runtime JSON、内部日志、受控写入 artifact 或私有运行数据。
