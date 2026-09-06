# Decision Records

Decision Records preserve confirmed product, design, and technical constraints
that must remain consistent across iterations and across ChatGPT Web and Codex.
They are durable project context, not a replacement for the workflow's
requirement or design artifacts.

## Purpose and boundaries

Use a Decision Record for an important decision that has been explicitly
confirmed and is likely to affect future product or engineering work. The
record should capture the chosen direction, why it was chosen, and what it
constrains.

Decision Records are not:

- a requirements document; use Requirement Review for the scope and acceptance
  boundary of the current request;
- a replacement for `ui-design-context.md` or Design Handoff; those artifacts
  capture design evidence, current UI intent, and implementation-facing detail;
- a permanent prohibition on change; an existing decision can be revisited
  through a new review;
- a place for ordinary implementation notes or unresolved ideas.

## What belongs in the record

Include confirmed decisions about:

- product direction, positioning, prioritization, or important non-goals;
- information architecture, navigation, ownership, or content hierarchy;
- core user flows, interaction models, or behavioral boundaries;
- UI/UX principles that should remain consistent across surfaces;
- technical constraints that materially limit product or implementation choices.

Do not include:

- ordinary code implementation details or local refactoring choices;
- temporary workarounds, experiments, or unresolved proposals;
- styling parameters for a single component, page, or isolated state;
- facts that are already fully captured by a current requirement or handoff and
  have no cross-iteration consequence.

## Lifecycle and status

Each record has a stable identifier such as `Decision-001` and a status. Use
`ACTIVE` for the current decision. When a later review replaces it, retain the
original text and mark it `SUPERSEDED`, then add a new record explaining the
new direction and linking the related decision. Use `DEPRECATED` only when the
decision no longer applies and has no direct replacement.

Create or update a record only after the decision has been explicitly confirmed
through the applicable product, design, or technical review. Do not promote an
assumption, inference, implementation preference, or ChatGPT Web suggestion to
an active decision without confirmation.

Changing an active decision requires re-review. A new request that conflicts
with an active record is a decision conflict: surface the conflict, identify
the affected record, and route the proposed change through Requirement Review
and the applicable Product Design, Design, or Engineering Analysis gate before
implementation. Do not silently rewrite the old record or implement the
conflicting direction as if the record did not exist.

Preserve the history of changed decisions. Prefer appending a new record and
marking the old one superseded over editing away the earlier rationale.

## Operating rules

1. At the start of a workflow, read the active records in
   `.chatgpt/decision-records.md` when the file exists.
2. ChatGPT Web and Codex must use relevant active records as constraints during
   requirement review, product/design decisions, task planning, engineering
   analysis, implementation, and final review.
3. When a proposed requirement or plan agrees with a record, reference the
   record ID in the relevant artifact or plan so the decision remains
   traceable.
4. When a proposed change conflicts with a record, stop the affected
   downstream work until the conflict is resolved by the responsible review
   stage. Record the outcome before continuing.
5. Codex may report repository facts and propose technical implications, but it
   must not unilaterally overturn a confirmed product or design decision.
6. Keep records concise and decision-focused. Put supporting detail in the
   requirement review, design handoff, engineering analysis, or task contract.

## Canonical location and template

The canonical project artifact is `.chatgpt/decision-records.md`. Start it from
`templates/decision-records.md` when the project needs decision tracking. The
file may contain multiple records; do not create a separate file for every
decision unless a project explicitly requires that organization.

At minimum, each record should state:

- the date and status;
- the decision itself;
- the reason for choosing it;
- the expected product, design, or technical impact;
- related requirement or artifact identifiers;
- notes or links needed to understand its history.
