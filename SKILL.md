---
name: chatgpt-planner
description: >-
  Use a risk-adaptive ChatGPT Web and Codex workflow for software changes.
  ChatGPT Web owns product intent and planning; Codex inspects, implements,
  and verifies the repository using one temporary execution context.
metadata:
  version: "3.0.0"
---

# ChatGPT Planner

Use a two-agent development workflow:

- **ChatGPT Web** understands the request, makes product/UI decisions, and
  produces one bounded execution context.
- **Codex** verifies repository facts, implements the requested change, and
  performs verification.
- **The current client** transfers the temporary context between the two
  agents.

Core rule:

> ChatGPT Web decides what should be built. Codex determines how it fits the
> real repository and implements it.

ChatGPT Web must not modify production code. Codex must not silently redefine
product requirements.

## Default workflow

The default workflow is deliberately short:

```text
User Request
  → ChatGPT Web analysis
  → one temporary structured execution context
  → Codex implementation
  → Codex verification
```

The execution context is transferred in the conversation and kept in memory
for the active task. New executions must not create `.md` or `.json` process
artifacts, including requirement reviews, design handoffs, engineering
analyses, task contracts, change results, visual reviews, or final reviews.

Do not persist intermediate analysis, repeat the user's request in multiple
documents, or create product documentation as part of the default flow.

The only normal handoff from ChatGPT Web is the six-field context defined in
`references/execution-context.md`:

```text
goal
scope
non_goals
implementation_constraints
acceptance_criteria
verification
```

`workflow_mode` is orchestration metadata held by the current client. It is
not a reason to add fields to the execution context.

## Select a workflow mode

Select `LITE`, `STANDARD`, or `COMPREHENSIVE` before asking ChatGPT Web to
produce the context. Read `references/workflow-selection.md` for routing.
When signals conflict, choose the safer, more comprehensive mode. The mode
changes analysis depth, not the handoff format or artifact policy.

### LITE

Use for a clear, localized, reversible, objectively verifiable change with no
material product/UI decision, public API, persistence, migration,
authentication, authorization, privacy, security, billing, payment, or
destructive impact.

```text
TRIAGE → CONTEXT → IMPLEMENTATION → VERIFICATION → DONE
```

ChatGPT Web performs a compact analysis and returns one execution context.
There is no file output and no default second review.

### STANDARD

Use for a bounded feature, page, endpoint, data-model adjustment, or
user-visible flow that needs more complete requirement and design reasoning.

```text
ANALYSIS → CONTEXT → IMPLEMENTATION → VERIFICATION → DONE
```

ChatGPT Web may reason through requirements, product behavior, UI states, and
implementation boundaries internally, but returns only one execution context.
There is no default Requirement Review, Design Handoff, Task Contract, or
Review file.

### COMPREHENSIVE

Use for architecture changes, core journeys, security/privacy/authorization,
irreversible data behavior, or major release capabilities.

```text
DEEP_ANALYSIS → CONTEXT → IMPLEMENTATION → VERIFICATION → DONE
```

ChatGPT Web may perform the complete product, UI, risk, and planning analysis
needed for the request. It still returns one execution context and does not
write intermediate artifacts by default. Codex performs repository analysis
and verification as part of implementation without creating engineering or
review documents.

## Planning and context rules

Read `references/planner-protocol.md` and
`references/execution-context.md` when interacting with ChatGPT Web.

Send enough standalone information for one planning turn:

- the current user request;
- concise project context;
- verified repository facts relevant to the request;
- real constraints, including available verification commands.

ChatGPT Web should analyze silently and return exactly one execution context.
The context must be bounded, observable, and implementation-ready. It must not
contain a duplicated requirement-review narrative, a task decomposition
artifact, speculative cleanup, or invented repository facts.

For UI work, use `references/ui-design-context.md`. Put confirmed design
inputs, existing-pattern constraints, required states, and unresolved visual
decisions in `implementation_constraints`; do not create a separate design
handoff.

If a material product, data, security, compatibility, or architecture decision
is unresolved, ask the user before producing the final context. Do not produce
partial planning artifacts to hold the question.

## Browser interaction

Read `references/browser-workflow.md` when opening or interacting with
ChatGPT Web. Use a normal Chat mode conversation; never use Work mode for
planning. Keep one dedicated Planner conversation when practical, but send
the complete current context with each planning turn instead of relying on
conversation memory.

## Implementation

Before editing production code, Codex must have:

- a selected `workflow_mode` in the current task state;
- one validated execution context in memory;
- verified repository facts that do not materially conflict with that context.

Implement only the context's `scope`, honoring `non_goals` and
`implementation_constraints`. Codex may choose repository-specific details,
but must not add product behavior. Keep unrelated discoveries under
`DISCOVERED_ISSUES` in the response; do not create a process file for them.

If repository facts conflict with the context, stop and use the bounded
`PLAN_CORRECTION` procedure in `references/browser-workflow.md` to request a
replacement context. Do not silently reinterpret the product intent.

## Verification

Codex runs the checks listed in `verification`, plus proportionate local
checks needed to protect the repository. Report commands, results, relevant
manual/UI evidence, deviations, and verification gaps in the final response
or active task conversation. Do not write a `change-result`, `visual-review`,
or `final-review` file by default.

Verification is the default completion gate for all three modes. A check that
could not be run must remain a gap; it must not be reported as passed.

For UI work, collect only focused screenshots or rendered evidence needed to
check the acceptance criteria. Visual judgment may be requested in chat, but
it does not create a file unless the user explicitly asks to save the review.

## Optional artifact extension

Artifact generation remains available as an explicit opt-in extension. Read
`references/optional-artifacts.md` only when the user asks to save a design
document, plan, review, decision record, or another durable handoff.

When explicitly requested, generate only the requested document and preserve
the six-field execution context as the source of truth. Do not reinstate the
old multi-artifact pipeline or create additional files for completeness.

Existing `.chatgpt/` files and legacy root-level planning files are readable
compatibility inputs. Do not delete, overwrite, or require them for a new
workflow. If an old workflow is active, either continue it without adding new
artifacts or collapse its approved information into one temporary execution
context.

## Authority

When information conflicts, use this priority:

1. explicit current user instruction;
2. confirmed product and design intent;
3. verified repository facts for current architecture;
4. the accepted execution context;
5. implementation preference.

ChatGPT Web owns product intent. Codex owns repository facts and technical
verification.

## Completion

The task is complete when:

- the execution context bounded the work;
- Codex implemented only the requested scope;
- every required acceptance criterion is established;
- required verification passed or any gap is clearly disclosed; and
- no unresolved product or technical blocker remains.

Do not use the presence of a workflow-state file or any legacy artifact as a
completion requirement for new work.
