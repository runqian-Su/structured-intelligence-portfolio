# META-MATH-V8 架构说明 / Architecture Note

## 1. 核心问题

META-MATH-V8 试图处理的问题不是“如何让 LLM 直接给出更好的答案”，而是：

> 如何在 LLM 外部建立一个受约束的慢思考 runtime，使复杂问题先被拆成概念状态、结构关系、动态张力和运行边界，再由 window、container、transition、goal evaluation 和 guard 进行推进与校验。

这条路线来自两个判断：

- **Transformer is Flat. The World is Deep.** token 序列是平面的，但真实问题往往包含层级、隐属性、约束、冲突、因果混沌和动态演化。
- **LLM 可以提供可能性，但不应直接拥有解释权。** LLM 的潜空间知识很有价值，但候选答案需要经过结构校验、运行窗口和 guard 判定。

这里需要明确区分：原始目录里的 `Concept World` 是 V8 之后下一轮重构版本的开头，不属于 V8 已完成范围。V8 本身应单独看作 ideon、五维波包、元算法算子与 guarded runtime 的组合。

## 2. V8 在 META-MATH 路线中的位置

V8 是 META-MATH 的第八个大版本，不是日常分支的简单改名。它是在前序版本的概念图、五维波包、HMF flow、拓扑空洞、代谢审计和递归收敛实验之后，进一步面向 agent-in-loop guarded runtime 的架构版本。

更准确地说：

- **V4** 更偏工程底座：用 Rust 实现概念图状态推进、HMF 流动、Pomegranate sheaf 修复、因果混沌评估、Gibbs energy / entropy / coherence 审计和 Ouroboros 收敛循环。
- **V8** 更偏运行时架构：强调 ideon 状态、五维波包、window、container、transition、goal evaluation、guard 和受限解释，把 LLM 放进外部 System 2 的循环中。

因此，V8 不是替代 V4 的“稳定版”，而是基于前序概念和工程经验继续上探的一条 guarded runtime 架构路线。

## 3. Ideon：概念状态单元

`ideon` 是 META-MATH-V8 中的核心数据单元。它不是普通知识图谱节点，也不是 embedding 向量，而是一个可演化的概念状态单元。

一个 ideon 至少承担四类角色：

- **语义指针**：能被人类、agent 和 LLM 识别的概念身份。
- **概念波包**：携带位置、速度、相位、稳定性、能量、退相干等动态状态。
- **HMF 执行单元**：可以落入 Source / Sink / Flow / Field 等结构位置。
- **可重写拓扑对象**：可以参与局部改写、沉淀、坍缩、跃迁或被代谢淘汰。

所以，ideon 要回答的不是“这个词是什么意思”，而是：

```text
这个概念状态当前处于什么位置？
它是否稳定？
它承受什么压力？
它能否继续展开？
它与目标、边界、其他状态之间是什么关系？
```

## 4. 五维波包

META-MATH-V8 用五维波包描述 ideon 的状态，目的不是制造复杂术语，而是避免把概念当成静态标签。

五维波包关注：

- 来源可靠性和语义身份
- 显性 / 隐性状态
- 尺度层级
- 演化趋势
- 关系张力和局部压力

这使 runtime 可以处理“概念状态如何变化”，而不是只处理“文本序列如何续写”。

## 5. 元算法与算子分工

META-MATH-V8 将四大元算法和两个关键算子放进 guarded runtime：

- **HMF**：负责结构拓扑、Source / Sink 闭合、流动路径和场约束。
- **Laplace**：负责意图场、摩擦、惯性、干预成本和行动可行性判断。
- **Pomegranate**：负责有限概念空间、挤压、沉积、空腔和边界穿刺。
- **Ouroboros**：负责反馈递归、自我修正和下一轮重构。
- **代谢算子 / Metabolic operator**：负责概念单元的吸收、转化、沉淀、排出和高熵剪枝。
- **因果混沌算子 / Causal-chaos operator**：判断概念之间是否具备双向互赋和有效传导，避免把松散联想误当成因果结构。

## 6. 三层系统结构

META-MATH-V8 可以理解为三层：

```text
现实输入层
  用户问题、背景事实、任务目标、约束条件

结构运行层
  ideon、波包状态、结构关系、边界、张力、稳定态、局部冲突

Agent 观测层
  播种、窗口观察、有限解释、必要时重播种或边界调整
```

结构运行层是系统主体。agent 不是中心驾驶员，LLM 也不是最终裁决者。agent 和 LLM 都只能在结构边界内参与。

## 7. LLM 的位置

LLM 在 META-MATH-V8 中不是最终裁决者，而是：

- 潜空间知识采样器
- 候选概念生成器
- 隐性关系提取器
- 局部空洞补全器
- 意图和语境估计器
- 动力学参数辅助估计器
- 自然语言转译器
- 反馈解释器

LLM 可以提出候选，但候选必须进入 runtime 的结构处理链路。最终解释必须建立在 window report、transition report、container snapshot、goal evaluation 等运行状态之上。

## 8. 解释权原则

META-MATH-V8 的核心边界是：

> No guarded runtime result, no final interpretive authority.  
> 没有通过 guarded runtime 的运行结果，就没有最终解释权。

这意味着：

- LLM 不能直接把候选答案写成结论。
- agent 不能绕过窗口状态宣布最终判断。
- 未通过目标评估、边界检查和跃迁监督的结果，只能作为候选或中间状态。
- 最终输出必须能追溯到窗口结果、跃迁报告、容器快照和 guard 判定。
