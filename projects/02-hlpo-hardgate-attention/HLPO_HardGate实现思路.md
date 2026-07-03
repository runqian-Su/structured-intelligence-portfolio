# HLPO HardGate 实现思路

## 1. 最小验证路径

HLPO 的完整目标是把“交互注意力”中的结构选择机制落到模型计算里。第一条可验证路径不是重写整个模型，而是在已有 Transformer attention 内部加入 HardGate。

这样可以把问题压缩到最清楚的一点：

> 保留更少 attention connections 时，训练还能不能稳定？

原有 attention 仍然保留：

```text
Q / K / V projection
RoPE 或位置编码
attention mask
softmax
value aggregation
```

HLPO 增加的是中间这几步：

```text
attention score
  -> relation quality / mass measurement
  -> top-k keep
  -> non-top-k mask
```

也就是说，HardGate 不是随便删除一半连接，而是先对 token relation 计算一个“参与质量”，再让高质量 relation 进入后续 attention 路径。这样 Dense 和 HLPO 的差异足够集中，A/B 测试也更干净。

## 2. 计算流程

HLPO HardGate 的简化流程如下：

```text
1. 输入 hidden states。
2. 计算 Q、K、V。
3. 得到 masked attention scores。
4. 计算 relation quality / mass。
5. 根据 field_permeability 计算每个 token 保留多少 relation。
6. 只保留 top-k quality 对应的 attention scores。
7. 其他 scores 置为 -inf。
8. 对稀疏后的 scores 做 softmax。
9. 用 sparse attention probabilities 聚合 V。
```

对应直觉：

```text
Dense attention:
  每个 token 读取所有可见 token。

HLPO attention:
  每个 token 只读取当前最有参与价值的一部分 token。
```

## 3. 质量计算 / Mass Measurement

HardGate 的关键不是“按位置保留一半 token”，也不是随机 dropout，而是基于 relation 的参与质量进行筛选。

在完整 HLPO 设想中，这个质量可以理解为一种 **Laplace Mass**：一个 token relation 在当前意图场、结构场和局部上下文中是否具有足够的参与价值。它对应的是：

```text
relation 是否和当前预测有关？
relation 是否具有足够作用强度？
relation 是否穿过当前结构边界？
relation 是否值得进入主要计算路径？
```

在当前 A/B 测试的最小实现中，没有引入完整的可学习 Laplace Mass 模块，而是采用了更直接、可控的近似：

```text
QK^T / sqrt(d)
  -> 加 causal / attention mask
  -> 以 masked attention logits 的 magnitude / score 作为 relation quality proxy
  -> top-k quality selection
  -> hard mask
```

原始实现中对应的逻辑可以概括为：

```text
Mass Measurement:
  使用 softmax 前 attention logits 的强度作为质量近似。

Hard Mass Filter:
  只让高质量 relation 通过 gate。
```

因此，当前验证的不是完整 HLPO 质量模型，而是一个简化问题：

> 如果用 attention logits 的强度近似 relation quality，并只保留 top-k 高质量连接，模型还能不能稳定训练？

这个最小近似很重要，因为它把质量计算、结构筛选和训练稳定性放进了同一个可控 A/B test。

## 4. Permeability 的作用

`field_permeability` 控制 attention 连接保留比例：

```text
field_permeability = 1.0
  接近 full attention。

field_permeability = 0.5
  只保留约 50% attention connections。
```

在训练中，可以让 permeability 从较开放逐步收紧，使模型不是突然被切掉一半连接，而是在训练过程中适应稀疏结构。

## 5. 为什么要做 A/B Test

HLPO 的关键不是“设计了一个门控”，而是这个门控放进训练后是否还成立。

因此 A/B test 要保持：

```text
Run A: Dense baseline
Run B: HLPO HardGate
Hardware: Mac Studio, M2 Ultra, 64GB unified memory
Framework: PyTorch + MPS
Model: HLPO-0.5B
Data: FineWeb-Edu streaming
Context length: 512
Batch size: 32
Training scale: ~100M tokens / 6000 steps
相同步数
相同基础训练设置
对比 final loss 和 loss curve
```

如果 HLPO loss 迅速发散，就说明 hard gate 破坏了学习过程；如果 loss 曲线接近 Dense，就说明稀疏参与有进一步研究价值。

## 6. 为什么保留图和过程证据

公开作品集保留图片，而不是只写一句结论：

- `loss_comparison.png`：看 HLPO 是否跟得上 Dense。
- `flops_vs_loss.png`：看稀疏计算和 loss 的关系。
- `weight_evolution.png`：看训练过程中权重是否连续变化。
- `weight_diff_500_6000.png`：看早期到后期模型是否确实发生了可观更新。

这些材料让实验不是“一个孤立数字”，而是可复盘的训练过程。

## 7. 工程承接与后续方向

HLPO 如果要真正变成推理加速，需要继续解决 kernel 和 runtime 问题。Python 层 top-k / mask 可能反而更慢，因为它有额外调度和 scatter 开销。

后续方向包括：

```text
Triton / CUDA kernel
Rust native benchmark
block sparse execution
hardware mass gate / RTL primitive
```

这些方向说明 HLPO 不只是训练实验，也有向执行系统延展的空间。但它们不应该和当前 A/B test 混成同一个结论。
