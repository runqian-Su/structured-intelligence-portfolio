# SDFN: Structured Decision Field Network / 结构决策场网络

SDFN（Structured Decision Field Network）是为 TAAC 2026 推荐排序任务构建的结构化决策模型。它的核心问题是：

> 推荐样本能不能不只是 dense feature vector，而被建模为一个样本内部的结构化决策场？

SDFN 不直接把用户、物品、上下文和行为序列压成一个 dense vector 后交给排序头，而是先构造多类 structure token，再为 token 生成可学习波包参数，构造样本级 decision field graph，通过 HLPO 做结构参与选择，最后只从图传播后的 well token states 读取排序分数。

## 主要文档 / Main Documents

- [SDFN_技术说明.md](SDFN_技术说明.md)
- [HLPO到推荐任务的迁移说明.md](HLPO到推荐任务的迁移说明.md)
- [SDFN可学习波包对Ideon的反向支撑说明.md](SDFN可学习波包对Ideon的反向支撑说明.md)
- [指标体系说明.md](指标体系说明.md)
- [evidence/384L8_训练与评估日志摘录.md](evidence/384L8_训练与评估日志摘录.md)

## 建模链路 / Modeling Chain

```text
raw payload / raw features + context field
  -> payload-context field interaction
  -> structure tokens
  -> learnable wave-packet parameters
  -> sample-level decision field graph
  -> HLPO structured attention / gating
  -> graph-aware propagation
  -> updated well token states
  -> ranking score
```

这里的 wave-packet 不是直接移植 Meta-Math 的 ideon 路线，而是为推荐排序任务重新设计的样本状态表达：它服务于 structure token、关系图、HLPO routing 和 AUC 目标。

## 当前主线 / Current Mainline

当前公开说明聚焦结构纯化后的 GraphOnlyReadout 主线：

```text
Width384L8 + GraphExpand64
d_model = 384
n_layers = 8
total_token_count = 64
edge_space = 4096
hlpo_eval_permeability = 0.55
```

GraphOnlyReadout 的约束是：最终排序分数只从图传播后的 well token states 读取；phase shortcut、role-pair shortcut、LI residual rank correction、late rank fusion 等旁路不参与最终出分。

## 关键结果 / Key Results

```text
256L6 / GraphExpand48:
  observe 1.00 AUC: 0.849001
  full local AUC:   0.846639

384L8 / GraphExpand64:
  full local AUC:   0.846524
  evaluation size:  400 batches / 204,306 rows
  eval throughput:  130.07 rows/sec

Final inference:
  384L8 Fullpath was near / over final-round time constraint.
  384L8 + HLPO HardSkip completed large-graph inference.
  official AUC: 0.825194
```

## 如何理解 / Interpretation

这组结果支持一个有限结论：

- 样本级 structured decision graph 可以学习到有效排序行为。
- GraphOnlyReadout 在关闭主要旁路后仍然可用。
- HLPO 的结构参与选择可以从 attention 实验迁移到推荐排序任务。
- 在 384L8 / GraphExpand64 这种更大图空间下，HardSkip 是让大图推理可完成的重要路径。

它不声称：

- SDFN 已经是成熟通用推荐架构。
- HLPO HardSkip 已经构成严格控制变量的泛化证明。
- 当前实现已经是生产级加速方案。

## 公开边界 / Public Scope

本目录只保留架构、指标和日志摘录，不包含源码、checkpoint、parquet 数据、完整训练日志、比赛数据或构建产物。
