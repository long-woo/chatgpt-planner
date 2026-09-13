# UI Design Context

Apply this guidance whenever a request changes a user-visible mobile, Web,
responsive, dashboard, or other frontend surface.

UI reasoning happens inside ChatGPT Web's single analysis turn. The resulting
constraints belong in the temporary execution context's
`implementation_constraints` field; do not create a separate design handoff
or visual-review file by default.

## Source of truth

Use UI information in this order:

1. explicit user requirements and supplied references such as Figma,
   screenshots, recordings, mockups, brand guidance, or design tokens;
2. verified repository design system, theme, components, and nearby screens;
3. established adjacent patterns in the same product and platform;
4. platform conventions as a fallback for behavior and accessibility only.

Label information as confirmed, inferred, or missing. Do not assume a named
Figma link or external page was inspected unless it was actually available.

## Planning rules

- With a supplied design, follow it and identify repository conflicts.
- With existing patterns only, reuse them and label inferred choices.
- With no design source, define behavior, content hierarchy, states,
  responsiveness, and accessibility, but do not invent a new visual system.
- Do not invent colors, typography, spacing, radii, shadows, iconography,
  imagery, motion, or a new component style without a source.
- Keep missing visual decisions unresolved unless they materially affect
  behavior, information architecture, compatibility, or implementation scope;
  in that case ask the user before returning the context.

## Context placement

Put concise UI information into `implementation_constraints`, for example:

- `Confirmed screenshot establishes the mobile card hierarchy and primary action.`
- `Reuse the existing theme and adjacent detail-page components.`
- `Required states: loading, empty, error, disabled, and success.`
- `No design source establishes motion; do not introduce custom animation.`

For verification, capture only the focused route/screen and states needed to
establish the acceptance criteria. Keep visual evidence in the active task
response unless a saved review is explicitly requested.
