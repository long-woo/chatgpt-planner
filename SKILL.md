---
name: chatgpt-planner
description: >-
  Use a risk-adaptive ChatGPT Web and Codex workflow for software changes.
  Small, low-risk changes use a compact planning and verification path;
  broader, ambiguous, visual, data, security, or architecture changes use the
  comprehensive planner/reviewer workflow. Codex inspects and implements the
  repository while ChatGPT Web owns product intent and bounded planning.
metadata:
  version: "1.3.0"
---

# ChatGPT Planner

Use a two-agent development workflow with explicit file-based handoffs:

- **ChatGPT Web**: requirement review, product/UI design, planning, task
  decomposition, acceptance criteria, visual QA judgment, and final
  requirement review.
- **Codex**: repository reconnaissance, engineering analysis, implementation,
  technical tests, and visual evidence collection/fixes.
- **Current client**: orchestrates the workflow between both agents.

Core rule:

> ChatGPT Web decides what should be built.
> Codex determines what the repository actually contains and how to implement it.

ChatGPT Web mode constraint:

> All planning and review must happen in a normal ChatGPT Web conversation using Chat mode. Do not use Work mode, create a Work task, or switch an existing Planner conversation into Work mode.

Do not let ChatGPT Web directly modify production code.

Do not let Codex silently redefine product requirements.

## Select a workflow profile

Before creating implementation artifacts, perform a preliminary risk screen.
If the request appears Lite-eligible, initialize the Lite state and complete
its `TRIAGE` gate. Otherwise initialize the comprehensive workflow directly.

Use `LITE` only when the behavior is clear, the impact is localized and easy
to revert, objective targeted verification is available, and the change does
not introduce material product/UI decisions or affect public APIs,
persistence, migration, authentication, authorization, privacy, security,
billing, payment, or destructive operations. The expected production-code
impact should normally be one module and roughly three files or fewer, but
risk takes priority over file count.

Read `references/lite-workflow.md` before selecting or operating the Lite
profile. Initialize `.chatgpt/workflow-state.json` from
`templates/lite-workflow-state.json` for an eligible Lite change.

Use `COMPREHENSIVE` when any Lite criterion is false or uncertain. Existing
state files without `workflow_profile` are comprehensive workflows for
backward compatibility. Never downgrade an active comprehensive workflow to
Lite merely to bypass a gate.

## Lite workflow

The Lite flow is:

```text
TRIAGE
  -> COMPACT_PLAN
  -> IMPLEMENTATION
  -> VERIFICATION
  -> FINAL_REVIEW (conditional)
  -> DONE
```

ChatGPT Web produces one compact plan. Codex persists it as
`.chatgpt/change-contract.json`, implements within that boundary, and records
implementation and verification in `.chatgpt/change-result.json`.

Skip the second ChatGPT Web review only when all acceptance criteria pass,
verification is complete, implementation has no plan deviation, no unresolved
product or visual judgment was introduced, and Codex has no material
uncertainty. Record the skipped review and its reason in state. Otherwise run
the final review or escalate to the comprehensive workflow as specified in
`references/lite-workflow.md`.

## Comprehensive workflow

Before any stage, read `references/workflow-state.md` and initialize or update
the canonical state file at `.chatgpt/workflow-state.json` from
`templates/workflow-state.json`. Set `workflow_profile` to `COMPREHENSIVE`.
The workflow is file-based and does not require Git, branches, commits, or
repository metadata.

At the start of a workflow, read `references/decision-records.md`. If the
project has `.chatgpt/decision-records.md`, ChatGPT Web and Codex must read its
relevant `ACTIVE` records before requirement review, planning, design, analysis,
implementation, and final review. A proposal that conflicts with an active
record must go through re-review before downstream work continues; do not
silently rewrite or override the record.

The complete state-gated flow is:

```text
REQUIREMENT_REVIEW
  → PRODUCT_DESIGN
  → DESIGN
  → DESIGN_HANDOFF
  → ENGINEERING_ANALYSIS
  → TASK_PLANNING
  → IMPLEMENTATION
  → VERIFICATION
  → VISUAL_QA
  → FINAL_REVIEW
  → DONE
```

Stage contract:

| Stage | Input | Output | Owner | State change |
|---|---|---|---|---|
| Requirement Review | User request, project context, repository facts | `.chatgpt/requirement-review.md` | ChatGPT Web | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Product Design | Approved requirement review | Product behavior and MVP decisions | ChatGPT Web | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Design | Product decisions, UI sources, existing patterns | UX/UI decisions and required states | ChatGPT Web | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Design Handoff | Approved product/design decisions | `.chatgpt/design-handoff.md` or `NOT_APPLICABLE` record | ChatGPT Web, persisted by Codex | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Engineering Analysis | Approved handoff, repository context | `.chatgpt/engineering-analysis.md` | Codex | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Task Planning | All approved preceding artifacts | Task Contract JSON | ChatGPT Web; Codex recovery only after bounded malformed-response recovery | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Implementation | Approved Task Contract and current state | Code and implementation result | Codex | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Verification | Approved implementation | Test, build, and manual verification results | Codex | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Visual QA | Verification evidence and design handoff | `.chatgpt/visual-review.md` or `NOT_APPLICABLE` record | ChatGPT Web, evidence by Codex | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Final Review | All artifacts, implementation result, verification, visual review | PASS/NEEDS_FIX/BLOCKED decision | ChatGPT Web | `IN_PROGRESS` → `WAITING_REVIEW` → `APPROVED` |
| Done | Approved final review | Closed workflow state | Current client | `IN_PROGRESS` → `COMPLETED` |

