# UI Design Context

Apply this guidance whenever a request changes a user-visible surface in a
mobile App, Web app, responsive site, dashboard, or other frontend product.
It does not apply to backend-only work with no user-visible UI impact.

The Planner is responsible for clarifying UI behavior and design inputs, not
for inventing a visual direction that the user did not provide.

## Source of truth

Use UI information in this order of authority:

1. Explicit user requirements and supplied UI references: Figma files or
   links, screenshots, recordings, annotated mockups, brand guidelines,
   design tokens, component specifications, or concrete visual constraints.
2. Verified project context: the repository's design system, component
   library, theme/tokens, existing screens, and established patterns on the
   same product surface.
3. Existing adjacent UI patterns that are demonstrably part of the same
   product and platform.
4. Platform conventions, but only as a fallback for interaction behavior,
   accessibility, and basic layout expectations—not as permission to create a
   new visual style.

Treat a source as available only when its contents are actually provided or
can be inspected. Do not assume that a Figma link, screenshot, design system,
or external page is accessible merely because it is mentioned.

For each relevant source, distinguish:

- **Confirmed**: directly supplied, inspected, or established in the
  repository.
- **Inferred**: a conclusion from existing product patterns; label it as an
  inference.
- **Missing**: needed UI information that is not available; keep the decision
  unresolved rather than filling it with taste or guesswork.

## Planning rules for UI work

Before creating tasks, determine which of these situations applies:

- **Provided design**: follow the supplied design reference and identify any
  conflicts with the repository or platform constraints.
- **Existing patterns only**: reuse the existing design system and nearby
  screens; call out any inferred choices that are not explicitly specified.
- **No design source**: define the user-visible behavior, content hierarchy,
  states, responsive or platform constraints, and accessibility expectations,
  but leave the visual direction explicitly unresolved.

When no Figma or UI design is available, do not invent or silently decide:

- color palettes, typography, brand treatment, spacing scales, corner radii,
  shadows, iconography, illustrations, imagery, or motion language;
- a new component style or visual hierarchy that is not supported by existing
  product patterns;
- polished mockups, visual variants, or aesthetic recommendations presented
  as requirements.

Instead, the plan should:

- reuse existing components, tokens, and patterns when they exist;
- specify behavior and structure in observable terms, including loading,
  empty, error, disabled, responsive, and accessibility states when relevant;
- record the missing visual decisions as an assumption, non-goal, or
  unresolved design input;
- add a blocking question only when the missing design decision materially
  changes user-visible behavior, information architecture, compatibility, or
  implementation scope. Otherwise, proceed with existing patterns and keep
  the visual decision open for design review.

Do not convert “make it look good,” “follow best practices,” or a similar
high-level request into an invented visual system. Interpret it as a request
to preserve or reuse the project's established UI language unless the user
provides a design source or explicitly asks for visual exploration.

## Required plan traceability

For a UI-changing request, include a `ui_context` object in the Planner JSON:

```json
{
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
    "unresolved_visual_decisions": [
      "Visual decisions that must not be invented by the implementation"
    ]
  }
}
```

Keep this object concise. If the request has no UI impact, omit it. The
`ui_context` object does not replace requirements, tasks, acceptance criteria,
or blocking questions; it records the evidence and boundary behind them.
