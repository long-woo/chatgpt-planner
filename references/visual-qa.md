# Visual QA

Visual QA is part of implementation verification for UI-changing work. It
checks the rendered result against the Design Handoff and Requirement Review;
it is not a generic aesthetic review. For non-UI work, write a concise
`NOT_APPLICABLE` record so the workflow remains traceable.

Codex owns producing the implementation and collecting evidence. ChatGPT Web
owns the product/design judgment and decides whether a visual deviation is a
required fix.

## Evidence

Use the most appropriate available evidence:

- browser screenshots and interaction states for Web work
- simulator/device screenshots for mobile work
- focused recordings or rendered previews when a screenshot cannot establish
  the behavior

For Web verification, prefer `chrome-devtools-mcp` when installed and usable.
If it is not installed, explicitly prompt the user to install it for the
enhanced Web inspection workflow, then continue with the available
browser/computer-use fallback. Do not install it silently or make the prompt a
workflow blocker.
Record the missing tool as a verification gap only for checks that the
fallback cannot establish.

For App verification, use simulator/device screenshots and validate the
relevant page states, including loading, empty, error, disabled, and success
states when applicable.

Record the route/screen, viewport or device, state, and how the evidence was
obtained. Follow `browser-workflow.md` for browser tooling and fallback rules.

## Output

Write `.chatgpt/visual-review.md` in the shared working directory from
`templates/visual-review.md`. Use this structure:

```markdown
# Visual Review

status: PASS | NEEDS_FIX | BLOCKED | NOT_APPLICABLE
stage: VISUAL_QA

## Compared against
- Design goal and key experience from `.chatgpt/design-handoff.md`.
- Relevant requirement IDs and states from `.chatgpt/requirement-review.md`.

## Evidence
- `path-or-reference` — surface, viewport/device, state, and observed result.

## Visual deviations
- VQA-1: Expected constraint; observed deviation; severity and evidence.

## Required fixes
- FIX-1: Bounded correction and objective acceptance check.

## Verification gaps
- Checks that could not be performed and why, or `None`.
```

`PASS` requires that required visual constraints and key states are evidenced.
`NEEDS_FIX` is reserved for a concrete requirement/design deviation, not a
personal preference. `BLOCKED` means evidence cannot reasonably be obtained.

## Gate

Codex must address bounded `Required fixes`, rerun affected checks, and update
the same artifact before final Requirement Review. ChatGPT Web's Visual QA
judgment does not authorize unrelated redesign or scope expansion.

Existing root-level `visual-review.md` may be read as a legacy input when the
canonical file is absent; new runs write the canonical path.
