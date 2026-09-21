# Automatic UI Design

Automatic UI Design is a ChatGPT Web design subroutine for requests that change
a user-visible surface. It runs inside the single ChatGPT Web analysis turn and
does not create a separate artifact or handoff. The current client only routes
the request and transfers context; Codex only implements and collects evidence
for the result. ChatGPT Web also judges the submitted UI evidence during
verification.

```text
Current client detects UI design mode
  → ChatGPT Web designs the UI
  → execution context carries the design constraints
  → Codex implements the approved direction
```

## When it runs

Set the routing metadata `ui_design_mode` before planning:

| Mode | Use when | Behavior |
|---|---|---|
| `REUSE_PATTERN` | Copy/style/local UI correction with an established nearby pattern | Reuse existing components, tokens, hierarchy, and interaction states |
| `REFERENCE_ADAPTATION` | The user supplied a screenshot, Figma, mockup, or design system | Adapt the confirmed reference to verified repository constraints |
| `AUTO_DESIGN` | New page, new flow, redesign, or UI feature without a complete visual reference | Generate a bounded UI direction and interaction specification |

`LITE` may use `REUSE_PATTERN` only. If it needs `AUTO_DESIGN` or a material
visual decision, upgrade to `STANDARD` or `COMPREHENSIVE` before implementation.
`STANDARD` automatically uses `AUTO_DESIGN` for a new page or flow unless a
confirmed reference is available. `COMPREHENSIVE` uses `AUTO_DESIGN` for new
surfaces and core journeys when the user has not supplied a complete design.

## Automatic design output

For `AUTO_DESIGN`, ChatGPT Web must actively make the bounded UI design
decisions and include concise, implementation-ready UI constraints in the
execution context's `implementation_constraints`. It must not merely tell Codex
to “design the UI” or repeat the user request. Cover only what the request
needs:

- visual direction: tone, density, hierarchy, color/typography intent, and
  reuse of existing tokens where available;
- layout: primary surface, content order, responsive behavior, and key
  component composition;
- interaction: entry point, primary action, feedback, navigation, and error
  recovery;
- states: loading, empty, error, disabled, success, and permission states when
  applicable;
- accessibility: keyboard/focus, contrast, semantics, touch targets, and
  reduced motion when applicable;
- explicit non-goals: visual or product areas that must not expand.

Prefix the constraints with `AUTO_UI_DESIGN` so Codex can distinguish ChatGPT
Web's design intent from repository facts. Keep the design compact; do not
return a mockup, task list, or design-document artifact unless the user
explicitly asks for one.

## Design authority and safety

Use this priority:

1. explicit user requirements and supplied visual references;
2. verified repository design system, tokens, and adjacent UI patterns;
3. platform conventions for behavior and accessibility;
4. a restrained generated direction that fits the product context.

The generated design must be reversible, scoped to the requested surface, and
traceable to the user request or verified repository patterns. Do not invent a
brand identity, new information architecture, unrelated screens, or optional
polish. Ask the user only when a missing visual decision materially changes
branding, information architecture, accessibility, compatibility, or scope.

## Responsibility boundary

- ChatGPT Web owns the UI design decisions and the design constraints in the
  execution context.
- The current client owns routing, context transfer, and validation only.
- Codex owns repository-specific implementation and raw evidence collection
  only; it does not own the verification judgment.

## Implementation boundary

Codex treats the generated constraints as product/design intent. It may choose
repository-specific components and CSS/layout details, but it must not silently
replace the direction, add new flows, or broaden the visual system. Verification
evidence should cover only the focused route/screen and states needed by the
acceptance criteria; Codex collects that evidence and ChatGPT Web judges it.
