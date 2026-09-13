# Collaboration Workflow

This is the high-level workflow for all three risk-adaptive modes. Read
`workflow-selection.md` first, then load only the references needed for the
selected mode.

## Flow

```text
User Request → ChatGPT Web analysis → temporary execution context
→ Codex implementation → verification
```

The context is the single planning handoff. It is carried in the active
conversation and current task memory. It is not written to the repository.

## Responsibilities

ChatGPT Web owns requirement interpretation, product/UI intent, bounded
planning, acceptance criteria, and design judgment when needed. It must not
write production code.

Codex owns repository reconnaissance, implementation choices that fit verified
repository facts, code changes, tests, and verification. It must not silently
change product behavior.

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
6. Codex runs the required verification and reports evidence.

If a material ambiguity or repository conflict appears, pause implementation
and obtain a replacement context. Do not preserve the intermediate analysis in
a process artifact.

## Compatibility

Existing planning files may be read when resuming a legacy workflow. They are
not required for new work and must not be generated automatically. Use
`references/optional-artifacts.md` for explicit document requests.
