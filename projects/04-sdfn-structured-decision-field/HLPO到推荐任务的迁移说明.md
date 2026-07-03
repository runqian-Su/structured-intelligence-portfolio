# HLPO 到推荐任务的迁移说明

HLPO 最早来自元引擎中“交互”问题的延伸：在多个对象、概念或状态同时存在时，系统不应只计算“谁和谁相似”，还要判断“谁应该参与这次决策、哪条关系值得被放大、哪些路径应该被弱化”。在早期 LLM attention A/B 测试中，这个问题被落到 HardGate sparse attention 上，验证的是结构化选择能否在可控训练规模下接近 dense attention 的语言建模效果。

SDFN 将 HLPO 迁移到了推荐排序，但迁移的不是具体任务形态，而是同一个结构问题：在一个推荐样本内部，用户、物品、上下文、行为序列和反馈信号并不总是同等重要，也不应该无差别交互。模型需要学习哪些 token 和 edge 对当前排序判断更有贡献。

## 1. 从 token attention 到样本内结构路由

在 LLM 场景中，HLPO 面对的是 token 序列中的注意力选择；在 SDFN 中，HLPO 面对的是样本内部的结构 token 和 decision field graph。

```text
LLM / attention 验证:
  token -> attention edge -> sparse / dense propagation -> language modeling loss

SDFN / recommendation:
  structure token -> decision field edge -> HLPO structural routing -> ranking score
```

两者的共同点是：都不是简单做后处理剪枝，而是在主干计算中学习结构参与质量。

## 2. 为什么推荐任务需要 HLPO

推荐排序中的交互关系非常稠密。用户长期偏好、候选物品、当前上下文、历史行为片段和反馈信号都可能影响最终判断。如果让所有结构 token 无差别传播，模型会退化成高成本的全量混合；如果只依赖固定规则筛选，又会失去样本级自适应能力。

HLPO 在 SDFN 中提供的是 learned sparse structural routing：

```text
哪些 token 进入主要传播路径？
哪些 edge 保留较高可见性？
哪些关系在当前样本中被削弱？
结构开放程度是否随训练阶段和样本状态变化？
```

这种选择发生在模型内部，参与训练，也参与推理。它不是额外的解释层，也不是对最终结果的事后说明。

## 3. Fullpath 与 HardSkip

SDFN 中的 HLPO 可以对应两种推理路径：

```text
Fullpath:
  使用较完整的结构传播路径，保留更多 token / edge 参与。

HardSkip:
  使用更硬的结构选择，跳过低参与关系，使更大图规模在推理约束下可执行。
```

在 256L6 / GraphExpand48 上，Fullpath 和 HardSkip 的官方 AUC 基本持平：

```text
256L6 fullpath official AUC : 0.818115
256L6 hardskip official AUC : 0.818163
```

这说明 HardSkip 没有明显破坏排序质量。在更大的 384L8 / GraphExpand64 上，Fullpath 无法稳定满足最终轮推理时间约束，HardSkip 成为可完成大图推理的路径：

```text
384L8 hardskip official AUC : 0.825194
```

## 4. true HardSkip 的执行含义

384L8 推理包中的 true HardSkip 可以概括为：

```text
well token 必更新；
根据 HLPO token_keep_score 从非 well token 中选择 top support tokens；
reader 层只对 active token 计算 Q 和 FFN 更新；
全部 token 仍作为 K/V 背景被 active token 读取；
非 active token 不做层更新。
```

默认配置：

```text
k_well = 16
inference_hlpo_support_tokens = 24
active token count = 40
total token count = 64
```

这意味着 HardSkip 不是“把图直接砍小”，而是保留全部 token 的背景可读性，同时只更新由 HLPO 选出的 active token。它是把训练中的结构参与信号转成推理侧执行路径的一种方式。

## 5. 当前边界

HLPO 在 SDFN 中不等于稳定的端到端加速结论。当前图规模下，dense attention 在硬件上仍可能有较好的执行效率；如果只做 active-token update，但仍保留大量 K/V 背景，再引入 top-k、index_select、scatter 等操作，实际速度收益可能被调度和访存开销抵消。

因此，当前更准确的结论是：HLPO 的结构参与选择已经能在推荐任务中作为主干机制工作，并且在更大图规模下提供可执行的 HardSkip 推理路径。后续如果要把它发展成明确的速度收益，需要继续做 token-level compact、top-k edge sparse gather 或 block-sparse attention。
