# HoLO 运行时逻辑与状态机图示 / Runtime Diagrams

## 1. Runtime 总链路

```mermaid
flowchart TD
    A[用户任务] --> B[HoloMesh Orchestrator]
    B --> C[编译 Root HoloUnit]
    C --> D{Source / Sink 是否闭合}
    D -- 否 --> E[追问 / 补全 / 拒绝进入执行]
    D -- 是 --> F[生成 HoloMesh]
    F --> G[召回 HoloMemoryGraph]
    G --> H[生成 Transform 候选]
    H --> I[DecisionFrame 裁决]
    I --> J{BoundaryGate}
    J -- 拒绝 --> K[记录 rejected / blocked]
    J -- 允许 --> L[Agent / Skill / Tool / SubUnitRuntime]
    L --> M[Runtime Events]
    M --> N[Run Snapshot]
    N --> O[Memory Writeback]
    O --> P[HoloUnit 网络更新]
```

## 2. HoloMesh 裁决逻辑

```mermaid
flowchart TD
    A[Agent / LLM 提案] --> B[闭合性判断]
    B --> C{Source / Sink 匹配}
    C -- 否 --> C1[dangling_source / dangling_sink / unit_mismatch]
    C -- 是 --> D[原子性判断]
    D --> E{是否需要向下分形}
    E -- 是 --> E1[downward_fractal 候选]
    E -- 否 --> F[边界判断]
    F --> G{是否需要向上同构}
    G -- 是 --> G1[upward_isomorphism 候选]
    G -- 否 --> H[接口判断]
    H --> I{是否存在可融合 memory / skill / unit}
    I -- 是 --> I1[cross_fusion 候选]
    I -- 否 --> J[成本收益判断]
    E1 --> J
    G1 --> J
    I1 --> J
    J --> K[风险与 BoundaryGate 预裁决]
    K --> L{DecisionFrame}
    L -- accepted --> M[进入 runtime execution]
    L -- rejected --> N[记录拒绝原因]
    L -- delayed --> O[等待证据 / 用户确认]
    L -- rewritten --> P[改写 transform 后继续裁决]
```

## 3. HoloUnit 状态机

```mermaid
stateDiagram-v2
    [*] --> dormant
    dormant --> active: 被 HoloMesh 激活
    active --> validating: 检查 Source / Sink / boundary
    validating --> blocked: 缺少闭合 / 权限不足 / 证据不足
    validating --> executing: DecisionFrame accepted
    blocked --> active: 用户补充 / memory 补全 / 边界改写
    executing --> waiting: 等待 tool / agent / subunit 结果
    waiting --> executing: 结果返回，继续执行
    executing --> unstable: 发现边界错误 / 成本失控 / transform 冲突
    unstable --> active: 向上同构 / 向下分形 / 交叉融合后重组
    executing --> complete: Sink 达成
    executing --> terminated: BoundaryGate 拒绝或用户终止
    complete --> archived: 写入 HoloMemoryGraph
    terminated --> archived: 写入失败与边界记录
    archived --> [*]
```

## 4. SubUnitRuntime 边界

```mermaid
flowchart LR
    A[Parent HoloUnit] --> B[SubUnit Assignment]
    B --> C[目标]
    B --> D[约束]
    B --> E[证据引用]
    B --> F[允许工具]
    B --> G[BoundaryGate]
    G -- 允许 --> H[SubUnitRuntime]
    G -- 拒绝 --> I[blocked / preview only]
    H --> J[SubUnitResult]
    J --> K[Parent HoloUnit 汇总]
    K --> L[DecisionFrame 更新]
```

SubUnitRuntime 的关键边界是：subagent 不是自由对话模型，而是由 parent HoloUnit 分配目标、证据、工具范围和退出条件的局部执行资源。

## 5. Memory 回写逻辑

```mermaid
flowchart TD
    A[Runtime Run] --> B[Runtime Events]
    A --> C[DecisionFrame]
    A --> D[Boundary Decisions]
    A --> E[Tool / Skill / SubUnit Results]
    B --> F[Run Snapshot]
    C --> F
    D --> F
    E --> F
    F --> G{是否允许写入 memory}
    G -- 否 --> H[只保留 no-write 输出]
    G -- 是 --> I[HoloMemoryGraph]
    I --> J[未来任务 recall]
    J --> K[cross_fusion 候选]
```

HoLO 的 memory 不是简单聊天历史，而是结构化运行经验：任务如何闭合、如何拆解、哪些 transform 被接受或拒绝、哪些工具和 subunit 路径有效、哪些边界曾经阻塞。
