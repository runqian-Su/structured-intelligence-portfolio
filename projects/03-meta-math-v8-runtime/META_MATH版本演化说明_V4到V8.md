# META-MATH 版本演化说明：V4 到 V8 / Version Evolution

## 1. 版本关系

META-MATH-V8 是 META-MATH 路线中的第八个大版本。它不是 V4 的简单重命名，也不是说 V4 已经被完全替代。

更准确的关系是：

- **V4 是日常更常使用的工程分支。** 它以 Rust 实现为主，重点是把概念图、ideon、morphism、HMF flow、Pomegranate sheaf、代谢审计、因果混沌和 Ouroboros 递归收敛做成可运行底座。
- **V8 是更上层的 guarded runtime 架构探索分支。** 它把前序版本中的概念图运行、状态演化和结构审计，提升为 agent-in-loop 的慢思考运行机制。

因此，V4 更像“概念图物理与结构演算底座”，V8 更像“面向 LLM/agent 的外部 System 2 runtime”。

需要单独说明：`Concept World` 是 V8 之后下一轮重构版本的开头，不属于 V8 已完成范围。V8 的版本说明不把 Concept World 纳入成果主体。

## 2. V4 的工程侧重

V4 的核心结构可以概括为：

```text
input graph
  -> Ideon / Morphism 图结构
  -> StateVector 推进
  -> HMF flow
  -> Pomegranate sheaf restoration
  -> causal-chaos empowerment evaluation
  -> metabolism audit
  -> Ouroboros fixed-point iteration
  -> output report
```

V4 中比较关键的模块包括：

- `state_vector`：维护图状态、路径、拓扑空洞、熵池、相干性、信息流形和 Gibbs energy。
- `ideon_orb`：把概念表示为五维复波包，携带位置、速度、sigma、psi、phase、稳定性和局部时间。
- `hmf_rewriting`：实现向下分形、向上封装、边升格为对象、跨域融合等结构改写操作。
- `causal_chaos`：评估边的双向互赋，区分 StrongEffective、WeakEffective、Untested 和 Invalid。
- `metabolism_audit`：计算 Gibbs free energy，并通过压缩、剪枝和 sink 操作清理低效结构。
- `pomegranate_sheaf`：检查局部覆盖、相位一致性、约束空洞和拓扑 void。
- `ouroboros_loop`：根据误差和熵差决定结构修正、阻力调整或收敛停止。

这些模块说明 V4 已经不是纯文档设想，而是围绕概念图状态推进形成的运行底座。

## 3. V8 的架构侧重

V8 继承了 V4 中的几个核心问题：

- 概念不能只作为 token 或静态标签存在。
- 复杂任务需要显式表示边界、阻力、稳定性、空洞和反馈。
- 候选结构需要经过运行状态校验，而不是只看语言是否流畅。

但 V8 的重点不再只是“图如何推进”，而是进一步问：

```text
LLM 参与以后，谁负责播种？
谁观察窗口？
谁决定当前结构是否稳定？
谁判断是否进入下一窗口？
谁拥有最终解释权？
```

所以 V8 引入了更明确的 runtime 对象：

- **Seeder / 播种器**：把外部问题变成 runtime 初始条件。
- **Window / 运行窗口**：限制一次观察和演化周期。
- **Container / 容器**：保存当前合法概念域、边界和规则。
- **Transition / 跃迁监督**：决定固化、升级、回退、重播种或停止。
- **Goal evaluation / 目标评估**：判断当前状态是否对齐任务目标。
- **Guard / 解释权控制**：决定输出是候选、中间状态，还是可以成为正式解释。

## 4. V4 与 V8 的差异

| 维度 | V4 | V8 |
| --- | --- | --- |
| 主要定位 | 日常更常使用的 Rust 概念图运行分支 | 第八个大版本，面向 agent-in-loop 的 guarded runtime 架构 |
| 核心对象 | Ideon、Morphism、StateVector、Graph、Sheaf、Ouroboros loop | Ideon、五维波包、Window、Container、Transition、Guard |
| 运行方式 | 输入概念图，推进状态，输出结构报告 | LLM/agent 播种和观察，runtime 负责结构推进与解释权控制 |
| 关注重点 | 图状态、流动、拓扑空洞、熵、相干性、收敛 | 外挂式 System 2、受限解释、目标评估、可追溯输出 |
| 成熟度 | 更接近日常可运行工程底座 | 架构原型和上层 runtime 设计 |

## 5. 为什么需要 V8

V4 能推进概念图，但当系统进入 LLM/agent 场景后，只靠底层图模拟还不够。agent 会带来新的问题：

- 输入来自自然语言，初始状态如何播种？
- LLM 生成的候选关系如何进入结构校验？
- 某一轮输出到底是候选、观察报告，还是最终解释？
- 长链路任务中，什么时候应该继续、回退、固化或停止？
- 如何避免 LLM 直接用语言流畅性覆盖 runtime 的结构判断？

V8 的意义就在这里：它把 V4 中的概念图演化经验，扩展为一个面向 LLM/agent 的解释权控制系统。

## 6. 当前公开边界

这个作品集只公开 V8 的架构说明、运行逻辑和图示，不公开 V4 / V8 的完整源码、私有运行快照或大规模中间报告。这里更重要的是说明版本迭代关系：

```text
V4: 概念图运行与结构演算底座
V8: ideon、五维波包、外挂式 System 2 与 agent-in-loop guarded runtime
Concept World: V8 之后的下一轮重构方向，不计入 V8 已完成范围
```

这条路线共同指向同一个问题：概念、自然语言和复杂任务如何进入可计算、可追溯、可边界控制的结构运行过程。
