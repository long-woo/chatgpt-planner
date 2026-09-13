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

## Optional review

Do not return to ChatGPT Web for a default review. If the user explicitly asks
for a product/design review, use `references/reviewer-protocol.md` and send
focused implementation evidence. Keep the review in chat unless the user
also explicitly asks to save it.

## Verification evidence

For UI work, send only focused screenshots or rendered evidence needed to
establish an acceptance criterion. Identify the route/screen, state,
viewport/device, and observed result. Do not create a Visual QA file unless
the user explicitly requests a saved review.
