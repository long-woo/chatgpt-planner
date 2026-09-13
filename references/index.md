# Reference Index

Read only the references needed for the current task. New workflows use the
temporary execution context and do not create process artifacts.

| Reference | Purpose | Read when |
|---|---|---|
| `workflow-selection.md` | Mode routing, risk boundaries, and escalation | Before planning |
| `workflow.md` | Shared responsibilities and lifecycle | Starting or resuming work |
| `lite-workflow.md` | Lite eligibility and compact path | Operating Lite |
| `standard-workflow.md` | Standard analysis depth and path | Operating Standard |
| `execution-context.md` | Six-field temporary handoff and validation | Before ChatGPT Web planning |
| `planner-protocol.md` | One-turn Planner behavior and prompt rules | Interacting with ChatGPT Web |
| `ui-design-context.md` | UI evidence and constraint rules | UI-changing work |
| `browser-workflow.md` | ChatGPT Web interaction and context transfer | Browser planning |
| `failure-handling.md` | Browser, context, conflict, and verification recovery | When a handoff/check fails |
| `reviewer-protocol.md` | Optional post-implementation ChatGPT review | Only when review is requested |
| `optional-artifacts.md` | Explicit opt-in durable documents and legacy inputs | Only when saving is requested |
| `decision-records.md` | Optional cross-iteration confirmed decisions | Existing records or explicit request |

Legacy stage references were removed. Existing project-level planning files
may still be read as plain compatibility input when an old workflow is resumed;
they are not part of this skill's active reference set.
