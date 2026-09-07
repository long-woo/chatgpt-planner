# Collaboration Workflow

This is the high-level operating guide for comprehensive work. First read
`lite-workflow.md` to determine whether the request qualifies for the compact
path. Read `workflow-state.md` for the comprehensive machine-readable gate
rules and `index.md` to route detailed stage protocols.

## Profile routing

Use the Lite flow for small, clear, low-risk, localized, objectively verifiable
changes. It combines requirement review, repository analysis, and task
planning into one Change Contract and combines implementation reporting and
verification into one Change Result.

Use this comprehensive flow whenever Lite eligibility is false or uncertain.
If a Lite implementation discovers broader impact, preserve its artifacts and
escalate according to `lite-workflow.md` before continuing.

## Responsibilities

ChatGPT Web owns requirement understanding, product design, UX/UI design,
engineering planning, Visual QA judgment, and final acceptance. Codex owns
repository facts, engineering analysis, code implementation, tests, technical
verification, and collection of visual evidence. The current client carries
artifacts between them.

ChatGPT Web must not write production code. Codex must not silently redefine
product requirements or design constraints.

## Execution sequence

1. Initialize `.chatgpt/workflow-state.json` and collect a concise `REPO_CONTEXT`.
2. ChatGPT Web completes Requirement Review and product design, then UX/UI
   design when the request has a visual surface.
3. Persist the Design Handoff. For non-UI work, persist an explicit
   `NOT_APPLICABLE` record so the gate remains traceable.
4. Codex performs Engineering Analysis before Task Planning. It must not
   implement during analysis.
5. ChatGPT Web creates the Task Contract from all approved inputs. Codex
   validates it against repository facts and requests a bounded correction when
   facts conflict.
6. Codex reads the current state again, implements only accepted tasks, and
   runs the planned verification.
7. Codex supplies screenshots or simulator evidence for UI work. ChatGPT Web
   performs Visual QA; Codex addresses only bounded required fixes.
8. ChatGPT Web performs Final Review. `PASS` unlocks `DONE`; `NEEDS_FIX`
   returns to the responsible implementation/verification loop; `BLOCKED`
   remains stopped until the missing evidence or decision is resolved.

The shared project/work directory is the handoff boundary. ChatGPT Web may
receive exact artifact contents or faithful extracts in the browser conversation
when it cannot access the filesystem directly. Do not require Git metadata.
