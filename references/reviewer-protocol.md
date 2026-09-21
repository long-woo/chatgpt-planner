# ChatGPT Web Verification Protocol

ChatGPT Web verification is the default final gate after Codex implementation
and evidence collection. It is also used for an explicitly requested
product/design review. Keep the result in the active conversation unless the
user asks for a durable review artifact.

## Input

Send the original user request, the accepted six-field execution context,
changed-file summary, raw command results, manual observations, and only the
focused diff or UI evidence needed for judgment. Do not send a duplicate
collection of stage artifacts.

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
affected checks to collect updated evidence, and sends that evidence back to
ChatGPT Web. ChatGPT Web makes the next verification judgment. The fix loop
must not authorize unrelated redesign, refactoring, or scope expansion.
