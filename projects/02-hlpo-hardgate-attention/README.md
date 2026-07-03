# HLPO 交互注意力与 HardGate / HLPO Interactive Attention and HardGate

本目录整理 HLPO 从“交互注意力”到 HardGate sparse attention A/B test 的公开说明材料。

HLPO comes from the "interaction" problem in the meta-engine and explores whether attention can be treated as a structural participation field rather than a fully connected relation matrix.

## 主要文档 / Main Documents

- [HLPO_架构说明.md](HLPO_架构说明.md)
- [HLPO_HardGate实现思路.md](HLPO_HardGate实现思路.md)
- [HLPO_AB测试结论.md](HLPO_AB测试结论.md)

## 核心问题 / Core Question

> 如果只保留一部分 attention connections，模型还能否保持接近 dense attention 的训练效果？

In short: can a hard-gated sparse attention path remain trainable and close to dense attention under a controlled A/B setup?

## 当前公开证据 / Public Evidence

```text
Run A: Dense baseline
Run B: HLPO HardGate
Hardware: Mac Studio, M2 Ultra, 64GB unified memory
Framework: PyTorch + MPS
Dataset: FineWeb-Edu streaming
Training scale: ~100M tokens / 6000 steps
HLPO sparsity: 50%
Dense final loss: 4.1909
HLPO final loss: 4.2745
Loss gap: 0.0836
```

## 图片 / Visualizations

- ![Dense vs HLPO Loss](assets/loss_comparison.png)
- ![FLOPs vs Loss](assets/flops_vs_loss.png)
- ![Weight Evolution](assets/weight_evolution.png)
- ![Weight Difference 500 to 6000](assets/weight_diff_500_6000.png)

## 公开边界 / Public Boundary

当前可以说明的是：在该本地训练设置下，50% HardGate sparse attention 没有破坏基本收敛过程，并保持了接近 dense attention 的训练轨迹。

当前不能说明的是：HLPO 已经普遍替代 dense attention，或 Python-level top-k / mask 实现已经构成真实推理加速 benchmark。
