---
name: chatgpt-planner
description: >-
  Use ChatGPT Web in the desktop client's built-in browser as the product and
  engineering planner/reviewer, while Codex explores the repository, implements
  code, runs tests, and fixes defects. Use for feature development, bug fixes,
  refactors, UI changes, architecture work, and other non-trivial coding tasks.
metadata:
  version: "1.0.0"
---

# ChatGPT Planner

Use a two-agent development workflow:

- **ChatGPT Web**: requirement understanding, planning, task decomposition,
  acceptance criteria, final requirement review.
- **Codex**: repository exploration, implementation, tests, verification.
- **Current client**: orchestrates the workflow between both agents.

Core rule:

> ChatGPT Web decides what should be built.
> Codex determines what the repository actually contains and how to implement it.

Do not let ChatGPT Web directly modify production code.

Do not let Codex silently redefine product requirements.

## Workflow

For normal implementation tasks:

1. Codex performs lightweight repository reconnaissance.
2. Build a concise `REPO_CONTEXT`.
3. Open ChatGPT Web using the client's built-in browser.
4. Ask ChatGPT Web to produce a structured implementation plan.
5. Codex validates that plan against repository facts.
6. If repository facts conflict with the plan, send corrections back to ChatGPT Web.
7. Codex implements accepted tasks in dependency order.
8. Run appropriate tests, lint, typecheck, build, or manual verification.
9. Send the implementation result back to the same ChatGPT Web conversation.
10. ChatGPT Web reviews requirement coverage.
11. If `NEEDS_FIX`, Codex performs bounded fixes and requests review again.
12. Finish when review returns `PASS`, or report a blocker.

## Before planning

Codex should collect only repository facts relevant to the requirement:

- stack
- relevant modules
- existing behavior
- APIs/data models
- architectural constraints
- available verification commands

Do not start implementation during reconnaissance.

## Planning

Read:

`references/planner-protocol.md`

and:

`references/task-contract.md`

Use them when interacting with ChatGPT Web.

## Browser interaction

Read:

`references/browser-workflow.md`

when opening or interacting with ChatGPT Web.

## Implementation

Implement only tasks from the accepted plan.

Codex may decide implementation details but must not independently introduce
new product behavior.

Keep unrelated discoveries under:

`DISCOVERED_ISSUES`

Do not perform unrelated cleanup unless it blocks the requested work.

## Review

After implementation and local verification, read:

`references/reviewer-protocol.md`

Send the implementation result to the same ChatGPT Web conversation.

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

Do not claim full completion unless:

- ChatGPT Web created the plan
- repository facts validated the plan
- Codex implemented the accepted tasks
- practical verification was performed
- ChatGPT Web reviewed the implementation
- final review returned `PASS`
