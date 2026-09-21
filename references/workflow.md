# Collaboration Workflow

This is the high-level workflow for all three risk-adaptive modes. Read
`workflow-selection.md` first, then load only the references needed for the
selected mode.

## Flow

```text
User Request → ChatGPT Web analysis → temporary execution context
→ Codex implementation → Codex evidence collection
→ ChatGPT Web verification → DONE or bounded fix loop
```

The context is the single planning handoff. It is carried in the active
conversation and current task memory. It is not written to the repository.

## Responsibilities

ChatGPT Web owns requirement interpretation, product/UI intent, bounded
planning, acceptance criteria, and design judgment when needed. It must not
write production code.

Codex owns repository reconnaissance, implementation choices that fit verified
repository facts, code changes, and raw evidence collection. ChatGPT Web owns
the verification judgment and must not silently change product behavior during
verification.

The current client selects `workflow_mode` and transfers the one context. It
does not initialize a `.chatgpt/` directory or copy planning output into
`.md`/`.json` files for a new task.

## Mode depth

| Mode | ChatGPT Web analysis | Default handoff |
|---|---|---|
| `LITE` | Compact scope and verification analysis | One six-field context |
| `STANDARD` | Complete requirement, product/UI, and boundary analysis | One six-field context |
| `COMPREHENSIVE` | Full product, UI, engineering-risk, and rollout analysis | One six-field context |

The mode changes the amount of reasoning, not the number of handoffs.

## Common gates

1. Select the mode from risk and decision complexity.
2. Codex gathers only repository facts relevant to the request.
3. ChatGPT Web returns one validated execution context.
4. Codex checks repository fit before editing.
5. Codex implements the bounded scope.
6. Codex runs the requested checks only to collect raw evidence.
7. ChatGPT Web evaluates the evidence against the acceptance criteria and
   returns `PASS`, `NEEDS_FIX`, or `BLOCKED`.

If a material ambiguity or repository conflict appears, pause implementation
and obtain a replacement context. Do not preserve the intermediate analysis in
a process artifact.

For UI work, steps 2–3 include the Automatic UI Design subroutine. It selects
`REUSE_PATTERN`, `REFERENCE_ADAPTATION`, or `AUTO_DESIGN` and places the
resulting design constraints in `implementation_constraints` without adding a
second handoff or file. ChatGPT Web performs the design and verification; the
current client only selects/transfers context, and Codex only implements and
collects evidence.

## Compatibility

Existing planning files may be read when resuming a legacy workflow. They are
not required for new work and must not be generated automatically. Use
`references/optional-artifacts.md` for explicit document requests.
