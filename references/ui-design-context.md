# UI Design Context

Apply this guidance whenever a request changes a user-visible mobile, Web,
responsive, dashboard, or other frontend surface.

UI reasoning happens inside ChatGPT Web's single analysis turn. The resulting
constraints belong in the temporary execution context's
`implementation_constraints` field; do not create a separate design handoff
or visual-review file by default. For a new surface or flow without a complete
reference, use the Automatic UI Design subroutine in
`references/auto-ui-design.md`.

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
  responsiveness, and accessibility. For a new page, flow, or redesign, also
  generate a restrained visual direction through Automatic UI Design; for a
  local correction, reuse the established pattern instead.
- Do not invent a brand identity or broad design system. Auto-generated color,
  typography, spacing, radii, shadows, iconography, imagery, or motion choices
  must remain local to the requested surface and be grounded in existing
  tokens, adjacent patterns, platform conventions, or an explicit user goal.
- Keep missing visual decisions bounded and reversible. Ask the user only when
  the missing decision materially affects branding, information architecture,
  accessibility, compatibility, or implementation scope.

## Context placement

Put concise UI information into `implementation_constraints`, for example:

- `AUTO_UI_DESIGN: Use a compact card-first layout with one primary action; reuse the existing theme tokens.`
- `Confirmed screenshot establishes the mobile card hierarchy and primary action.`
- `Reuse the existing theme and adjacent detail-page components.`
- `Required states: loading, empty, error, disabled, and success.`
- `No design source establishes motion; do not introduce custom animation.`

For verification, capture only the focused route/screen and states needed to
establish the acceptance criteria. Codex collects the screenshots or rendered
evidence; ChatGPT Web judges them against the generated or confirmed design
constraints. Keep visual evidence in the active task response unless a saved
review is explicitly requested.
