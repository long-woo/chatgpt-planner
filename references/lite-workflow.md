# Lite Workflow

Use the `LITE` profile for small, low-risk, well-bounded changes. Its purpose is
to preserve scope control and independent planning without forcing every change
through the comprehensive eleven-stage workflow.

## Eligibility

Use `LITE` only when all of the following are true:

- the requested behavior is clear and does not require a material product
  decision;
- the change is localized to one module and is expected to touch no more than
  roughly three production files; this is a heuristic, not a hard limit;
- no persistence schema, public API contract, authentication, authorization,
  privacy, billing, payment, destructive operation, or irreversible migration
  is involved;
- the change does not introduce a new visual direction, page flow, information
  architecture, or interaction model;
- an objective targeted test or deterministic manual check can establish the
  requested behavior;
- the change is straightforward to revert.

A UI defect may use `LITE` only when the expected appearance or interaction is
already established by an inspected design source or existing repository
pattern and focused visual evidence can verify the correction.

If any criterion is false or uncertain, use the comprehensive workflow. Do not
choose `LITE` merely because the expected diff is short.

## Flow

```text
TRIAGE
  -> COMPACT_PLAN
  -> IMPLEMENTATION
  -> VERIFICATION
  -> FINAL_REVIEW (conditional)
  -> DONE
```

Initialize `.chatgpt/workflow-state.json` from
`templates/lite-workflow-state.json`. The Lite artifacts are:

- `.chatgpt/change-contract.json`
- `.chatgpt/change-result.json`

The state file is the source of truth for the current Lite stage. The Change
Contract is the scope boundary; the Change Result is the implementation and
verification record.

## Triage

Codex inspects only enough repository context to validate the eligibility
criteria and identify a targeted verification method. Record the decision and
evidence in the `TRIAGE` state notes. Triage is not implementation.

If `.chatgpt/decision-records.md` exists, read relevant `ACTIVE` records during
triage. A conflict with an active record is not Lite-eligible unless the user
has already resolved it and no downstream product, design, or technical
decision remains.

When eligible, move `COMPACT_PLAN` to `IN_PROGRESS`. When ineligible, initialize
the comprehensive workflow instead. If a Lite workflow already exists, follow
the escalation procedure below.

## Compact Plan

Use one ChatGPT Web Planner interaction. Send:

- `USER_REQUEST`
- concise verified `REPO_CONTEXT`
- the Lite eligibility constraints
- the Change Contract schema from `templates/change-contract.json`

Ask ChatGPT Web to return exactly one JSON object. Codex persists it as
`.chatgpt/change-contract.json` and validates that it:

- preserves the explicit user request;
- has at least one observable requirement and acceptance criterion;
- has a bounded scope and explicit non-goals;
- uses only verified repository facts;
- defines practical verification;
- contains escalation conditions appropriate to the change.

If repository facts contradict the response, use the existing
`PLAN_CORRECTION` procedure. Do not split the compact plan into separate
Requirement Review, Product Design, Design Handoff, Engineering Analysis, and
Task Planning artifacts.

## Implementation

Before editing production code, verify that:

- `workflow_profile` is `LITE`;
- `COMPACT_PLAN` is `APPROVED`;
- `IMPLEMENTATION` is `IN_PROGRESS`;
- `.chatgpt/change-contract.json` is present and usable.

Implement only the contract scope. Do not perform adjacent cleanup. If the
work crosses an escalation condition, stop the Lite implementation and
escalate before continuing.

## Verification and result

Write `.chatgpt/change-result.json` from `templates/change-result.json`.
Record:

- changed files;
- one result for every acceptance criterion;
- commands with status and exit code;
- deterministic manual or visual checks when applicable;
- plan deviations;
- discovered issues;
- whether ChatGPT Web final review is required and why.

Use `passed`, `failed`, or `not_run` for individual checks. The overall result
may be `passed` only when every required acceptance criterion is established,
required checks did not fail or remain `not_run`, and there is no unresolved
plan deviation.

## Conditional final review

Skip the second ChatGPT Web interaction only when all of the following are
true:

- every acceptance criterion passed;
- the implementation stayed within the Change Contract;
- no required verification is missing;
- no user-visible product or unresolved visual decision was introduced;
- Codex has no material uncertainty about correctness.

When skipped, set `FINAL_REVIEW` to `SKIPPED` and record the reason. Require
ChatGPT Web final review when any condition is not met, when the user requests
it, or when the change needs subjective visual judgment. A review `PASS` maps
to `APPROVED`; `NEEDS_FIX` or `BLOCKED` follows the bounded review rules from
`failure-handling.md`.

For a required review, send the original user request, Change Contract, Change
Result, and only the diff excerpts or visual evidence needed to judge the
acceptance criteria. Ask ChatGPT Web to return `PASS`, `NEEDS_FIX`, or
`BLOCKED` with bounded required fixes. Persist the status, summary, and fixes
in the review fields of `.chatgpt/change-result.json`.

`DONE` may be `COMPLETED` only when Verification is `APPROVED` and Final Review
is either `APPROVED` or validly `SKIPPED`. Set `workflow_status` to
`COMPLETED` at the same transition.

## Escalation

Escalate from `LITE` to the comprehensive workflow when implementation or
verification discovers any of the following:

- material product ambiguity;
- a new UI direction or flow decision;
- public API, persistence, migration, compatibility, authentication,
  authorization, privacy, security, billing, payment, or destructive impact;
- impact outside the approved Change Contract;
- verification that cannot establish correctness locally;
- a broader architectural decision.

Do not continue editing while the profile is being escalated. Copy the current
Lite state to `.chatgpt/lite-workflow-state.json`, preserve the Change Contract,
Change Result, and existing code changes, then initialize the comprehensive
state at `.chatgpt/workflow-state.json`. Its first history entry must reference
the Lite state and artifacts and explain the escalation. Start at the earliest
responsible stage. Normally this is Requirement Review; use Engineering
Analysis only when product and design intent remain fully settled and the
newly discovered issue is exclusively technical.

Never downgrade an active comprehensive workflow to `LITE` merely to bypass a
gate.
