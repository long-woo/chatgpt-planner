# Task Contract

ChatGPT Web must return one JSON object.

## Schema

{
  "plan_version": "1",

  "summary": "Description of the intended outcome.",

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

For a mobile App or Web UI request, include `ui_context` immediately after
`summary`. Follow `references/ui-design-context.md` for its values and omit
the field when the request has no UI impact.

Every requirement must be observable or meaningful to the system.

## Tasks

Every task must:

- have a bounded goal
- reference at least one requirement
- have acceptance criteria
- declare dependencies
- avoid unrelated work

Tasks should be split by coherent behavior rather than mechanically by files.

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
