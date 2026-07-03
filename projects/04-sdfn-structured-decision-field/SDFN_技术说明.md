# SDFN 技术说明 / Technical Note

## 1. 设计出发点

推荐排序样本通常同时包含用户特征、物品特征、上下文信号、历史行为序列和反馈信息。常规做法会将这些信息编码后拼接、交叉或融合，再由 MLP / ranking head 给出分数。这种方式工程上可用，但最终排序分数的来源很难拆解：模型到底依赖了用户长期偏好、当前候选物品、行为序列、上下文，还是某条后接的修正路径，并不清楚。

SDFN 的出发点是把一个推荐样本看成临时形成的结构化决策场。模型先构造结构 token，再学习 token 的状态、关系和传播路径，最后只从核心决策 token 读取排序分数。

```text
raw features / payload / context
  -> structured token construction
  -> learned wave-packet state
  -> sample-level decision field graph
  -> HLPO structural routing
  -> graph-aware propagation
  -> well-token-only readout
  -> ranking score
```

这里的“波包”不是直接移植 Meta-Math 的 ideon 路线，而是 SDFN 为推荐任务重新设计的样本状态描述方式。它借鉴“用多维状态描述对象参与关系”的思路，但在实现上服务于推荐排序中的结构 token、关系图和 AUC 目标。

## 2. Structure Token

SDFN 不把全部输入压成一个 dense vector，而是把样本组织成多类结构 token：

```text
well token:
  核心决策 token，最终排序分数只从这里读取。

anchor token:
  承载上下文、字段背景、相对稳定的结构锚点。

residual token:
  承载补充证据、局部结构状态或未被主路径完全吸收的信息。

fast token:
  承载当前 payload、候选物品、行为序列中变化较快的信号。
```

这些 token 不是手写规则枚举出的离散对象，而是由模型从输入特征中学习得到的连续结构表示。它们的意义不在于给字段贴标签，而在于让模型拥有“谁负责背景、谁负责当前信号、谁负责最终判断”的内部结构分工。

## 3. 可学习波包参数

每个结构 token 会生成一组可学习状态参数，用于后续关系计算与图传播：

```text
mu:
  token 在隐式决策场中的中心位置。

sigma:
  token 的扩散尺度，影响关系计算范围。

relativistic_mass:
  token 的参与质量，影响关系强度和传播幅度。

persistence:
  token 状态在当前样本决策中的稳定程度。

phase_response:
  token 对相位关系的响应状态。
```

这些参数由网络预测，不是人工指定哪个字段更重要。训练中会对部分数值范围做约束，例如 sigma 下界、mass 范围等；这些约束服务于训练稳定性，不编码业务规则。

## 4. Decision Field Graph

波包参数生成后，模型构造样本级 decision field graph。这个图不是外部用户-物品图，也不是预先固定的知识图谱，而是在每个样本内部动态形成的关系图。

可以把它理解为：

```text
传统图推荐:
  图先于样本存在，样本在图上取子结构。

SDFN:
  每个样本内部生成一张临时关系图，图服务于当前排序判断。
```

一个典型关系计算可概括为：

```text
dist = sqrt(((mu_i - mu_j) * G)^2)
phase_kernel = cos(dist / sigma_pair)
mass_pair = sqrt(mass_i * mass_j)
persistence_pair = 0.5 * (p_i + p_j)
interference = reachability * mass_pair * persistence_pair * phase_kernel
```

图边与 edge gate 读取的主要信号包括：

```text
relativistic mass pair
persistence pair
field state
role-pair embedding
phase_kernel
abs_interference
reachability
```

其中 phase 与 role-pair 不再作为最终 readout 的旁路输入，而是进入图关系与 edge visibility，参与结构传播。

## 5. HLPO Structural Routing

HLPO 在 SDFN 中承担结构参与选择的作用。它不是额外预测模型，也不是后处理剪枝，而是 SDFN 主干内部的 learned sparse structural routing。

它回答的问题是：

```text
哪些 token 应该参与当前样本的决策？
哪些 edge 应该被强调？
哪些关系更像噪声，应当弱化或跳过？
当前样本需要开放多少结构传播通路？
```

训练阶段，HLPO 与主模型共同学习 token / edge 的参与质量。推理阶段，同一套结构信号可以走 Fullpath，也可以走更硬的 HardSkip 路径。最终分数仍然只由传播后的 well token states 读出，因此 SDFN + HLPO 不是 ensemble，也不是多个模型分数的 late fusion。

