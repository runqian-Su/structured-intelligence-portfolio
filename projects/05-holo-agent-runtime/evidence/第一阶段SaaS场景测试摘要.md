# 第一阶段 SaaS 场景测试摘要

本测试用于观察 DeepSeek 作为底座模型接入 HoLO Runtime 后，在真实后端 SaaS 技术问题中的表现差异。测试重点不是证明 HoLO 已经给出更完整的业务诊断，而是验证：

```text
bare DeepSeek diagnosticReport
HoLO Runtime HoloMesh / DecisionFrame / BoundaryGate
HoLO-derived diagnosticReport
```

三者之间的差异。

## 1. 测试结论概览

第一阶段包含 5 个 SaaS 后端问题：

```text
01 PostgreSQL 订单列表慢查询
02 第三方支付 API 超时
03 后台任务队列积压
04 仪表盘缓存数据陈旧
05 发布后错误率升高
```

对照测试结果：

```text
Bare DeepSeek pass: 5/5
HoLO Runtime pass: 5/5
Snapshot pass: 5/5
```

HoLO 的主要增益：

- 将问题编译为 root HoloUnit 和 child units。
- 记录 Source / Sink closure、socket closure、transform closure。
- 生成 DecisionFrame 和 BoundaryGate 结果。
- 记录 rejected capabilities，避免 shell、remote、file apply、MCP write 等越界能力。
- 生成 runtime snapshot，便于复盘。

当前不足：

- HoLO-derived diagnosticReport 仍然不如 bare DeepSeek 完整，业务建议字段还不是一等 runtime 输出。
- PatchPreview 对诊断类任务也会出现，形成一定 friction。
- 部分场景 semantic preservation 偏低，说明结构化拆解保住了机制，但会压缩一部分业务语义。

## 2. 五个 SaaS 问题与拆解结果

| 场景 | Root unit | Units / sockets / transforms | Child units | 语义保留 |
| --- | --- | --- | --- | --- |
| PostgreSQL 订单列表慢查询 | `unit-root-001` | `6 / 5 / 1` | risk verification, pagination, cause analysis, optimization, diagnostic steps | `pagination` 保留；`cache / redis` 丢失 |
| 第三方支付 API 超时 | `unit-root-payment-analysis` | `6 / 0 / 1` | design recommendations, cause analysis, risk validation, degradation UX, diagnostic steps | `payment` 保留；`queue / cache / idempotency` 丢失 |
| 后台任务队列积压 | `unit-invoice-delay-diagnostic` | `8 / 7 / 1` | risk verification, recovery, readonly steps, queue metrics, retry storm, cause analysis, idempotency | `queue / idempotency` 保留；`cache` 丢失 |
| 仪表盘缓存数据陈旧 | `unit-root-dashboard-staleness` | `7 / 6 / 1` | risk, symptoms, cache, fix, job, consistency | `postgres / cache / redis` 保留；`release` 丢失 |
| 发布后错误率升高 | `root-001` | `8 / 5 / 1` | 7 个 child units | semantic preservation 评分为 3 |

每个场景都满足：

```text
Source / Sink closed: True
Sockets closed: True
Transform closed: True
Accepted transforms: 1
Evidence readiness: ready
Patch preview / approval: proposed / pending_user
```

## 3. BoundaryGate 与安全边界

五个场景的 BoundaryGate outcome 基本一致：

```text
allow_memory_write: 1
allow_readonly: 1
allow_subunit_mock: 1
approval_pending: 1
```

被拒绝能力包括：

```text
agent
apply_patch
bash
cron
mcp
powershell
remote
shell
team
worktree
```

这说明 DeepSeek 参与的是语义诊断与结构生成，不会因此获得 shell、remote、MCP write、file apply 或真实 subagent 权限。

## 4. 与 bare DeepSeek 的差异

bare DeepSeek 的优势：

- 更直接给出业务诊断建议。
- 五个场景输出均为可解析 JSON。
- diagnostic completeness 在 baseline 中较高。

HoLO 的优势：

- 输出可追踪到 HoloUnit / HoloMesh。
- 每次运行保留 BoundaryGate、DecisionFrame、runtime snapshot。
- 可以明确记录哪些能力被拒绝。
- 诊断过程有可审计 artifact，而不是只留下自然语言答案。

第一阶段对照结论可以概括为：

```text
Bare DeepSeek is stronger as a direct diagnostic writer.
HoLO is stronger as an auditable runtime wrapper.
```

也就是说，HoLO 当前证明的是“结构控制链成立”，不是“业务诊断表达已经优于裸模型”。

## 5. 风险与下一步

当前风险：

- HoLO diagnosticReport 字段还不完整。
- PatchPreview 对纯诊断任务应当更克制。
- 部分场景的语义保留仍需提升，尤其是支付超时和订单慢查询中的关键业务词。

下一步应当让以下内容成为一等 runtime field：

```text
root cause hypotheses
readonly diagnostic plan
risk / rollback plan
business recommendation
dangerous advice flags
```

这样 HoLO 才能同时保留结构审计能力和业务诊断完整性。
