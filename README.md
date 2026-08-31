# ChatGPT Planner

Use ChatGPT Web as the **planner and reviewer**, and Codex as the **implementation engineer**.

`chatgpt-planner` is a development orchestration Skill designed for workflows where:

- ChatGPT Web understands the requirement
- ChatGPT Web breaks the requirement into implementation tasks
- ChatGPT Web defines acceptance criteria
- Codex inspects the actual repository
- Codex writes and modifies the code
- Codex runs tests and verification
- ChatGPT Web reviews whether the final implementation satisfies the original requirement

In short:

```text
Requirement
    ↓
ChatGPT Web
Planning / Task Breakdown
    ↓
Codex
Implementation / Testing
    ↓
ChatGPT Web
Requirement Review
    ↓
PASS / NEEDS_FIX
```

## Why

Coding agents are good at understanding repositories and making concrete code changes, but allowing the coding agent to simultaneously decide product requirements can easily lead to scope drift.

This Skill separates the responsibilities.

### ChatGPT Web

Responsible for:

- requirement understanding
- product behavior
- solution planning
- task decomposition
- edge cases
- acceptance criteria
- final requirement review

### Codex

Responsible for:

- repository exploration
- locating relevant code
- understanding the current architecture
- implementing the plan
- writing tests
- running lint/typecheck/build/tests
- reporting repository facts

The key principle is:

> ChatGPT decides what should be built. Codex decides how it fits the real repository and implements it.

## Requirements

This Skill is intended for a development environment with:

- Codex or another repository-aware coding agent
- local repository access
- shell/test execution
- ChatGPT desktop client
- built-in browser/computer-use support
- access to `chatgpt.com`

The built-in browser is used to communicate with ChatGPT Web.

## Installation

Place the Skill directory in the location used by your Agent Skills environment.

Directory:

```text
chatgpt-planner/
├── README.md
├── SKILL.md
└── references/
    ├── planner-protocol.md
    ├── task-contract.md
    ├── reviewer-protocol.md
    ├── browser-workflow.md
    └── failure-handling.md
```

The Agent executes the workflow according to `SKILL.md`.

`references/` contains detailed protocols that are loaded only when needed.

## Usage

You normally do not need to manually invoke every stage.

Describe the development request normally.

For example:

```text
Use chatgpt-planner to implement sharing for today's recommended place.
```

Or:

```text
Use chatgpt-planner to fix this bug:

When the daily recommendation limit has been reached,
reopening the app after some time automatically fetches another recommendation.
```

The Skill should automatically execute the planning and implementation workflow.

## Example

User request:

```text
Use chatgpt-planner to add destination weather information to the place detail page.
```

The workflow becomes:

```text
1. Codex inspects the repository

2. Codex creates REPO_CONTEXT

3. Built-in browser opens ChatGPT Web

4. ChatGPT Web receives:
   - USER_REQUEST
   - PROJECT_CONTEXT
   - REPO_CONTEXT
   - CONSTRAINTS

5. ChatGPT Web creates:
   - requirements
   - assumptions
   - non-goals
   - tasks
   - acceptance criteria
   - verification plan

6. Codex validates the plan against the repository

7. Codex implements the tasks

8. Codex runs:
   - tests
   - typecheck
   - lint
   - build
   where applicable

9. Implementation result is sent back to ChatGPT Web

10. ChatGPT Web returns:
    PASS
    or
    NEEDS_FIX

11. Codex fixes required issues if necessary

12. Final result is returned to the user
```

## Usage modes

### Plan only

Use when you want analysis and task decomposition without changing code.

Example:

```text
Use chatgpt-planner in plan mode.

I want to support 2-4 users shaking at the same time and receiving the same destination.
Analyze the requirement and create an implementation plan.
Do not modify code.
```

Typical flow:

```text
Repository reconnaissance
→ ChatGPT Web
→ Implementation plan
```

### Plan and implement

This is the normal mode.

Example:

```text
Use chatgpt-planner to implement user profile editing.
```

Flow:

```text
Repository
→ ChatGPT Web Planner
→ Codex
→ Tests
→ ChatGPT Web Reviewer
```

### Review existing implementation

Use when code already exists.

Example:

```text
Use chatgpt-planner to review whether the current sharing implementation
fully satisfies the original requirement.
```

Flow:

```text
Requirement
+
Current implementation
+
Verification
→ ChatGPT Web Reviewer
```

## Recommended prompts

You can use normal natural-language requests.

