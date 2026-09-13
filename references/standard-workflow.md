# Standard Workflow

Use `STANDARD` for a bounded feature or contract change that needs more
complete requirement and design alignment than Lite, but does not cross a
Comprehensive risk boundary.

## Flow

```text
ANALYSIS → CONTEXT → IMPLEMENTATION → VERIFICATION → DONE
```

ChatGPT Web performs the requirement, product/UI, edge-case, and scope
analysis in one planning turn. It returns one execution context. There is no
separate Requirement Review, Design Handoff, Task Contract, Change Result, or
default ChatGPT review file.

## Context requirements

The context follows `references/execution-context.md` exactly. Put product
behavior, required UI states, design evidence, repository-independent
constraints, and explicit exclusions into the six allowed fields. Keep
implementation suggestions bounded and non-speculative.

Codex validates repository fit, implements the scope, and verifies acceptance
criteria. For UI work, focused screenshots or rendered evidence remain
temporary evidence in the active task response.

## Escalation and optional review

If the decision cannot be safely bounded in one context, escalate to
`COMPREHENSIVE` before implementation. A user may separately request a
ChatGPT Web review after implementation; use `references/reviewer-protocol.md`.
That review is not a default Standard stage and is not saved unless requested.
