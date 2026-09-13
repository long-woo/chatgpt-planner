# Workflow Selection

`workflow_mode` is an in-memory routing decision. Select it before the single
ChatGPT Web planning turn; it is not bound to a state file or artifact.

## Selection order

1. Choose `COMPREHENSIVE` for architecture changes, core user journeys,
   security/privacy/authorization boundaries, irreversible data behavior, or
   major release capabilities.
2. Otherwise choose `STANDARD` for a page, endpoint, medium-sized feature,
   data-model adjustment, or user-visible flow needing product/design
   reasoning.
3. Choose `LITE` only for clear, local, reversible, objectively verifiable
   work with no material product or architecture decision.

When signals conflict, choose the more comprehensive mode. A short diff does
not make risky work Lite-eligible.

## Default classification

| Mode | Typical scope | Analysis depth |
|---|---|---|
| `LITE` | Small bug fix, copy change, style adjustment, local correction | Compact |
| `STANDARD` | New page/API, medium feature, data-model or user-flow change | Complete |
| `COMPREHENSIVE` | Core flow, architecture, security, irreversible data, major release | Full |

All modes return the same six-field execution context and create no planning
artifact by default.

## Escalation

Escalate before continuing if implementation or verification discovers:

- a new product or design decision;
- public API, persistence, migration, compatibility, authentication,
  authorization, privacy, security, billing, payment, or destructive impact;
- a broader page, endpoint, feature, core flow, or architecture boundary;
- verification that cannot establish correctness locally.

Stop the smaller-mode implementation. Ask ChatGPT Web for one replacement
execution context at the safer mode, carrying only the original request,
verified repository facts, and the newly discovered decision. Do not create a
partial artifact during escalation.

## Explicit user mode

Honor an explicit mode when safe. A request to use `LITE` does not bypass a
Comprehensive boundary. A request to save a plan or design document enables
the opt-in artifact extension described in `optional-artifacts.md`; it does
not change the selected mode.
