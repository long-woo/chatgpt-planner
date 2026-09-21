# ChatGPT Planner

<img src="./assets/logo.png" width="160" center />

Use ChatGPT Web as the planner and Codex as the implementation engineer. The
skill preserves their responsibility boundary while keeping the normal
handoff small and temporary.

## Default flow

```text
User Request
  → ChatGPT Web analysis
  → one temporary execution context
  → Codex implementation
  → Codex evidence collection
  → ChatGPT Web verification
  → Done or bounded fix loop
```

ChatGPT Web performs the requirement, product/UI, and planning analysis in one
turn. For new UI surfaces, that turn includes Automatic UI Design and returns
the design direction inside `implementation_constraints`. The current client
transfers one structured context to Codex in memory; it does not save it to the
project. ChatGPT Web owns the UI design; the current client only routes the
design mode, and Codex implements the returned direction.

The context contains only:

```text
goal
scope
non_goals
implementation_constraints
acceptance_criteria
verification
```

No new task should automatically create `.md` or `.json` process files. This
includes `requirement-review.md`, `product-design.md`, `design-handoff.md`,
`engineering-analysis.md`, `task-contract.json`, `final-review.md`,
`visual-review.md`, change results, or workflow state.

## Workflow modes

`workflow_mode` remains a routing concept. It changes analysis depth, not the
handoff format or artifact policy.

### LITE

For small, local, reversible, objectively verifiable changes with no material
product/UI, public API, persistence, migration, authentication, security,
privacy, billing, payment, or destructive decision.

```text
Compact analysis → context → implementation → evidence
→ ChatGPT Web verification
```

### STANDARD

For bounded pages, endpoints, medium features, data-model adjustments, or
user-visible flows that need more complete requirement/design reasoning.

```text
Complete analysis → context → implementation → evidence
→ ChatGPT Web verification
```

### COMPREHENSIVE

For architecture, core journeys, security/privacy/authorization, irreversible
data behavior, or major release capabilities.

```text
Full analysis → context → implementation → evidence
→ ChatGPT Web verification
```

Comprehensive may internally cover product design, automatic UI design, UI
states, engineering impact, risks, and rollout considerations, but still
returns one context and does not create intermediate documents by default.

See `references/workflow-selection.md` for routing and escalation rules.

## Roles

ChatGPT Web owns:

- requirement interpretation and product intent;
- UI/design decisions when the request needs them;
- scope, non-goals, acceptance criteria, and verification expectations;
- final verification judgment and PASS/NEEDS_FIX/BLOCKED decision.

Codex owns:

- repository reconnaissance and verified repository facts;
- implementation choices that fit the repository;
- code changes;
- raw test/build output and focused UI evidence for ChatGPT Web to evaluate.

The central rule is:

> ChatGPT Web decides what should be built. Codex decides how it fits the real
> repository and implements it.

ChatGPT Web must not write production code. Codex must not silently redefine
product requirements.

## Usage

Describe the development request normally:

```text
Use chatgpt-planner to implement sharing for today's recommended place.
```

Or:

```text
Use chatgpt-planner to fix this bug:

When the daily recommendation limit has been reached, reopening the app after
some time automatically fetches another recommendation.
```

Codex inspects only the relevant repository context, sends a concise standalone
planning request to ChatGPT Web, validates the returned context, then
implements and collects evidence. ChatGPT Web performs the final verification.

## Planner response contract

ChatGPT Web must return one JSON object with exactly these top-level keys:

```json
{
  "goal": "The intended outcome.",
  "scope": ["Included work."],
  "non_goals": ["Excluded work."],
  "implementation_constraints": ["Confirmed constraints and design boundaries."],
  "acceptance_criteria": ["Observable completion condition."],
  "verification": [
    {"check": "Concrete command or manual check.", "required": true}
  ]
}
```

The schema and validation rules are in `references/execution-context.md`.
Do not add tasks, requirements, assumptions, risks, repository facts,
workflow metadata, review status, or artifact paths as extra fields.

If a product-defining decision is missing, resolve it with the user before
returning the context. If Codex later finds a repository conflict, request one
complete replacement context; do not silently reinterpret it.

## Low-token usage

For the lowest-token path:

1. Keep `LITE` for clear, local changes.
2. Send one concise `USER_REQUEST`, `PROJECT_CONTEXT`, `REPO_CONTEXT`, and
   `CONSTRAINTS` bundle.
3. Ask for the six-field JSON context only, with no explanation or preamble.
4. Do not paste the full repository or repeat the same requirement in multiple
   messages.
5. Let Codex collect only relevant raw evidence; let ChatGPT Web make the
   verification judgment.

`STANDARD` and `COMPREHENSIVE` still use one ChatGPT Web response; they spend
more reasoning on ambiguity and risk, not on generating more files.

## Verification

Verification is the default completion gate for every mode. Codex runs the
checks in the context and proportionate local checks only to collect evidence;
ChatGPT Web then judges:

- changed files;
- acceptance-criterion evidence;
- command results and relevant manual/UI evidence;
- plan deviations, verification gaps, and unrelated `DISCOVERED_ISSUES`.

Do not report unavailable checks as passed. A default task does not create a
Change Result, Visual Review, or Final Review file. The ChatGPT Web verification
remains in chat by default.

## When to save documents

The default flow does not save product documentation. Save a document only
when the user explicitly asks for a durable plan, design, review, decision
record, or exported context.

When asked, create only the requested document, keep it concise, and use the
temporary context as its source of truth. Do not regenerate the old artifact
bundle for completeness. See `references/optional-artifacts.md`.

Existing `.chatgpt/` files and root-level planning files remain readable as
legacy context when needed. New work does not require, delete, or overwrite
them. The old stage references and process templates have been removed from
the skill; only the optional decision-record template remains.

## UI guidance

For UI changes, read `references/ui-design-context.md` and
`references/auto-ui-design.md`. Reuse existing patterns for local corrections;
adapt supplied references when available; otherwise automatically generate a
bounded visual direction for new pages, flows, and redesigns. Put the result in
`implementation_constraints` and keep visual evidence temporary.

## Browser and security

Use a normal ChatGPT Web Chat mode conversation, never Work mode. The built-in
browser is preferred, with the system default browser as fallback. Do not
upload credentials, secrets, or an entire repository merely to transfer
context.

## Reference map

- `SKILL.md` — activation, roles, default workflow, and completion rules.
- `references/execution-context.md` — temporary six-field handoff.
- `references/planner-protocol.md` — one-turn ChatGPT Web behavior.
- `references/workflow-selection.md` — mode selection and escalation.
- `references/lite-workflow.md` / `standard-workflow.md` — mode-specific depth.
- `references/browser-workflow.md` — browser interaction and transfer.
- `references/failure-handling.md` — malformed context and recovery.
- `references/reviewer-protocol.md` — optional post-implementation review.
- `references/optional-artifacts.md` — explicit durable output only.
- `references/ui-design-context.md` — UI source and constraint rules.
- `references/decision-records.md` — optional durable decisions.

Old project-level stage files may still be interpreted as legacy context when
resuming an existing workflow, but they are no longer active skill flows.