当前主线采用：

```text
hlpo_permeability_schedule = loss_driven
hlpo_permeability_min = 0.55
hlpo_eval_permeability = 0.55
hlpo_permeability_warmup_steps = 900
```

## 6. GraphOnlyReadout

SDFN 当前主线采用 GraphOnlyReadout。它的约束是：最终排序分数只从图传播后的 well token states 读取。

关闭的旁路包括：

```text
phase direct readout shortcut
role-pair direct readout shortcut
LI residual rank correction
late rank fusion
```

最终满足：

```text
rank = base_rank
prob = sigmoid(rank)
prob_delta_abs = 0
li_delta = 0
fb_prob_corr = 1
```

这一设置的目的不是追求最复杂的融合，而是让实验更集中地验证结构主链：structure token 是否有效、decision field graph 是否有效、HLPO routing 是否有效、图传播后的 well token 是否能独立承担读出。

## 7. Width384L8 / GraphExpand64

最终候选路线相对 256L6 / GraphExpand48 做了三类放大：

```text
表征宽度: 256 -> 384
传播层数: 6 -> 8
图 token 数: 48 -> 64
edge space: 2304 -> 4096
```

主要配置如下：

```text
d_model = 384
payload_dim = 384
field_dim = 384
n_heads = 12
n_layers = 8
ff_mult = 4
dropout = 0.15
weight_decay = 5e-4

k_well = 16
k_anchor = 24
k_residual = 22
k_fast = 2
total_token_count = 64
edge_space = 4096
```

训练目标以排序关系为主：

```text
bce_loss_weight = 0.02
pairwise_auc_loss_weight = 0.80
pairwise_temperature = 1.50
rank_scale_loss_weight = 0.05
prob_scale_loss_weight = 0.50
```

BCE 项保留较小权重，用于维持基础监督和概率标定；主要优化压力来自 pairwise AUC loss。

学习率协议：

```text
lr = 2.5e-4
lr_schedule = warmup_linear
lr_warmup_steps = 900
lr_decay_steps = 5600
lr_min_ratio = 0.20
```

## 8. 384L8 Forward 主链

384L8 版本的主链可以概括为：

```text
batch
  -> OfficialFlatFeatureBank
  -> BackgroundFieldEncoder
  -> SlowTerrainBuilder
  -> FastPacketEncoder
  -> WaveIRBuilder
  -> TokenObserver
  -> TransformerInputPackBuilder
  -> HLPOController
  -> InternalSampleDynamics
  -> StructureAwareReader
  -> WellReadout
  -> EvidenceHeads / metrics
```

关键模块的功能：

- `OfficialFlatFeatureBank`：把官方 flat schema 中的稀疏、多值、dense、sequence 和上下文信号整理为统一表示。
- `BackgroundFieldEncoder`：从 user / context / cross 生成样本级背景场，包括 `bg_state`、`kappa`、`G`、`V`。
- `SlowTerrainBuilder`：从 `domain_state` 和 `bg_state` 构造 anchor / residual token。
- `FastPacketEncoder`：从 item / user / context / bg_state 构造 fast token，承载当前候选和短期 payload 信息。
- `WaveIRBuilder`：拼接 well / anchor / residual / fast token，并生成波包状态、role_id、time_id 和 reachability 输入。
- `HLPOController`：生成 token / edge keep score、effective adjacency 和 well emergence gate。
- `InternalSampleDynamics`：补充样本内部 inertia / resistance / action / flow bias。
- `StructureAwareReader`：执行 8 层图感知传播。
- `WellReadout`：只读取前 `k_well=16` 个 well token，生成最终 rank score。

StructureAwareReader 不是普通 transformer。每层 attention 使用：

```text
QK attention logits
+ B_geo
+ B_edge
+ B_role
+ B_hlpo
+ B_internal
+ log(Adj_mask_effective)
```

也就是说，reader 的传播被决策场图、HLPO 结构选择和内部动力学共同约束。

## 9. 实验边界

本文中的 SDFN 指结构纯化后的 GraphOnlyReadout 主线，而不是所有早期探索版本的集合。它不是模型融合、外部检索增强、生成式推荐模型，也不是通用 LLM 推理框架。

更准确地说，SDFN 是一次面向推荐排序的结构可学习性验证：将每个样本表示为结构 token，构造样本级可学习关系图，通过 HLPO 进行结构参与选择，并只从传播后的核心决策 token 读取最终分数。
