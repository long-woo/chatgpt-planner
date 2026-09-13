# Temporary Execution Context

The execution context is the only normal planning handoff from ChatGPT Web to
Codex. It is returned once in the active conversation, validated by the
current client, and kept in memory for the task. It must not be written to a
project `.md` or `.json` file.

## Shape

Return exactly one JSON object with exactly six top-level fields:

```json
{
  "goal": "One concise statement of the intended outcome.",
  "scope": [
    "Included behavior, surface, or bounded implementation area."
  ],
  "non_goals": [
    "Explicitly excluded behavior, refactors, integrations, or polish."
  ],
  "implementation_constraints": [
    "Confirmed product, design, repository, compatibility, or technical constraint."
  ],
  "acceptance_criteria": [
    "Observable condition that establishes completion."
  ],
  "verification": [
    {
      "check": "Concrete test command, build check, or manual/UI validation.",
      "required": true
    }
  ]
}
```

## Validation

- `goal` is a non-empty string and states an outcome, not a file edit.
- `scope`, `non_goals`, `implementation_constraints`, and
  `acceptance_criteria` are concise arrays of non-empty strings.
- `acceptance_criteria` are observable or meaningful system conditions.
- `verification` contains at least one concrete check for implementation work;
  each item has a non-empty `check` and boolean `required`.
- The context does not introduce behavior absent from the user request,
  verified repository facts, or confirmed design input.
- Empty `non_goals` or constraints are allowed only when there is genuinely
  nothing material to exclude or constrain; do not invent filler.

## Content boundary

Keep the context minimal. Do not add summary, requirements, task lists,
assumptions, risks, repository facts, review statuses, artifact paths, or
intermediate reasoning as extra fields. Put a necessary boundary in the most
appropriate of the six fields.

For UI requests, place source status, required states, accessibility needs,
and unresolved visual choices in `implementation_constraints`. Do not create a
separate design handoff.

For plan-only requests, the same context is returned but Codex does not edit
code. For implementation requests, Codex uses it as the scope boundary.

## Lifecycle

The context is temporary by default. If the user explicitly asks to save it,
use the smallest requested durable format and read
`references/optional-artifacts.md`.
