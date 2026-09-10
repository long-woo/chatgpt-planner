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

Treat a response-format failure separately from a missing plan. Do not block
implementation solely because an otherwise available plan was not serialized
as valid JSON.

First retain the raw response in the Planner conversation and attempt
lossless normalization: remove a surrounding Markdown code fence or isolate
the one complete JSON object when surrounding prose is present. Accept it only
if the resulting object parses without repair and validates against the
applicable contract schema. Never add quotes, commas, fields, requirements, or
acceptance criteria while normalizing.

If normalization does not yield a usable contract, make one bounded recovery
request. Together with the malformed original response, this is the maximum of
two automatic Planner attempts for the same contract:

1. In the same Planner conversation, state the exact parse or schema failure,
   resend the expected schema, and request the complete contract as JSON only.

If the original conversation is unavailable, send that one repair request in a
fresh normal Chat conversation. It must be self-contained: include the
approved upstream artifacts, verified repository facts, the schema, and the
original request; do not rely on chat history.

Do not immediately regenerate the plan yourself.

If the repair response remains malformed, record the two raw-response
summaries, parse/validation failures, and recovery attempt in
`workflow-state.json` history. Then apply the appropriate deterministic
contract recovery below.

### Comprehensive deterministic Task Contract recovery

When Requirement Review, applicable Design and Design Handoff, and Engineering
Analysis are all approved and together contain enough unambiguous information,
Codex may write `.chatgpt/task-contract.json` itself and approve
`TASK_PLANNING`. This exception repairs a transport failure; it does not give
Codex product-design authority.

The recovered contract must:

- validate against `task-contract.md`;
- use only the approved user scope, requirement/design artifacts, verified
  repository facts, and available verification commands;
- preserve explicit non-goals and unresolved decisions;
- make each requirement, task, acceptance criterion, and risk traceable to an
  approved artifact or repository fact;
- omit ambiguous optional work rather than choosing it; and
- be identified in the state notes and history as
  `CODEX_RECOVERY_FROM_APPROVED_ARTIFACTS`, including the source artifacts and
  the two failed attempts.

If those artifacts do not establish observable requirements, a safe behavior,
or a bounded implementation shape, do not synthesize a contract. Re-enter or
block the earliest responsible upstream stage for the missing substantive
decision. JSON formatting alone is never that substantive decision.

### Lite deterministic Change Contract recovery

After the same normalization and two failed total Planner attempts, Codex may
write a Change Contract when the approved `TRIAGE`, explicit user request, and
verified repository facts fully establish a localized, reversible change with
objective verification. Record `CODEX_RECOVERY_FROM_APPROVED_ARTIFACTS` and
the failed attempts in the Lite state. Do not escalate to the comprehensive
workflow solely because JSON serialization failed.

If the Lite inputs are not sufficient for a bounded contract, escalate because
of the substantive scope or decision gap, not because the Planner emitted
malformed JSON.

For any recovery path:

- preserve only unambiguous source information
- record missing or ambiguous required input in state and return to the
  responsible upstream stage when it prevents a complete contract
- do not fabricate requirements or acceptance criteria
- do not implement from malformed fragments; implement only after a complete,
  schema-valid Planner or recovered contract is persisted and approved

## Lite scope expansion

When a Lite change crosses an eligibility boundary or an `escalate_if`
condition:

1. stop implementation before making broader changes;
2. copy `.chatgpt/workflow-state.json` to
   `.chatgpt/lite-workflow-state.json`, and preserve the Change Contract,
   Change Result, and existing code changes;
3. record the discovered impact and reason for escalation;
4. initialize the comprehensive state while retaining a history reference to
   the Lite artifacts;
5. resume at the earliest stage responsible for the newly discovered decision.

Do not discard existing user changes or silently continue under the Lite
contract.

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
