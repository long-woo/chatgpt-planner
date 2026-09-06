# Design Handoff

Design Handoff is the contract between ChatGPT Web's product/UI design work
and Codex's implementation. It is required for UI-changing work and is marked
not applicable for backend-only or otherwise non-visual work.

ChatGPT Web owns design intent and user experience decisions. Codex may report
what the repository can support, but must not replace missing design input with
an invented visual system.

## Inputs

Use:

- `.chatgpt/requirement-review.md`
- confirmed user-provided design references
- `ui-design-context.md`
- relevant repository patterns and constraints

Distinguish confirmed, inferred, and unresolved design information. Keep the
handoff bounded by the MVP and non-goals in the Requirement Review.

## Output

Write `.chatgpt/design-handoff.md` in the shared working directory from
`templates/design-handoff.md`. Use this structure:

```markdown
# Design Handoff

status: NOT_STARTED | IN_PROGRESS | WAITING_REVIEW | APPROVED | BLOCKED | COMPLETED
stage: DESIGN_HANDOFF

## Design goal
- The intended user outcome and visual/interaction purpose.

## Key experiences
- UX-1: The important user journey or interaction.

## Core interactions
- Entry point, action, feedback, success/error, and return path.

## Page or surface priority
1. Primary surface — why it matters and what must work first.
2. Secondary surface — scope and dependency.

## Invariants and design constraints
- Constraints Codex must not change during implementation.

## Prohibited changes
- Pages, behaviors, data, components, or visual constraints outside this handoff.

## Required states
- Loading, empty, error, disabled, success, responsive, or accessibility
  states that apply.

## Unresolved decisions
- Decisions deliberately left open, or `None`.

## Evidence
- Confirmed reference or repository pattern supporting the handoff.
```

The `Invariants and design constraints` section is normative. Use an explicit
`NOT_APPLICABLE` record inside the artifact for non-UI work; the workflow stage
itself still uses the shared state vocabulary. The `Evidence`
section must not claim that a screenshot, Figma file, or design system was
inspected unless it was actually available and inspected.

## Gate

Codex reads this artifact before engineering analysis. If a required behavior
or visual constraint is missing and materially changes implementation scope,
send a bounded question or `PLAN_CORRECTION` to ChatGPT Web. Do not silently
invent the missing decision.

Existing root-level `design-handoff.md` may be read as a legacy input when the
canonical file is absent; new runs write the canonical path.
