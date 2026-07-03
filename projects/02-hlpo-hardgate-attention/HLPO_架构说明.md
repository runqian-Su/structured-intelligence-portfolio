# HLPO 架构说明：从交互注意力到结构参与场

## 1. 背景

HLPO 最早来自元引擎中的“交互”问题：概念不是孤立存在的，意义来自概念之间的信息传递、作用方向、边界约束和反馈修正。

标准 Transformer attention 会计算 token 两两之间的关系。这个设计很强，但也很贵：序列越长，attention connections 越多，计算量和显存压力都会迅速上升。

HLPO 的出发点是：并不是所有 token relation 都同等重要。很多时候，真正参与当前预测、推理或决策的，可能只是其中一部分高质量连接；其他连接虽然存在，但不一定都应该进入主要计算路径。

因此，HLPO 不把 attention 看成必须全连接的矩阵，而是把它看成一个可以被选择的 **结构参与场**。

## 2. HLPO 名称来源

HLPO 名称来自 H / L / P / O 四个元算法：

- **HMF**：提供结构拓扑、源-汇关系和流动路径。
- **Laplace**：提供意图场、摩擦、惯性和干预成本。
- **Pomegranate**：提供有限概念空间、挤压、沉积与边界穿刺。
- **Ouroboros**：提供递归反馈和自我修正。

这不是把四个元算法机械拼接进一个 attention layer，而是把它们作为“如何判断一个 relation 是否值得参与计算”的四类结构视角。

## 3. 核心想法

HLPO 的核心想法可以概括为一句话：

> 让模型只把高质量的 token relation 放进主要计算路径。

在最小验证版本里，这件事通过 HardGate sparse attention 完成：

```text
attention score
  -> relation quality / mass measurement
  -> 根据 permeability 选择 top-k relation
  -> 非 top-k relation 被 mask
  -> softmax
  -> value aggregation
```

如果 `permeability = 1.0`，它接近 full attention；如果 `permeability = 0.5`，它只允许约一半连接进入 attention 计算。

当前 A/B 版本里的 quality / mass measurement 是完整 HLPO 质量模型的最小近似：先计算 masked attention logits，再用其强度作为 relation quality proxy，最后对高质量 relation 做 top-k 保留。完整 HLPO 方向中，这一步会进一步引入 Laplace Mass、结构边界和反馈调节。

## 4. 为什么不是普通剪枝

普通剪枝通常是在模型训练后删除参数、通道或结构。HLPO 更关注训练过程中的结构参与：

```text
哪些连接应该参与当前计算？
模型能否在低连接保留率下继续学习？
稀疏结构能否成为训练动力学的一部分？
```

因此，HLPO 不是“把模型砍小”，而是让模型在训练中适应更低的 attention connectivity。

## 5. 完整研究架构

HLPO 的完整研究架构可以理解为几个相互衔接的部分：

```text
HMF Structure:
  判断 token / concept 之间是否存在有效结构关系。

Laplace Field:
  判断当前意图场、摩擦和干预成本。

Pomegranate Boundary:
  判断有限概念空间中的挤压、空腔和边界穿刺。

Kinetic Attention:
  让信息沿被允许的关系流动。

Ouroboros Feedback:
  根据训练状态调节结构开放程度。
```

这些层次描述的是 HLPO 的完整研究方向。当前公开作品集重点展示的是其中最先被验证的一条路径：HardGate sparse attention。

## 6. 与 A/B 测试的关系

A/B 测试中使用的是更简化、更容易验证的版本：

```text
Dense baseline:
  所有 attention connections 进入计算。

HLPO HardGate:
  先计算 relation quality / mass，再只保留 top-k attention connections。
```

这个版本的价值在于，它把问题压到最清楚：

> 只保留一半 attention 连接，训练是否还能接近 Dense？

实验结果显示，在当前设置下答案是“可以接近”：HLPO final loss 只比 Dense 高 0.0836。

## 7. 边界

当前可以说：

- HLPO HardGate 已经完成 Dense vs Sparse A/B 验证。
- 测试规模为约 100M tokens / 6000 steps。
- 硬件环境为 Mac Studio（M2 Ultra, 64GB unified memory）/ PyTorch + MPS。
- 50% sparse attention 下，final loss gap 为 0.0836。
- loss 曲线和权重演化材料显示训练过程可复盘。

当前不应该说：

- HLPO 已经在所有 LLM 和所有任务上验证。
- HLPO 已经完全替代 dense attention。
- Python 层 top-k sparse simulation 就等于最终推理加速。
- 硬件/RTL 探索已经等于商用硬件落地。