Every stage must have an explicit state. A stage may enter `IN_PROGRESS` only
when the previous stage is `APPROVED`; no implementation may begin until
Codex has read `.chatgpt/workflow-state.json`, confirmed `IMPLEMENTATION` is
`IN_PROGRESS`, and confirmed the Task Contract and required preceding
artifacts are approved. `BLOCKED` stops downstream work until its cause is
resolved and the responsible stage is re-entered.

Canonical comprehensive artifacts live under `.chatgpt/` in the current
project/work directory:

- `.chatgpt/workflow-state.json`
- `.chatgpt/requirement-review.md`
- `.chatgpt/design-handoff.md`
- `.chatgpt/engineering-analysis.md`
- `.chatgpt/visual-review.md`
- `.chatgpt/decision-records.md` (when the project uses Decision Records)

For compatibility, existing root-level `requirement-review.md`,
`design-handoff.md`, `engineering-analysis.md`, and `visual-review.md` remain
valid legacy inputs. If a canonical file is absent, read the legacy file; do
not delete or silently overwrite it. New executions should write canonical
`.chatgpt/` artifacts and may mirror them to legacy paths only when an existing
workflow explicitly depends on those paths.

## Before planning

Codex should collect only repository facts relevant to the requirement:

- stack
- relevant modules
- existing behavior
- APIs/data models
- architectural constraints
- available verification commands

Do not start implementation during reconnaissance.

## Stage references

Read the following references at the corresponding gates:

- Lite eligibility, compact artifacts, conditional review, and escalation:
  `references/lite-workflow.md`
- Overall workflow and artifact routing: `references/workflow.md` and
  `references/index.md`
- Decision Records and cross-iteration product/design/technical constraints:
  `references/decision-records.md`
- State machine and transition gates: `references/workflow-state.md`
- Requirement Review: `references/requirement-review.md`
- Design Handoff: `references/design-handoff.md` and, for UI work,
  `references/ui-design-context.md`
- Engineering Analysis: `references/engineering-analysis.md`
- Task Contract: `references/planner-protocol.md` and
  `references/task-contract.md`
- Visual QA: `references/visual-qa.md`
- Browser interaction and visual evidence:
  `references/browser-workflow.md`
- Failures, missing artifacts, and bounded review loops:
  `references/failure-handling.md`

## Planning

For Lite work, read `references/lite-workflow.md` and use
`templates/change-contract.json`.

For comprehensive work, read:

`references/planner-protocol.md`

and:

`references/task-contract.md`

Use them when interacting with ChatGPT Web for comprehensive planning.

## Browser interaction

Read:

`references/browser-workflow.md`

when opening or interacting with ChatGPT Web.

## Implementation

Before editing production code, read `.chatgpt/workflow-state.json` and verify
the selected profile, confirm `IMPLEMENTATION` is `IN_PROGRESS`, and confirm
every dependency is `APPROVED`. For Lite, also confirm
`.chatgpt/change-contract.json` is usable. For comprehensive work, confirm the
Task Contract and required preceding artifacts are approved. A usable Task
Contract may be a schema-valid ChatGPT Web response or the narrowly bounded
recovered contract allowed by `references/failure-handling.md`; record its
provenance in workflow state. If the state is missing, stale, blocked, or
inconsistent with the artifacts, stop and repair the state through the
responsible stage before coding.

Implement only tasks from the accepted plan.

Codex may decide implementation details but must not independently introduce
new product behavior.

Keep unrelated discoveries under:

`DISCOVERED_ISSUES`

Do not perform unrelated cleanup unless it blocks the requested work.

## Review

For Lite work, apply the conditional review gate in
`references/lite-workflow.md`.

After comprehensive implementation and local verification, read:

`references/reviewer-protocol.md`

For UI changes, complete Visual QA and update `.chatgpt/visual-review.md` before
sending the implementation result to the same ChatGPT Web conversation.

Expected review statuses:

- `PASS`
- `NEEDS_FIX`
- `BLOCKED`

## Failures

For browser failures, malformed Planner responses, lost conversations,
verification failures, or review loops, read:

`references/failure-handling.md`

## Authority

When information conflicts, use this priority:

1. explicit current user instruction
2. confirmed product requirement
3. verified repository facts
4. accepted ChatGPT Web plan
5. implementation preference

ChatGPT Web owns requirement interpretation.

Codex owns repository facts.

## Completion

For Lite work, do not claim full completion unless:

- `.chatgpt/workflow-state.json` records `workflow_profile: LITE` and `DONE` is
  `COMPLETED`
- `.chatgpt/change-contract.json` bounds the request
- `.chatgpt/change-result.json` maps every acceptance criterion to evidence
- all required verification passed
- the implementation has no unresolved plan deviation
- Final Review is `APPROVED` or validly `SKIPPED` with a recorded reason

For comprehensive work, do not claim full completion unless:

- `.chatgpt/workflow-state.json` records all stages and `DONE` is `COMPLETED`
- `.chatgpt/requirement-review.md` bounded the request
- product/UI design completed its applicable handoff
- `.chatgpt/engineering-analysis.md` assessed repository impact before planning
- ChatGPT Web created the plan
- repository facts validated the plan
- Codex implemented the accepted tasks
- practical verification was performed
- `.chatgpt/visual-review.md` passed for UI work, or is marked
  `NOT_APPLICABLE` for non-UI work
- ChatGPT Web reviewed the implementation
- final review returned `PASS`
