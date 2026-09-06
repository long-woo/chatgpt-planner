# Reference Index

Read only the references needed at the current workflow gate. All paths below
are relative to this skill directory.

| Reference | Purpose | Read when | Generates or governs |
|---|---|---|---|
| `workflow.md` | High-level two-agent collaboration flow and responsibilities | Starting or resuming a project workflow | Overall stage sequence |
| `workflow-state.md` | State machine, owners, transition gates, and update protocol | Before initializing or changing any stage | `.chatgpt/workflow-state.json` |
| `decision-records.md` | Durable confirmed product, design, and technical decisions across iterations | At workflow start and whenever a proposal may affect an existing decision | `.chatgpt/decision-records.md` |
| `requirement-review.md` | Scope-control and requirement review rules | At `REQUIREMENT_REVIEW` | `.chatgpt/requirement-review.md` |
| `ui-design-context.md` | Evidence and boundaries for UI design decisions | Before UI design and handoff | `ui_context` in Task Contract |
| `design-handoff.md` | Normative product/UI-to-engineering contract | At `DESIGN_HANDOFF` and before Engineering Analysis | `.chatgpt/design-handoff.md` |
| `engineering-analysis.md` | Repository impact assessment before planning | At `ENGINEERING_ANALYSIS` | `.chatgpt/engineering-analysis.md` |
| `planner-protocol.md` | ChatGPT Web planning behavior and prompt | At `TASK_PLANNING` | Planner interaction |
| `task-contract.md` | Task Contract schema and acceptance criteria rules | When producing or validating the plan | Accepted Task Contract JSON |
| `visual-qa.md` | Evidence collection, tool fallback, and visual review gate | At `VISUAL_QA` | `.chatgpt/visual-review.md` |
| `browser-workflow.md` | ChatGPT Web browser and evidence transfer procedure | When opening ChatGPT Web or collecting Web evidence | Browser handoffs |
| `reviewer-protocol.md` | Final requirement review criteria and review loop | At `FINAL_REVIEW` | PASS/NEEDS_FIX/BLOCKED |
| `failure-handling.md` | Recovery for missing artifacts, browser loss, and failed checks | Whenever a gate or handoff fails | Recovery decisions |

Legacy root-level stage artifacts remain readable for compatibility, but new
executions should generate the canonical files under `.chatgpt/`.
