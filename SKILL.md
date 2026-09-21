---
name: chatgpt-planner
description: >-
  Use a risk-adaptive ChatGPT Web and Codex workflow for software changes.
  ChatGPT Web owns product intent, planning, and verification; Codex inspects,
  implements, and collects implementation evidence using one temporary
  execution context.
metadata:
  version: "3.1.0"
---

# ChatGPT Planner

Use a two-agent development workflow:

- **ChatGPT Web** understands the request, makes product/UI decisions, and
  produces one bounded execution context.
- **Codex** supplies verified repository facts, implements the requested
  change, and collects raw test, build, and UI evidence. It does not own the
  final verification judgment.
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
  → Codex evidence collection
  → ChatGPT Web verification
  → DONE or bounded fix loop
```

The execution context is transferred in the conversation and kept in memory
for the active task. New executions must not create `.md` or `.json` process
artifacts, including requirement reviews, design handoffs, engineering
analyses, task contracts, change results, visual reviews, or final reviews.

When the request changes a UI surface, the ChatGPT Web analysis includes the
Automatic UI Design subroutine. It produces implementation-ready design intent
inside `implementation_constraints` before Codex edits the UI. The current
client must not design the UI, and Codex must not replace ChatGPT Web's design
decisions with its own product or visual direction.

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
not a reason to add fields to the execution context. For UI work, the current
client also derives `ui_design_mode`; it is routing metadata, not another
context field.

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
TRIAGE → CONTEXT → IMPLEMENTATION → EVIDENCE → CHATGPT_WEB_VERIFICATION → DONE
```

ChatGPT Web performs a compact analysis and returns one execution context.
There is no file output. ChatGPT Web performs the final verification after
Codex submits the implementation evidence.

### STANDARD

Use for a bounded feature, page, endpoint, data-model adjustment, or
user-visible flow that needs more complete requirement and design reasoning.

```text
ANALYSIS → CONTEXT → IMPLEMENTATION → EVIDENCE → CHATGPT_WEB_VERIFICATION → DONE
```

ChatGPT Web may reason through requirements, product behavior, UI states, and
implementation boundaries internally, but returns only one execution context.
There is no default Requirement Review, Design Handoff, Task Contract, or
Review file, but ChatGPT Web still verifies the implementation in the active
conversation.

### COMPREHENSIVE

Use for architecture changes, core journeys, security/privacy/authorization,
irreversible data behavior, or major release capabilities.

```text
DEEP_ANALYSIS → CONTEXT → IMPLEMENTATION → EVIDENCE → CHATGPT_WEB_VERIFICATION → DONE
```

ChatGPT Web may perform the complete product, UI, risk, and planning analysis
needed for the request. It still returns one execution context and does not
write intermediate artifacts by default. Codex performs repository analysis as
needed for implementation and submits evidence; ChatGPT Web performs the
verification without creating engineering or review documents.

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
inputs, existing-pattern constraints, required states, and the result of the
Automatic UI Design subroutine in `implementation_constraints`; do not create a
separate design handoff. Read `references/auto-ui-design.md` for the routing
and output rules.

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

## Evidence collection and ChatGPT Web verification

Codex runs the checks listed in `verification`, plus proportionate local
checks needed to collect evidence. Report raw commands and results, relevant
manual/UI evidence, deviations, and verification gaps to ChatGPT Web in the
active task conversation. Do not write a `change-result`, `visual-review`, or
`final-review` file by default.

ChatGPT Web verification is the default completion gate for all three modes.
Codex must not turn evidence into a final PASS. A check that could not be run
must remain a gap; it must not be reported as passed.

For UI work, Codex collects only focused screenshots or rendered evidence
needed to check the acceptance criteria. ChatGPT Web performs the visual
judgment in the verification turn, and it does not create a file unless the
user explicitly asks to save the review.

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

ChatGPT Web owns product intent and verification judgment. Codex owns
repository facts, implementation, and raw technical evidence collection.

## Completion

The task is complete when:

- the execution context bounded the work;
- Codex implemented only the requested scope;
- ChatGPT Web judged every required acceptance criterion as satisfied;
- ChatGPT Web judged the submitted verification evidence sufficient, with no
  unresolved gap; and
- no unresolved product or technical blocker remains.

Do not use the presence of a workflow-state file or any legacy artifact as a
completion requirement for new work.
