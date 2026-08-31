# Browser Workflow

Use the desktop client's built-in browser/computer-use capability to interact
with ChatGPT Web.

The Planner must be an actual ChatGPT Web conversation.

Do not substitute normal web search for this step.

## Opening ChatGPT

Open:

https://chatgpt.com

If authentication is required, allow the user to authenticate directly in the
browser.

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
