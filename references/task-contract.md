# Task Contract

ChatGPT Web must return one JSON object.

Normally ChatGPT Web creates the contract. If its JSON output remains invalid
after the bounded recovery procedure, Codex may create a deterministic
recovered contract only as specified in `failure-handling.md`. The state file,
not the JSON schema, records whether the contract came from ChatGPT Web or
`CODEX_RECOVERY_FROM_APPROVED_ARTIFACTS` and identifies its approved sources.

## Schema

{
  "plan_version": "1",

  "summary": "Description of the intended outcome.",

  "workflow_artifacts": {
    "requirement_review": ".chatgpt/requirement-review.md",
    "design_handoff": ".chatgpt/design-handoff.md",
    "engineering_analysis": ".chatgpt/engineering-analysis.md",
    "visual_review": ".chatgpt/visual-review.md"
  },

  "ui_context": {
    "surface": "mobile_app | web | both | unknown",
    "source_status": "provided_design | existing_patterns_only | no_design_source",
    "sources": [
      {
        "type": "figma | screenshot | existing_ui | design_system | user_requirement | platform_convention",
        "status": "confirmed | inferred | missing",
        "description": "What this source establishes or fails to establish"
      }
    ],
    "unresolved_visual_decisions": []
  },

  "assumptions": [
    "Explicit assumption"
  ],

  "non_goals": [
    "Explicitly excluded work"
  ],

  "requirements": [
    {
      "id": "REQ-1",
      "description": "Observable user-visible or system requirement"
    }
  ],

  "tasks": [
    {
      "id": "TASK-1",
      "title": "Short task title",

      "goal": "What this task accomplishes",

      "requirement_ids": [
        "REQ-1"
      ],

      "scope": [
        "Included work"
      ],

      "implementation_guidance": [
        "Non-binding implementation guidance"
      ],

      "acceptance_criteria": [
        "Observable completion condition"
      ],

      "dependencies": []
    }
  ],

  "verification": [
    {
      "type": "test",
      "command_or_check": "Concrete command or validation step",
      "required": true
    }
  ],

  "risks": [
    {
      "risk": "Potential problem",
      "mitigation": "Mitigation"
    }
  ],

  "blocking_questions": []
}

## Requirements

Requirements describe behavior, not source-file changes.

The Task Contract must preserve the scope in `.chatgpt/requirement-review.md`, use
the constraints in `.chatgpt/design-handoff.md` for UI work, and reflect the
bounded impact in `.chatgpt/engineering-analysis.md`. Include `workflow_artifacts` immediately after
`summary` with the shared-directory filenames. When UI work also includes
`ui_context`, place it immediately after `workflow_artifacts`. `visual_review`
is a forward reference for the verification stage and may contain a
`NOT_APPLICABLE` record for non-UI work.

For a mobile App or Web UI request, include `ui_context` after
`workflow_artifacts`. Follow `references/ui-design-context.md` for its values
and omit the field when the request has no UI impact.

Every requirement must be observable or meaningful to the system.

## Tasks

Every task must:

- have a bounded goal
- reference at least one requirement
- have acceptance criteria
- declare dependencies
- avoid unrelated work

Tasks should be split by coherent behavior rather than mechanically by files.
Do not create tasks for non-goals or for speculative engineering cleanup.

## Acceptance criteria

Acceptance criteria must be objectively checkable.

Bad:

"The implementation is good."

Bad:

"Use clean code."

Good:

"Triggering the share action opens the platform share sheet."

Good:

"When no cover image exists, sharing still succeeds."

## Dependencies

Use task IDs:

{
  "dependencies": [
    "TASK-1"
  ]
}

Do not invent unnecessary sequencing.

## Blocking questions

`blocking_questions` should normally be empty.

A blocking question is justified only if Codex cannot safely proceed without a
material product or architecture decision.

## Recovered contracts

A recovered contract has the same schema and quality bar as a Planner contract.
It may only restate approved requirement/design decisions and verified
repository facts as bounded tasks and checks. It must not resolve an ambiguous
product choice, add convenience work, or turn malformed response text into new
requirements. When that information is insufficient, return to the earliest
upstream stage that owns the missing decision instead of blocking on JSON
formatting.
