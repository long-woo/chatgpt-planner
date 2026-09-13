# Lite Workflow

Use `LITE` for a clear, localized, reversible, objectively verifiable change.
Its purpose is to preserve product scope without forcing a planning document
or review loop.

## Eligibility

All of these should be true:

- behavior is clear and requires no material product decision;
- impact is localized, normally one module and roughly three production files
  or fewer;
- no public API, persistence schema, migration, authentication,
  authorization, privacy, security, billing, payment, or destructive behavior
  is involved;
- no new visual direction, page flow, information architecture, or
  interaction model is introduced;
- targeted verification is available.

If any criterion is false or uncertain, use `STANDARD` or `COMPREHENSIVE`.

## Flow

```text
TRIAGE → CONTEXT → IMPLEMENTATION → VERIFICATION → DONE
```

Triage and stage status are held in the current task memory. Do not initialize
`.chatgpt/workflow-state.json` or write a Change Contract/Change Result.

## Context

Use one ChatGPT Web turn. Send the user request, concise verified repository
facts, Lite constraints, and the execution-context schema from
`references/execution-context.md`. ChatGPT Web returns exactly one context.

The context must contain a bounded goal, included scope, non-goals,
implementation constraints, observable acceptance criteria, and practical
verification. Codex validates it in memory and does not persist it.

## Implementation and verification

Codex may edit only the bounded scope. Run the listed targeted checks and any
proportionate local check needed to protect the change. Report acceptance
results and command evidence in the active task response.

If the change crosses an escalation boundary, stop, select a safer mode, and
request a replacement context before continuing. Do not add an ad-hoc review
stage or artifact to Lite.
