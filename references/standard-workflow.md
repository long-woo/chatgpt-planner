# Standard Workflow

Use `STANDARD` for a bounded feature or contract change that needs more
complete requirement and design alignment than Lite, but does not cross a
Comprehensive risk boundary.

## Flow

```text
ANALYSIS → CONTEXT → IMPLEMENTATION → EVIDENCE
→ CHATGPT_WEB_VERIFICATION → DONE
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

Codex validates repository fit, implements the scope, and collects raw command
results and focused UI evidence. ChatGPT Web verifies the acceptance criteria
against that evidence in the active task conversation. For UI work, screenshots
or rendered evidence remain temporary evidence in the active task response.

## Escalation and verification

If the decision cannot be safely bounded in one context, escalate to
`COMPREHENSIVE` before implementation. After implementation, use
`references/reviewer-protocol.md` for the required ChatGPT Web verification.
It remains in chat and is not saved unless the user explicitly requests a
durable review.
