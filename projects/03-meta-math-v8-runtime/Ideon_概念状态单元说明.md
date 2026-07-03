# Ideon 概念状态单元说明

`ideon` 是 META-MATH 路线中用于替代普通 token / graph node 的概念状态单元。它不是把一个词换个名字，也不是只给概念加一个 embedding，而是把一个概念在运行时中的身份、位置、状态、边界和时间一起封装起来。

可以把它理解为：

```text
Ideon =
  concept identity
  semantic pointer
  wave-packet state
  topology position
  internal time state
  runtime metadata
```

## 1. 为什么不是 token

token 更适合表达文本序列中的局部片段。它的主要位置来自序列索引，主要运动来自 attention 中的上下文混合。

ideon 试图表达的是另一类对象：

```text
这个概念在当前问题世界中是什么？
它处在什么结构位置？
它是材料、边界、硬约束，还是空洞？
它是否稳定？
它是否应该继续演化、沉淀、坍缩或被代谢掉？
```

所以 ideon 不只服务于语言续写，而是服务于 guarded runtime 中的结构推进。

## 2. 基本结构

一个 ideon 可以粗略拆成四类信息：

```text
V / Content:
  概念内容、相位状态、语义质量。

P / Pointer:
  可被人类、agent、report 和 goal evaluation 引用的概念指针。

M / Metadata:
  稳定性、熵、质量、边界状态、参与关系等运行元数据。

T / Temporal:
  ideon 内部封装的时间状态，也可以理解为局部时间 tau。
```

其中 `T` 是 ideon 和普通 token / 静态节点的重要区别之一。

## 3. 内封时间：时间作为 ideon 的内部属性

在 META-MATH 的语境中，时间不只是一条全局 step 轴，也不只是文本中出现的时间词。更关键的是：每个概念状态自身也有演化节奏。

因此，ideon 将时间封装为内部状态：

```text
global runtime step:
  window_1 -> window_2 -> window_3

ideon internal time:
  ideon_a.tau: 0.2 -> 0.7 -> 1.4
  ideon_b.tau: 5.1 -> 5.1 -> 5.2
  ideon_c.tau: unstable -> decayed -> removed
```

这意味着不同 ideon 不一定共享同一种时间速度。某些概念会快速膨胀，某些概念会保持稳定，某些概念会衰减，某些概念会在窗口中被暂时冻结或沉淀。

内封时间的作用主要有四点：

1. **避免把所有概念压到同一个全局时钟里。**  
   真实问题中，证据、约束、目标、风险和候选方案的变化速度并不相同。

2. **让概念可以拥有生命周期。**  
   一个 ideon 可以出生、展开、稳定、退相干、沉淀、坍缩或被代谢掉。

3. **让 window / container / transition 有状态依据。**  
   runtime 不是只看一轮文本输出，而是观察 ideon 在若干窗口内是否稳定、是否偏离目标、是否触发升级容器或重播种。

4. **把“慢思考”从叙事变成运行状态。**  
   System 2 不只是让 LLM 多写几步推理，而是让概念状态在受限窗口里演化，再由 guard 判断是否拥有解释权。

## 4. 内封时间与 guarded runtime

V8 中的 window / container / transition 可以理解为外部运行时框架；ideon 的内部时间则是每个概念单元自己的局部演化记录。

二者关系大致是：

```text
window:
  给一组 ideon 一个有限运行周期。

container:
  保存当前 ideon graph、资源边界和运行上下文。

transition:
  根据窗口结果决定继续、冻结、升级容器、重播种或停止。

ideon.tau:
  记录单个概念状态在这些窗口中的局部时间和演化阶段。
```

如果一个候选概念只是在 LLM 输出中出现过，但没有在 runtime 中形成稳定 ideon 状态，它就只能算候选表达；如果它在多个窗口中保持稳定、与目标和边界一致，并通过 guard，它才更接近可解释的结构结果。

## 5. 与五维波包的关系

五维波包是 ideon 的状态描述方式之一。它不是为了把概念神秘化，而是为了让 ideon 不再只是一个静态标签。

可以粗略理解为：

```text
semantic identity:
  这个 ideon 是什么。

wave-packet state:
  它在当前结构空间中的位置、扩张度、稳定性、相位和张力。

internal time:
  它自己的演化节奏、年龄、冻结状态或衰减状态。
```

也就是说，五维波包描述 ideon “在哪里、如何变化、是否稳定”；内封时间描述 ideon “以什么节奏变化、变化到了哪个阶段”。

## 6. 当前边界

这里的 ideon 说明用于解释 META-MATH-V8 的数据单元设计，不等同于完整 Concept World 实现。 Concept World 属于 V8 之后的下一轮重构方向，不计入 V8 已完成范围。

V8 中更准确的说法是：ideon、五维波包、window、container、transition 和 guard 共同构成了一个外部 System 2 的架构原型。ideon 提供概念状态单元，guarded runtime 决定这些状态是否能获得最终解释权。
