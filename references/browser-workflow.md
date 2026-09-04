# Browser Workflow

Use the desktop client's built-in browser/computer-use capability to interact
with ChatGPT Web whenever it is available. If the built-in browser cannot be
opened, cannot load `https://chatgpt.com`, or cannot interact with the page,
fall back to the system default browser and continue the same workflow there.

The Planner must be an actual ChatGPT Web conversation.

Do not substitute normal web search for this step.

The default-browser fallback is equivalent to the built-in browser for
planning and review. Keep using the same dedicated Planner conversation when
possible; if the conversation cannot be recovered, follow the conversation
loss procedure in `failure-handling.md`.

## Opening ChatGPT

Open in the preferred browser:

https://chatgpt.com

If opening or using the preferred built-in browser fails, open that URL in the
system default browser and continue. Do not treat a failed built-in-browser
attempt as a planning blocker if the default browser is available.

Resolve the fallback through the host's configured default-browser mechanism;
do not hard-code a particular browser such as Edge or Chrome.

If authentication is required, allow the user to authenticate directly in the
currently active browser. The user may complete authentication in the default
browser fallback just as they would in the built-in browser.

Never request passwords, cookies, session tokens, or authentication secrets in
the chat.

## Planner conversation

Prefer one dedicated Planner conversation per project.

Suggested naming:

<Project Name> — Product & Engineering Planner

Examples:

Markgo — Product & Engineering Planner

STC — Product & Engineering Planner

Reuse an existing project Planner conversation when it can be confidently
identified.

Otherwise start a new conversation.

## Context policy

Do not rely solely on previous ChatGPT Web conversation memory.

Every request should contain enough context to stand alone.

Send:

USER_REQUEST

PROJECT_CONTEXT

REPO_CONTEXT

CONSTRAINTS

The repository summary should normally be concise.

Do not paste the entire repository.

## Repository correction

ChatGPT Web may make an incorrect assumption about repository architecture.

Codex must validate the plan before implementation.

When a conflict exists, send:

PLAN_CORRECTION

Planner assumption:
...

Repository fact:
...

Why they conflict:
...

Recommended correction:
...

Then ask:

The implementation plan conflicts with verified repository facts.

Treat the repository facts below as authoritative and revise the complete plan.

Return the full updated Task Contract JSON only.

## Same conversation

Planning and final review should normally happen in the same ChatGPT Web
conversation.

This allows the reviewer to understand the accepted requirement and plan.

## Review interaction

After Codex implementation, return to the same conversation and send:

- ORIGINAL_USER_REQUEST
- ACCEPTED_PLAN
- IMPLEMENTATION_RESULT
- relevant diff excerpts only when necessary

Do not send enormous diffs unless required to determine requirement coverage.
