# Browser Workflow

Use the desktop client's browser/computer-use capability to interact with
ChatGPT Web when available. If it cannot be used, fall back to the system
default browser at `https://chatgpt.com`.

The Planner must be a normal ChatGPT Web conversation in Chat mode. Never use
Work mode for planning, create a Work task, or ask ChatGPT Web to modify
production code.

## One planning interaction

Prefer one dedicated Planner conversation per project, but make each planning
turn standalone. Send only:

- `USER_REQUEST`;
- concise `PROJECT_CONTEXT`;
- verified `REPO_CONTEXT`;
- relevant `CONSTRAINTS`;
- the selected mode as routing metadata.

For a UI request, also derive `ui_design_mode` from
`references/auto-ui-design.md`. Keep it as routing metadata and instruct
ChatGPT Web to place the generated design constraints in the existing
`implementation_constraints` field. ChatGPT Web must perform the UI design in
the planning turn; do not ask Codex or the current client to invent the visual
direction.

Ask ChatGPT Web to analyze internally and return exactly one six-field
execution context from `references/execution-context.md`. The current client
transfers that response to Codex in memory. Do not write it to `.md` or `.json`
and do not paste the same analysis into multiple messages.

Do not upload credentials, secrets, or the entire repository merely to provide
context. If authentication is required, let the user authenticate directly in
the browser.

## Plan correction

If verified repository facts conflict with the context, send:

```text
PLAN_CORRECTION

Planner context:
...

Repository fact:
...

Why they conflict:
...

Recommended correction:
...
```

Ask for a complete replacement execution context with exactly the six allowed
fields. Never ask for a JSON patch and never silently reinterpret product
intent.

## ChatGPT Web verification

After Codex implementation, return to the same ChatGPT Web conversation and
send the raw implementation evidence required by
`references/reviewer-protocol.md`. ChatGPT Web must judge the evidence against
the execution context and return `PASS`, `NEEDS_FIX`, or `BLOCKED`. Keep the
verification in chat unless the user explicitly asks to save it.

## Verification evidence

For UI work, send only focused screenshots or rendered evidence needed to
establish an acceptance criterion. Identify the route/screen, state,
viewport/device, and observed result. Do not create a Visual QA file unless
the user explicitly requests a saved review.
