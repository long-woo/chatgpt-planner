# Engineering Analysis

Engineering Analysis is the repository-impact assessment before ChatGPT Web
generates the Task Contract. Codex owns this analysis because it is based on
verified repository facts. It is analysis only: do not begin implementation in
this stage. It is the approval gate immediately before Task Planning.

## Inputs

Read:

- `.chatgpt/requirement-review.md`
- `.chatgpt/design-handoff.md`
- `REPO_CONTEXT`
- the current repository state and relevant local conventions

Do not depend on Git metadata or a Git repository being present. The shared
working directory and files that are actually available are the source of
truth.

## Output

Write `.chatgpt/engineering-analysis.md` in the shared working directory from
`templates/engineering-analysis.md`. Use this structure:

```markdown
# Engineering Analysis

status: NOT_STARTED | IN_PROGRESS | WAITING_REVIEW | APPROVED | BLOCKED | COMPLETED
stage: ENGINEERING_ANALYSIS

## Current code impact
- Verified current code paths and expected impact.

## Impacted modules
- Module or subsystem — verified current role and expected impact.

## Involved files
- Verified file or directory — why it is relevant.

## Technical risks
- Risk — likelihood/impact and a bounded mitigation.

## Data impact
- Schema, persistence, migration, compatibility, or `None`.

## Implementation recommendation
- Smallest coherent implementation approach.
- Explicit files/components/services only when supported by repository facts.

## Implementation suggestions
- Boundary-safe suggestions for Task Planning and Codex.

## Change boundary
- Included technical changes.
- Areas that must remain untouched.

## Verification strategy
- Tests, checks, manual flows, and UI evidence needed.

## Repository conflicts or open decisions
- `None`, or concrete facts that require Planner resolution.
```

The analysis must call out affected modules, data consequences, risks, and the
smallest viable implementation shape. It must not broaden the product scope
or turn speculative cleanup into work.

## Gate before Task Contract

ChatGPT Web receives the artifact contents or a concise faithful extract and
uses it to produce the Task Contract. If the analysis conflicts with an
accepted product/design decision, Codex sends a `PLAN_CORRECTION`; repository
facts remain authoritative for current architecture, while ChatGPT Web remains
authoritative for product intent.

Existing root-level `engineering-analysis.md` may be read as a legacy input
when the canonical file is absent; new runs write the canonical path.
