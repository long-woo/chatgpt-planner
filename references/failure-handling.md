# Failure Handling

## Built-in browser unavailable

When the built-in browser cannot be opened or used:

1. Attempt the workflow in the system default browser at `https://chatgpt.com`.
2. Reuse the existing Planner conversation when it can be confidently
   identified; otherwise create a new dedicated Planner conversation.
3. Continue planning and review normally once ChatGPT Web is usable.

Do not:

- pretend ChatGPT Web was used
- replace the Planner with search-engine results
- silently skip planning

Only report the Planner step as blocked when both the built-in browser and the
system default browser are unavailable or ChatGPT Web cannot be used in either
browser. A Codex-only fallback still requires explicit user permission to
bypass ChatGPT Web.

## ChatGPT authentication required

Open the authentication page in the currently active browser, including the
system default browser fallback.

Allow the user to authenticate directly.

Never ask for authentication secrets.

## Malformed Planner response

If ChatGPT Web does not return the Task Contract:

1. ask once for reformatting
2. resend the expected schema
3. request JSON only

Do not immediately regenerate the plan yourself.

If it remains malformed:

- preserve only unambiguous information
- mark missing fields
- do not fabricate requirements or acceptance criteria

## Missing or conflicting stage artifact

The shared working directory is the durable handoff boundary. If one of the
required artifacts is missing, unreadable, or materially inconsistent:

1. stop before the next dependent stage;
2. recover or rewrite only the affected artifact with the responsible agent;
3. preserve the current user's scope and verified repository facts;
4. continue only when the artifact's status is usable.

The required canonical files are `.chatgpt/workflow-state.json`,
`.chatgpt/requirement-review.md`, `.chatgpt/design-handoff.md`,
`.chatgpt/engineering-analysis.md`, and `.chatgpt/visual-review.md`. A non-UI request
may use `NOT_APPLICABLE` Design, Design Handoff, or Visual QA records, but
must record that status and approve the corresponding state gates. Existing
root-level artifacts are valid legacy fallbacks when the canonical file is
absent.
Do not silently substitute chat memory, Git state, or invented content.

## Repository contradicts Planner

Repository facts are authoritative for repository state.

Do not silently modify the Planner plan.

Send a PLAN_CORRECTION to ChatGPT Web and obtain a revised plan.

## Conversation lost

Open ChatGPT Web again.

If necessary create a new conversation.

Resend:

- USER_REQUEST
- PROJECT_CONTEXT
- REPO_CONTEXT
- ACCEPTED_PLAN
- current IMPLEMENTATION_RESULT

The workflow must not depend on conversation history alone.

## Test failure

If implementation tests fail:

Codex should first determine whether the failure was caused by its changes.

Fix failures caused by the current implementation when they are within scope.

If an unrelated pre-existing failure exists:

- record it
- identify evidence that it predates the change when possible
- do not claim full test success

## Verification unavailable

Use:

passed
failed
not_run

Never convert `not_run` into `passed`.

State why verification was unavailable.

For Visual QA, distinguish `BLOCKED` from `PASS`: missing screenshots,
simulator access, or browser access means the visual check was not established.
Use available fallback browser/computer-use tools where possible, and record
the specific gap in `.chatgpt/visual-review.md`.

## Optional Web verification tool unavailable

`chrome-devtools-mcp` is an optional enhancement for Web verification, not a
workflow dependency. If it is missing, notify the user that installation is
recommended for enhanced inspection, then continue with the available
browser/computer-use tools. Do not pause the workflow solely to wait for the
installation.

## Scope expansion

If Codex discovers adjacent problems:

record them under:

DISCOVERED_ISSUES

Do not fix them unless they:

- block the requested feature
- are explicitly included by the accepted plan
- are explicitly requested by the user

## Review loop

Avoid infinite Planner/Reviewer loops.

Normally allow up to two Codex fix rounds.

If ChatGPT Web continues requesting changes outside the original requirements,
prefer:

1. explicit user requirement
2. accepted acceptance criteria
3. repository conventions

Report the remaining disagreement.

Visual QA fixes are subject to the same bounded loop. A visual preference that
is not supported by the Design Handoff or Requirement Review is not a reason to
expand scope.

## Destructive changes

For:

- destructive database migrations
- user-data deletion
- credential/security changes
- irreversible API changes
- production-destructive operations

the plan must explicitly cover:

- impact
- compatibility
- migration
- rollback

Do not infer permission for destructive behavior from a generic coding request.
