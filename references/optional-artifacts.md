# Optional Artifacts

Artifacts are opt-in. Do not read this reference or create any process file
unless the user explicitly asks to save a plan, design document, review,
decision record, or another durable handoff.

## Rules

- The temporary execution context remains the source of truth.
- Create only the specifically requested document or format.
- Do not generate the former artifact bundle for completeness.
- Do not persist intermediate reasoning or duplicate the user request.
- Preserve existing files unless the user explicitly asks to update them.
- Do not overwrite legacy `.chatgpt/` or root-level files silently.
- Saving a document does not authorize production-code changes by itself.

## Explicit examples

- “Save the design” may create one concise design document containing only the
  relevant product/UI decisions from the context.
- “Export the execution context as JSON” may create one context JSON file.
- “Keep a decision record” may create or update
  `.chatgpt/decision-records.md` after explicit confirmation.

Do not automatically create `requirement-review.md`, `product-design.md`,
`design-handoff.md`, `engineering-analysis.md`, `task-contract.json`,
`final-review.md`, `visual-review.md`, `change-result.json`, or workflow state
files. The only bundled template is `templates/decision-records.md`, retained
for explicit decision-record requests; it is never copied by the default
workflow.
