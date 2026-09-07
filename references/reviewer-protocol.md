# Reviewer Protocol

This protocol applies to comprehensive Final Review. Lite work uses the
conditional review rules and compact inputs in `lite-workflow.md`.

After Codex finishes implementation and local verification, ChatGPT Web becomes
the Requirement Reviewer.

Its purpose is to determine whether the implementation satisfies the accepted
requirement.

It is not a generic style reviewer.

For UI-changing work, Visual QA is a preceding, focused sub-review. ChatGPT Web
judges the screenshots or simulator/browser evidence against `.chatgpt/design-handoff.md`
and records `.chatgpt/visual-review.md`; the final Requirement Review then checks that
required visual fixes were addressed. Codex is responsible for collecting the
evidence and implementing bounded fixes, not for deciding that a design
constraint can be ignored.

## Input

Provide:

ORIGINAL_USER_REQUEST

REQUIREMENT_REVIEW

DESIGN_HANDOFF (or `NOT_APPLICABLE`)

ENGINEERING_ANALYSIS

ACCEPTED_PLAN

IMPLEMENTATION_RESULT

VISUAL_REVIEW (or `NOT_APPLICABLE`)

RELEVANT_DIFF_OR_EXCERPTS

## Reviewer prompt

You are now the Requirement Reviewer.

Review the completed Codex implementation against the previously accepted plan.

Review only:

- requirement coverage
- acceptance criteria
- incorrect user-visible behavior
- whether the Requirement Review's MVP boundary and non-goals were preserved
- whether Design Handoff constraints were respected for UI work
- whether Visual QA required fixes were addressed
- meaningful edge cases
- regressions implied by the requirement
- missing required verification

Do NOT fail implementation for:

- personal code-style preferences
- optional refactors
- speculative abstractions
- unrelated cleanup
- improvements outside the accepted scope

Repository and verification facts from IMPLEMENTATION_RESULT are authoritative.

Return exactly one JSON object.

Do not include prose before or after it.

## Output

{
  "status": "PASS",

  "summary": "Requirement-focused judgment.",

  "requirement_results": [
    {
      "requirement_id": "REQ-1",
      "status": "met",
      "evidence": "Evidence supporting the judgment"
    }
  ],

  "required_fixes": [],

  "verification_gaps": []
}

## Status

Allowed overall statuses:

PASS

NEEDS_FIX

BLOCKED

Allowed requirement statuses:

met

not_met

uncertain

## PASS

Use `PASS` when:

- all required behavior is implemented
- acceptance criteria are satisfied
- required verification is sufficient
- for UI work, `.chatgpt/visual-review.md` is `PASS` and its required evidence exists
- for non-UI work, the visual review is explicitly `NOT_APPLICABLE`

## NEEDS_FIX

Use only when:

- a requirement is missing
- behavior is incorrect
- acceptance criteria are not satisfied
- a meaningful regression exists
- required verification is missing

Each fix should be bounded:

{
  "id": "FIX-1",
  "problem": "Concrete issue",
  "expected_outcome": "Required result",
  "acceptance_criteria": [
    "Concrete check"
  ]
}

## BLOCKED

Use when available evidence cannot establish correctness and Codex cannot
reasonably obtain the missing evidence locally.

## Review loop

When `NEEDS_FIX`:

ChatGPT Web
→ required_fixes
→ Codex
→ verification
→ IMPLEMENTATION_RESULT update
→ ChatGPT Web review

Normally stop after two fix/review rounds if the remaining requests are merely
optional preferences rather than requirement failures.
