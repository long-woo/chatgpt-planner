# Optional Reviewer Protocol

ChatGPT Web review is not a default stage in the new workflow. Use this
protocol only when the user explicitly requests a product/design review or
when the current task is separately authorized to require one.

## Input

Send the original user request, the accepted six-field execution context,
changed-file summary, verification results, and only the focused diff or UI
evidence needed for judgment. Do not send a duplicate collection of stage
artifacts.

## Review criteria

Check:

- every acceptance criterion;
- scope and non-goals;
- implementation constraints and required UI states;
- regressions indicated by verification evidence;
- concrete visual deviations when UI evidence is supplied.

Return one of:

- `PASS` — acceptance criteria are established;
- `NEEDS_FIX` — a concrete bounded requirement failure exists;
- `BLOCKED` — evidence is insufficient for a decision.

Return the result in chat. Do not write `final-review.md`, `visual-review.md`,
or another review artifact unless the user explicitly asks to save it.

## Fix loop

For `NEEDS_FIX`, Codex fixes only the identified bounded issue, reruns the
affected verification, and sends the updated evidence. A review must not
authorize unrelated redesign, refactoring, or scope expansion.
