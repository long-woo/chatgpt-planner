# Planner Protocol

ChatGPT Web is the Product & Engineering Planner. It determines what should
be implemented but must not write production code.

## One planning turn

The current client sends:

- `USER_REQUEST`;
- concise `PROJECT_CONTEXT`;
- verified `REPO_CONTEXT`;
- relevant `CONSTRAINTS`;
- the selected `workflow_mode` as routing metadata.

ChatGPT Web performs the needed analysis internally and returns one execution
context. Do not split the turn into Requirement Review, Product Design,
Design Handoff, Engineering Analysis, Task Planning, or Final Review outputs.
Do not save intermediate reasoning.

## Required response

Return exactly one JSON object with only these top-level keys:

```json
{
  "goal": "The intended outcome.",
  "scope": ["Included behavior or bounded implementation area."],
  "non_goals": ["Explicitly excluded work."],
  "implementation_constraints": ["Confirmed product, design, repository, or technical constraint."],
  "acceptance_criteria": ["Observable completion condition."],
  "verification": [
    {"check": "Concrete command or manual check.", "required": true}
  ]
}
```

See `execution-context.md` for validation rules.

## Planning behavior

- Preserve the user's actual outcome and do not restate it in multiple forms.
- Separate observable behavior from implementation details.
- Use only verified repository facts supplied by Codex.
- Include explicit non-goals and the smallest coherent scope.
- Define objective acceptance criteria and practical verification.
- For UI work, run the Automatic UI Design subroutine from
  `references/auto-ui-design.md`. Distinguish confirmed design input from
  existing patterns and generated design intent, and place the concise result
  in `implementation_constraints`.
- Do not add optional improvements, broad refactors, or design outside the
  selected UI design mode.
- For `AUTO_DESIGN`, generate a restrained, reversible visual direction from
  the user request, verified repository patterns, and platform conventions;
  ChatGPT Web must make these design decisions itself and return
  implementation-ready design intent. Do not delegate UI design to Codex or
  leave a new UI surface without a bounded direction.
- Resolve material blocking questions with the user before producing the
  context.
- For Standard and Comprehensive work, do the fuller analysis internally but
  keep the response to the six-field context.

Rules:

- Do not write production code.
- Do not return prose before or after the JSON.
- Do not include `workflow_mode`, tasks, requirements, assumptions, risks,
  file lists, review status, or artifact paths as extra fields.

## Plan correction

If Codex finds a repository conflict, send the original request, the relevant
repository fact, the conflict, and the recommended correction. Ask ChatGPT
Web to return a complete replacement execution context, not a patch. Keep the
replacement in memory and discard the superseded context for implementation.
