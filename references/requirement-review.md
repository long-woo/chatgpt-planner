# Requirement Review

Requirement Review is the scope-control gate before product or UI design. It
is the first approval gate in `workflow-state.json`.
ChatGPT Web owns the product judgment; Codex may provide repository facts but
must not resolve product ambiguity by coding.

## Inputs

Provide ChatGPT Web with:

- `USER_REQUEST`
- `PROJECT_CONTEXT`
- the relevant `REPO_CONTEXT`
- explicit constraints and user-provided references

The review must be grounded in the request and verified context. Do not turn
possible future improvements into requirements.

## Output

Write `.chatgpt/requirement-review.md` in the shared working directory from
`templates/requirement-review.md`. Use this structure:

```markdown
# Requirement Review

status: NOT_STARTED | IN_PROGRESS | WAITING_REVIEW | APPROVED | BLOCKED | COMPLETED
stage: REQUIREMENT_REVIEW

## User goal
- The user's underlying outcome, in plain language.

## Core problem
- The current user or business problem causing the request.

## Core requirements
- REQ-1: Observable required behavior.

## Non-goals
- Explicitly excluded behavior, refactors, integrations, or polish.

## MVP boundary
- Included in this iteration.
- Deferred or intentionally omitted.

## Risks
- Requirement, scope, compatibility, data, security, or delivery risk.

## Scope guardrails
- Decisions that prevent the implementation from expanding scope.

## Open questions
- Only blocking questions that materially affect behavior, data, security,
  compatibility, cost, or irreversible architecture.
```

`WAITING_REVIEW` means the artifact is ready for approval; `APPROVED` unlocks
Product Design. `BLOCKED` stops downstream design and implementation until the
material decision is resolved. A non-blocking uncertainty belongs in
assumptions or non-goals instead. If a legacy artifact uses `READY` or
`NEEDS_CLARIFICATION`, preserve its meaning while migrating the stage state to
the canonical state vocabulary.

## Gate

Before product design begins, Codex checks that the artifact contains a real
user goal, core problem, must-have requirements, non-goals, MVP boundary, and
risks. If the artifact conflicts with the explicit current user request, the
current user request wins and ChatGPT Web must revise the artifact.

Existing root-level `requirement-review.md` may be read as a legacy input when
`.chatgpt/requirement-review.md` is absent; new runs write the canonical path.
