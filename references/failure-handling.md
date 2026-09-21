# Failure Handling

## Browser unavailable

Try the system default browser at `https://chatgpt.com` if the built-in browser
cannot be used. Continue once a normal ChatGPT Web conversation is available.
Do not replace ChatGPT Web with search results or pretend the planning turn
occurred. Only report the Planner as blocked when neither browser path works.

## Authentication

Let the user authenticate directly in the active browser. Never request or
store passwords, cookies, session tokens, or other authentication secrets.

## Malformed execution context

Treat formatting failure separately from missing planning. First perform only
lossless normalization: remove a surrounding Markdown fence or isolate one
complete JSON object. Accept it only when it parses and validates against
`execution-context.md`.

If that fails, make one concise repair request in the same Planner
conversation, asking for the complete six-field JSON object and identifying
the exact parse/schema issue. This is the only automatic retry for that
planning turn. Do not persist the raw response, create a partial file, or add
missing product requirements during repair.

If the replacement is still unusable, stop and ask the user or report the
Planner as blocked. Codex must not invent product decisions to make up for a
missing context.

## Repository conflict

Use the `PLAN_CORRECTION` procedure in `browser-workflow.md`. Request one
complete replacement context. Do not edit production code until the conflict
is resolved.

## Verification failure

ChatGPT Web owns the final verification judgment. If it returns `NEEDS_FIX`,
keep the context scope fixed. Codex diagnoses and fixes only failures caused by
the requested change, reruns the affected checks to collect new evidence, and
sends that evidence back to ChatGPT Web. ChatGPT Web then returns the next
`PASS`, `NEEDS_FIX`, or `BLOCKED` judgment.

If a failure exposes a new product, architecture, data, security, or
compatibility decision, stop and escalate to a safer mode instead of expanding
scope. Report failed and unavailable checks accurately. Never convert
`not_run` into `passed`, and never let Codex declare a final PASS.

## Legacy files

Existing `.chatgpt/` state, requirement, design, engineering, task-contract,
result, or review files may be read as plain context when resuming a legacy
workflow. They are compatibility inputs only. Do not delete, overwrite, or
require them for new work. Use `optional-artifacts.md` for explicit durable
output.
