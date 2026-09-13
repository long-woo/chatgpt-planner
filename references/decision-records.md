# Optional Decision Records

Decision Records are optional durable context for decisions that should remain
consistent across iterations. They are not part of the default execution
workflow and must not be created automatically.

## Use and boundaries

Read an existing `.chatgpt/decision-records.md` when a request may conflict
with an active confirmed decision. Apply it as a constraint while ChatGPT Web
produces the temporary execution context.

Create or update a record only when the user explicitly asks to preserve a
confirmed product, design, or technical decision. Keep it concise and separate
from the current task's temporary context.

Decision Records are not:

- a requirement review or current task plan;
- a replacement for the six-field execution context;
- a place for ordinary implementation notes, guesses, or unresolved ideas;
- permission to modify production code.

## Lifecycle

Use a stable identifier and `ACTIVE`, `SUPERSEDED`, or `DEPRECATED` status.
Only explicitly confirmed decisions may become `ACTIVE`. To change an active
decision, surface the conflict to the user and update the record only after
confirmation; do not silently rewrite historical rationale.

## Compatibility

Existing decision records are read-only inputs unless the user explicitly
requests an update. The legacy location is `.chatgpt/decision-records.md` and
the optional starter is `templates/decision-records.md`.
