---
name: distill-lessons
description: 'Distill lessons.md entries into skill files where they will be consulted at point-of-use. Run periodically (every 5-10 new lessons, or monthly). Prevents lesson accumulation without behavior change.'
---

# Distill Lessons into Skill Files

Run this prompt periodically to push learned patterns from `tasks/lessons.md` into skill files where they'll actually be seen during work.

## Why

`tasks/lessons.md` is an append-only audit trail. The problem: it's only scanned once at session start. Skill files are read when actively working in a domain. Gotchas belong where they'll be seen.

## Mapping: lesson domain → skill file

Adapt this to your project's skill file structure:

| Domain | Skill |
|---|---|
| Test patterns, mocks, co-location | `testing/SKILL.md` |
| LLM calls, agent prompts, structured output | `agents/SKILL.md` (if exists) |
| DB queries, schema, migrations | `database/SKILL.md` (if exists) |
| API routes, server logic | `backend/SKILL.md` (if exists) |
| UI components, hooks, styling | `ui/SKILL.md` (if exists) |
| Debug scripts, trace inspection | `debugging/SKILL.md` |
| Security, auth, OWASP | `security/SKILL.md` |
| Architecture decisions, invariants | `architecture/SKILL.md` |
| Project conventions, naming, structure | `code-standards/SKILL.md` |

## Process

1. Read `tasks/lessons.md` — identify all entries NOT yet marked `(→ codified)`.
2. For each un-codified lesson, determine its domain using the table above.
3. Open the target `SKILL.md`. Find the nearest relevant section (`## Common Pitfalls`, `## Known Gotchas`, `## Critical Rules`, etc.).
4. Add a 1–3 sentence bullet:
   ```
   - **<Rule in imperative>.** <Short rationale>. <Key file or location if applicable>. _(Lesson N)_
   ```
5. Mark the lesson in `lessons.md` by appending `(→ codified in skills/<domain>/SKILL.md)` to its entry.
6. **Prune codified lessons**: once marked, delete the full entry from `lessons.md`. The rule now lives in the skill file. Keep only un-codified lessons in the file.
7. Escalate to `copilot-instructions.md` checklists only if the lesson was violated 2+ times.

## Structural Gap Trigger

After codifying, check: does a batch of lessons reveal a structural fix?

Triggers:
- 2+ lessons in the same layer in this pass
- A lesson rule says "always add X to every caller" (implies a shared utility is missing)
- A lesson was violated more than once

If triggered:
1. Identify what architectural change would make the rule unnecessary
2. Write an ADR to `docs/adr/`
3. Add it to `tasks/todo.md` as a backlog item

## Output

```
## Distillation Complete

Lessons codified: [N]
Skill files updated: [list]
Lessons deleted from lessons.md: [N]
Structural gaps found: [N — list or "none"]
ADRs created: [N — or "none"]
```
