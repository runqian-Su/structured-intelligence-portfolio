# Agent-in-loop Runtime 说明 / Runtime Logic

## 1. 为什么是 agent-in-loop

META-MATH-V8 不是让 agent 直接求解问题，而是让 agent 参与一个受限循环：

```text
现实材料进入
  -> ideon / 波包状态初始化
  -> runtime 在窗口内推进
  -> agent 观察窗口状态
  -> LLM 辅助解释或提出候选
  -> guard 判断是否允许输出、跃迁或重播种
```

这里的关键是：agent 在 loop 中，但 agent 不是 loop 的唯一主语。系统真正运行的对象是 ideon 状态、结构关系、窗口边界和 guarded transition。

## 2. 三个角色

### 播种器 / Seeder

播种器负责把外部输入变成 runtime 的初始展开条件。它不应该暗示答案，而是定义：

- 观测视角
- 概念域
- 初始张力
- 禁止模式
- 演化边界
- 目标吸引子

### 运行协调器 / Runtime Coordinator

运行协调器负责驱动 runtime。它调度 window、container、transition、goal evaluation 和 guard，不直接替代底层演化。

它关注的问题包括：

- 当前窗口是否已经稳定
- 是否出现 dominant kernel
- 是否触发 rewrite boundary
- 是否需要容器升级
- 是否应该 retry、freeze、halt 或进入下一窗口

### 受限解经器 / Constrained Interpreter

受限解经器负责把运行状态解释成人类可读语言。它必须读取窗口状态、跃迁结果和目标评估，不能凭 LLM 的语言流畅度直接输出结论。

受限解经器可以输出：

- 当前运行状态说明
- 已形成的稳定结构
- 尚未解决的冲突
- 下一步可观察路径
- 通过 guard 的最终解释

## 3. Window / Container / Transition

### Window

`window` 是一次有限观察和演化周期。它记录某一阶段的运行状态，例如：

- dominant kernel
- 当前冲突或阻塞
- rewrite boundary
- 窗口状态：critical / exhausted / solidified / deadlocked
- 本窗口是否允许继续推进

### Container

`container` 是当前结构运行的反应容器。它保存：

- 静态规则
- 合法概念域
- 当前阶段边界
- 允许的演化方式
- 被固化或沉淀的结构

### Transition

`transition` 决定窗口结束后系统如何进入下一阶段：

- 固化当前主导核
- 容器升级
- 回退重试
- 外部反馈修正
- 停止输出

它的作用是避免系统在临界状态下只是简单失败或无限重跑，而是把临界变成下一层结构的入口。

## 4. Guarded Runtime

Guarded runtime 的作用不是给结果“盖章”，而是控制解释权。

一个输出要成为正式解释，需要至少经过：

```text
window report
transition report
container snapshot
goal evaluation
guard 判定
```

如果没有通过这些环节，结果只能是候选、草稿、观察状态或中间解释。

## 5. LLM 在 loop 中做什么

LLM 的职责是提供材料和表达能力：

- 从自然语言中提出候选概念
- 补全隐含关系和背景常识
- 辅助估计阻力、意图、风险和行动候选
- 解释反馈中的熵差来源
- 把内部结构转译为自然语言

但 LLM 不做最终裁决：

- Sheaf / Completion 的一致性不由 LLM 直接决定。
- Laplace 的行动可行性不由 LLM 直接决定。
- Ouroboros 的最终误差修正不由 LLM 直接决定。
- final 是否成立不由 LLM 语言流畅度决定。

## 6. 输出边界

可以把 META-MATH-V8 理解为一个外挂式 System 2：

- LLM 是世界知识和语言能力。
- Runtime 是慢思考的结构控制层。
- Agent 是窗口观察者和受限解释者。
- Guard 决定什么可以被当作正式解释。
