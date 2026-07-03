# Phase 3-6 Runtime Snapshot 摘要

本文件整理 HoLO Phase 3-6 的 runtime snapshot / integration validation 摘要。它不是完整 JSON 转储，而是说明每一阶段验证了哪些能力、哪些边界仍然保留。

## Phase 3：BoundaryGate2 / MemoryGraph / Controlled Write

```text
phase: 3.8
validationId: phase3-28a2f472189a
createdAt: 2026-06-24T02:54:23Z
mode: mock
acceptance: passed
runs: run-52eb2e6a0f1b -> run-11ec29ac63d4
```

验证通过的能力：

- Runtime chain。
- BoundaryGate2。
- HoloMemoryGraph。
- Session / compact memory。
- Controlled write audit。

BoundaryGate2 摘要：

```text
decisionCount: 5
outcomes:
  allow_audit_write: 1
  allow_memory_write: 1
  allow_readonly: 1
  allow_subunit_mock: 1
  approval_pending: 1
rejectedCapabilities:
  auto_approval
  boundary_bypass
  shell_execution
  repo_patch_apply
  ui_logic_as_core
```

MemoryGraph 摘要：

```text
nodeCount: 630
edgeCount: 1414
nodeKinds:
  approval: 38
  boundary_decision: 231
  controlled_write_artifact: 58
  run: 52
  safe_write_preview: 41
  skill_application: 47
  subunit_result: 60
  tool_result: 52
  unit: 51
```

这一阶段的意义是：HoLO 不只是能跑一次，而是能把 BoundaryGate、controlled write、subunit result、tool result 等运行证据写入结构化 memory graph。

## Phase 4：ToolAdapter2 / PatchPreview / Controlled File Write

```text
phase: 4.8
validationId: phase4-83450f5ee239
createdAt: 2026-06-24T07:25:18Z
mode: real
acceptance: passed
runs: run-f7113ad5bed5 -> run-bdd7a1d974db
```

验证通过的能力：

- Layer D artifacts。
- ToolAdapter2 schema / execution plan / lifecycle records。
- PatchPreview2 preview-only proposal。
- Controlled File Write 1.0 allowlisted append-only apply。
- VerificationTool 1.0 allowlist command checks。
- Tool result memory writeback。
- Recall。

关键状态：

```text
PatchPreview:
  proposed: true
  allWillWriteFalse: true
  hasVerificationPlan: true

ControlledFileWrite:
  statusOk: true
  targetAllowlisted: true
  appliedToSourceTrue: true
  hasHashes: true

Verification:
  passedCount: 3
  failedCount: 0
  nextAction: continue
```

Phase 4 的边界：它验证受控文件写入与验证工具，但仍不开放任意 shell、MCP/remote adapter、repo-wide patch apply 或完整 Claude-Code 工具能力。

## Phase 5：Runtime API / Daemon / MCP Adapter / Session Boundary

```text
phase: 5.8
validationId: phase5-d9fcfc3b267d
createdAt: 2026-06-25T15:52:00Z
mode: real
acceptance: passed_with_warnings
runs:
  runtimeApiRunId: run-4271905970a7
  daemonRunId: run-a6fd343ca0df
  cliApiRunId: run-f02796ccb440
  sessionRunId: run-a8acb8fc6ab4
```

验证通过的能力：

- Runtime API。
- Local daemon。
- CLI local API。
- MCP adapter。
- Session / remote boundary。
- Adapter/API guardrails。

关键状态：

```text
Runtime API:
  statusOk: true
  noWrite: true
  schemaRepair: not_needed

Daemon:
  host: 127.0.0.1
  health: ok
  runtimeOk: true
  sessionAttached: true

MCP Adapter:
  listedTools: true
  taskResultsOk: true
  allNoWrite: true
  noFileApply: true
  toolCount: 5

Session boundary:
  remoteDecision: remote_adapter_disabled
  remoteDisabled: true
  noShellOrFileApply: true
```

Phase 5 的 warning：

```text
DeepSeek is used only for compile; it cannot grant runtime permissions.
Schema repair may retry malformed DeepSeek JSON once without granting permissions.
```

这说明模型输出与 runtime permission 是分离的：LLM 可以参与 compile，但不能授予 adapter、daemon、MCP、remote、tool、file write 或 memory write 权限。

## Phase 6：Workbench / Snapshot Viewer / Approval Protocol / VSIX Contract

```text
phase: 6.8
validationId: phase6-52b03e7c6659
createdAt: 2026-06-26T09:02:52Z
mode: mock
acceptance: passed
runs:
  cliWorkbenchRunId: run-474d3ffe6230
  approvalPreviewRunId: run-c7ce6354ff0d
  approvalFinalRunId: run-8774f510b64a
  snapshotViewerRunId: run-8774f510b64a
```

验证通过的能力：

- CLI Workbench。
- Snapshot Viewer。
- Approval Protocol。
- Workbench static viewer。
- VSIX contract。
- Phase 5 regression。
- Guardrails。

关键状态：

```text
CLI Workbench:
  nonInteractiveStatusOk: true
  nonInteractiveNoWrite: true
  approvalDecisionArtifact: true
  controlledWriteOk: true
  appliedToSourceFalse: true

Snapshot Viewer:
  commandOk: true
  hasApprovalEnvelope: true
  hasControlledWriteSection: true
  hasVerificationSection: true
  runtimeCountUnchanged: true

Approval Protocol:
  decision: approve_artifact_write
  controlledArtifactOnlyForApprove: true
  appliedToSourceFalse: true

Workbench Viewer:
  exportOk: true
  htmlExists: true
  manifestExists: true
  loopback: true
  postRejected: true

VSIX Contract:
  directFileApplyFalse: true
  directRuntimeReuseFalse: true
  uiGrantsPermissionFalse: true
  publicRemoteFalse: true
  marketplaceFalse: true
```

Phase 6 的边界：它验证 UI / workbench surfaces 和 contract，不创建 VSIX package，不开放 public remote workbench，不允许 UI 绕过 BoundaryGate 授权。

## 总结

Phase 3-6 的共同结论是：

```text
HoLO 的价值不在于单次回答更流畅，
而在于 runtime 可以持续记录：
  HoloUnit / HoloMesh 结构
  DecisionFrame 裁决
  BoundaryGate 决策
  Tool / SubUnit / Memory 证据
  Snapshot / Approval / Controlled Write artifact
```

当前仍然保留的边界：

- 不开放任意 shell。
- 不开放 remote execution。
- 不开放 MCP write。
- 不允许 LLM 输出直接授予权限。
- 不把 UI / Workbench 当成权限主体。
- 不声称已经达到成熟 IDE agent 或 Claude-Code 工具等价能力。