### Feature

```text
Use chatgpt-planner to implement this feature:

The place detail page should display destination weather and travel advice.
```

### Bug fix

```text
Use chatgpt-planner to fix this bug:

After the user reaches the daily recommendation limit,
reopening the app later can incorrectly generate another recommendation.
```

### UI change

```text
Use chatgpt-planner to redesign the splash screen.

Requirements:
- Do not display loading text
- Use a flat logo
- Only change visuals
- Do not change startup behavior
```

### Refactor

```text
Use chatgpt-planner to refactor the recommendation state management.

Behavior must remain unchanged.
```

### Large feature

```text
Use chatgpt-planner to design and implement a group recommendation feature.

2-4 users should be able to join the same temporary group,
shake together, and receive the same destination.
```

For large features, ChatGPT Web should break the requirement into multiple bounded tasks before Codex writes code.

## Planner conversation

It is recommended to use one dedicated ChatGPT Web Planner conversation per project.

For example:

```text
Markgo — Product & Engineering Planner
```

or:

```text
STC — Product & Engineering Planner
```

The same conversation can be used for:

```text
Planning
→ Repository corrections
→ Implementation review
```

However, the Skill should not rely entirely on previous conversation memory.

Important context should be included with every request.

## Repository context

Before asking ChatGPT Web to make a plan, Codex performs a lightweight repository inspection.

Example:

```text
Project:
Markgo

Platform:
Mobile app

Stack:
React Native
TypeScript
Zustand
TanStack Query

Relevant areas:
src/features/place
src/screens/home
src/services/recommendation

Existing behavior:
Place detail already exists.
Recommendations have a daily usage limit.

Validation:
pnpm lint
pnpm typecheck
pnpm test
```

This prevents ChatGPT Web from designing against an imaginary repository structure.

## Plan correction

ChatGPT Web may occasionally make an incorrect assumption about the repository.

For example:

```text
Planner:
Add the behavior to RecommendationManager.

Repository:
RecommendationManager does not exist.
The behavior currently lives in RecommendationStore.
```

Codex should not silently reinterpret the plan.

Instead:

```text
Codex
    ↓
PLAN_CORRECTION
    ↓
ChatGPT Web
    ↓
Revised Plan
```

Repository facts are authoritative for repository state.

## Scope control

Codex should not perform unrelated improvements while implementing a task.

If it discovers something unrelated, it should report it as:

```text
DISCOVERED_ISSUES
```

Example:

```text
DISCOVERED_ISSUES

- The existing image cache has duplicated retry logic.
- RecommendationStore currently has weak test coverage.
```

These issues do not automatically become implementation work.

This prevents:

```text
Small feature
→ unrelated refactor
→ dependency upgrade
→ architecture rewrite
```

## Review

After implementation, ChatGPT Web reviews the result against the accepted requirements.

Possible results:

### PASS

The implementation satisfies the requirement.

### NEEDS_FIX

There is a concrete requirement failure.

Codex receives bounded fix tasks and implements them.

### BLOCKED

There is not enough evidence to establish correctness.

For example:

- required platform behavior cannot be validated
- an external dependency is unavailable
- required environment access is missing

## Authority

When information conflicts:

```text
User instruction
    ↓
Product requirement
    ↓
Repository facts
    ↓
Accepted Planner plan
    ↓
Implementation preference
```

This prevents either agent from taking too much authority.

## Files

### `SKILL.md`

Main orchestration instructions.

Contains:

- activation rules
- roles
- core workflow
- authority model
- completion rules

### `references/planner-protocol.md`

Defines how ChatGPT Web should analyze requirements and produce plans.

### `references/task-contract.md`

Defines the structured JSON contract exchanged between Planner and Codex.

### `references/reviewer-protocol.md`

Defines how ChatGPT Web reviews completed implementation.

### `references/browser-workflow.md`

Defines how the built-in browser should open and interact with ChatGPT Web.

### `references/failure-handling.md`

Defines behavior for:

- browser failures
- lost conversations
- malformed Planner responses
- test failures
- verification gaps
- review loops
- destructive operations

## Design philosophy

This Skill intentionally separates four concerns:

```text
Think
↓
Plan
↓
Implement
↓
Verify
```

ChatGPT Web focuses on:

```text
Think + Plan + Requirement Verify
```

Codex focuses on:

```text
Repository + Implement + Technical Verify
```

The result is a development workflow where product decisions and code implementation remain clearly separated.
