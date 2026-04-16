---
name: implement
description: Full implementation workflow for a planned task — restate the problem, select the right refinement loop, query library docs, plan (with approval pause for multi-file work), implement, run checks, write tests. Use for any non-trivial code change.
argument-hint: 'Describe the task to implement, or reference a task ID from tasks/todo.md'
---

# Implementation Workflow

Structured implementation from problem statement to passing tests. Follows the recursive template and pre-completion gate from `copilot-instructions.md`.

---

## Phase 0 — Resolve Task

If an argument was provided, use it as the task description.

If **no argument was provided**, read `tasks/todo.md` Active This Session table and find the first row with status `Queued` or non-`✅ Done`. Use that row's ID and title as the task. If no active task is found, tell the user to add one first.

---

## Phase 1 — Restate the Problem

Before touching any code, answer these:

```
Problem     : [What is broken, missing, or needs to change?]
Assumptions : [What am I taking for granted?]
Constraints : [Project rules, compatibility limits — list any that apply to this change]
Success     : [What does "done" look like? Be specific and measurable]
Risks       : [What could go wrong? Edge cases, data issues, integration conflicts]
Out of scope: [What are you explicitly NOT doing in this task?]
```

**If you can't fill in "Success" with a testable statement, stop and clarify with the user before proceeding.**

---

## Phase 2 — Select Refinement Loop + Decide on Decision Doc

### Loop selection

| Change scope | Loop | Auto-pause? |
|---|---|---|
| New module / feature / multi-file | Full 6-phase | **Pause here for approval** |
| Refactor existing code (2+ files) | Full 6-phase | **Pause here for approval** |
| Single function / hook / component | Mini 3-phase | No pause — auto mode |
| Bug fix with clear root cause | Mini 3-phase | No pause — auto mode |
| Config / env / typo | None | Just do it |

### Decision doc gate

Write a decision doc **before writing code** if any trigger fires:

| Trigger | Doc type | Template |
|---|---|---|
| New pattern, library, or architectural approach | ADR | `docs/templates/TEMPLATE-ADR.md` |
| New external API integration | IRD | `docs/templates/TEMPLATE-IRD.md` |
| New/changed DB model or schema (wide propagation) | SDD | `docs/templates/TEMPLATE-SDD.md` |
| Agent reasoning or output format change | ARD | `docs/templates/TEMPLATE-ARD.md` |
| Routing / dispatch logic change | RFD | `docs/templates/TEMPLATE-RFD.md` |

After writing the doc: add it to `docs/adr/README.md` and set status `Draft`.

---

## Phase 3 — Query Library Docs

**Before writing any code that calls an external library**, query the docs (context7 MCP or official docs):

```
1. Resolve the library ID
2. Query-docs with the specific API or topic you need
```

Never guess API signatures.

---

## Phase 4 — Implementation Plan

Break the work into ordered steps (max 7 — split into sub-tasks if more needed):

```
1. [Step: file to create or modify, what changes]
2. ...
```

**For Full 6-phase (multi-file) tasks: show this plan and wait for "go ahead" before writing code.**  
Mini 3-phase tasks proceed immediately.

---

## Phase 5 — Implementation

Write production-quality code following the conventions in `.github/skills/project/SKILL.md` and `.github/skills/code-standards/SKILL.md`.

Non-negotiable regardless of project:
- TypeScript strict, no unqualified `any`
- Co-located test file for every new module
- Input validation at API/system boundaries
- No secrets hardcoded in source files
- Auth checked before any data access in route handlers

---

## Phase 6 — Verification

1. Run `{{CHECK_COMMAND}}` — must be **fully green**. Any failure is a real regression to fix.
2. Run the dev server and manually verify the behavior in a browser or via curl.
3. `tsc` passing does not mean it works. Verify actual behavior.

---

## Phase 7 — Pre-Completion Gate

Before marking done:

- [ ] `{{CHECK_COMMAND}}` is green
- [ ] Co-located `.test.ts` file exists and passes for every new module
- [ ] `CHANGELOG.md` updated
- [ ] `tasks/todo.md` Active This Session row removed for this task
- [ ] Relevant `.github/skills/<domain>/SKILL.md` updated if behavior changed
- [ ] Do NOT auto-commit — user commits via `/commit`
