# Workflow State

This reference defines the comprehensive file-based state machine for ChatGPT
Web and Codex collaboration. Small eligible changes use the state machine in
`lite-workflow.md`. Both state files are project-local and must work in a
directory that is not a Git repository.

## Profile routing

Every new state file declares `workflow_profile` as `LITE` or
`COMPREHENSIVE`. Read `lite-workflow.md` and perform a preliminary risk screen.
When the request appears eligible, initialize the Lite state and complete its
`TRIAGE` gate before planning or implementation. Existing schema-version-1
state files without `workflow_profile` are treated as comprehensive workflows.

Initialize a Lite workflow from `templates/lite-workflow-state.json`. Initialize
a comprehensive workflow from `templates/workflow-state.json`. Do not replace
an active state file with a fresh template. Resume it or follow the Lite
escalation procedure while preserving its artifacts and history.

## Canonical state

At workflow start, copy `templates/workflow-state.json` to
`.chatgpt/workflow-state.json` if the canonical file does not exist. Create `.chatgpt/`
as needed. The state file is the source of truth for stage position; stage
artifacts are the source of truth for stage content.

The allowed stages, in order, are:

```text
REQUIREMENT_REVIEW → PRODUCT_DESIGN → DESIGN → DESIGN_HANDOFF
→ ENGINEERING_ANALYSIS → TASK_PLANNING → IMPLEMENTATION → VERIFICATION
→ VISUAL_QA → FINAL_REVIEW → DONE
```

The allowed stage states are:

- `NOT_STARTED`: no work has started.
- `IN_PROGRESS`: the responsible owner is actively working.
- `WAITING_REVIEW`: the owner has produced the stage output and is waiting for
  the designated approver.
- `APPROVED`: the output is accepted and may unlock the next stage.
- `BLOCKED`: work cannot continue because a required decision, artifact,
  capability, or verification is unavailable.
- `COMPLETED`: the stage is closed. Use this for `DONE`; keep earlier stages
  `APPROVED` so their gate evidence remains visible.

Lite additionally uses `SKIPPED` for conditional Final Review when the skip
conditions in `lite-workflow.md` are all satisfied. `SKIPPED` is not a general
mechanism for bypassing comprehensive stages.

## Stage ownership

| Stage | Owner | Approver | Required output |
|---|---|---|---|
| `REQUIREMENT_REVIEW` | ChatGPT Web | ChatGPT Web | `.chatgpt/requirement-review.md` |
| `PRODUCT_DESIGN` | ChatGPT Web | ChatGPT Web | Product decisions in the Planner conversation |
| `DESIGN` | ChatGPT Web | ChatGPT Web | UX/UI decisions, states, and constraints |
| `DESIGN_HANDOFF` | ChatGPT Web; Codex persists | ChatGPT Web | `.chatgpt/design-handoff.md` or an explicit `NOT_APPLICABLE` record |
| `ENGINEERING_ANALYSIS` | Codex | Codex | `.chatgpt/engineering-analysis.md` |
| `TASK_PLANNING` | ChatGPT Web | ChatGPT Web | Accepted Task Contract JSON |
| `IMPLEMENTATION` | Codex | Codex | Code changes and implementation result |
| `VERIFICATION` | Codex | Codex | Test, build, and manual verification results |
| `VISUAL_QA` | ChatGPT Web; Codex supplies evidence | ChatGPT Web | `.chatgpt/visual-review.md` or an explicit `NOT_APPLICABLE` record |
| `FINAL_REVIEW` | ChatGPT Web | ChatGPT Web | `PASS`, `NEEDS_FIX`, or `BLOCKED` decision |
| `DONE` | Current client | Current client | State marked `COMPLETED` |

## Transition rules

1. Initialize `REQUIREMENT_REVIEW` as `IN_PROGRESS` and every later stage as
   `NOT_STARTED`.
2. A stage can enter `IN_PROGRESS` only if the immediately preceding stage is
   `APPROVED`. `REQUIREMENT_REVIEW` is the sole initial exception.
3. The owner records the output, then changes the stage to `WAITING_REVIEW`.
4. The approver changes `WAITING_REVIEW` to `APPROVED` only when the output is
   complete, consistent with prior artifacts, and within scope.
5. A stage with `BLOCKED` cannot unlock any later stage. Record the cause and
   the recovery action; resume it as `IN_PROGRESS` after the blocker is
   resolved.
6. `NEEDS_FIX` in Visual QA or Final Review returns control to the responsible
   Codex stage. Reset any downstream stages whose prior approvals are no
   longer valid to `NOT_STARTED`; do not mark the review approved until the
   bounded fix is verified.
7. A Visual QA or Final Review result of `PASS` maps to that stage's
   `APPROVED` state. `NEEDS_FIX` maps the review stage to `BLOCKED` while the
   responsible Codex stage re-enters `IN_PROGRESS`; after the fix and repeat
   verification, resume the review stage.
8. Before implementation, Codex must read the current state and verify that
   `IMPLEMENTATION` is `IN_PROGRESS`, `TASK_PLANNING` is `APPROVED`, and the
   requirement, design, and engineering artifacts are present and usable.
9. After verification, UI work must complete Visual QA; non-UI work must still
   produce a `NOT_APPLICABLE` record. Final Review requires all applicable
   upstream stages to be approved.
10. Only an approved `FINAL_REVIEW` may move `DONE` to `IN_PROGRESS`, after
   which the current client marks `DONE` as `COMPLETED`.

## Update protocol

When changing state, update `current_stage`, the stage's `state`, timestamps,
`artifact`, and a short `notes` value together. Append a concise entry to
`history` so a later agent can reconstruct why the transition occurred. Never
use chat history, Git branches, or commits as a substitute for this record.

For an existing workflow that uses root-level artifacts, read those files as
legacy inputs when the corresponding `.chatgpt/` file is absent. New work writes
the canonical `.chatgpt/` paths and leaves legacy files intact.
