# Recursive Self-Improvement Template

Structured refinement loops for Copilot sessions. Reference this template for non-trivial implementations.

---

## Full Loop (6-Phase) — Use for: new modules, architectural features, multi-file changes

### Phase 1: Specification

Before writing any code:

1. **Restate** the problem in your own words
2. **List assumptions** — what are you taking for granted?
3. **Define constraints** — project rules, performance requirements, compatibility
4. **Success criteria** — what does "done" look like? Be specific and measurable
5. **Risks** — what could go wrong? Edge cases, data issues, integration conflicts
6. **Dependencies** — what other code/systems does this touch?

### Phase 2: Implementation Plan

1. Break into ordered steps (max 7 — if more, split into sub-tasks)
2. Define file changes: which files created, which modified
3. Identify edge cases to handle
4. Define verification strategy: how will you prove it works?

**Pause here for user approval before writing code (for multi-file / Full 6-phase tasks).**

### Phase 3: Implementation

Write clean, production-quality code following project conventions (see `.github/skills/project/SKILL.md` and `.github/skills/code-standards/SKILL.md`).

### Phase 4: Critique

Brutally review your own output. Score 1-10 on each axis:

| Axis                | Question                                                                 |
| ------------------- | ------------------------------------------------------------------------ |
| **Correctness**     | Does it handle all inputs correctly? Edge cases?                         |
| **Security**        | SQL injection? Auth bypass? Secrets exposed? XSS?                        |
| **Performance**     | N+1 queries? Unnecessary re-renders? Unbounded loops?                    |
| **Maintainability** | Would a new developer understand this in 5 minutes?                      |
| **Scalability**     | Will this break at 10x data? 100x requests?                              |
| **Consistency**     | Does it follow project patterns? Or introduce a new style?               |
| **Error handling**  | What happens when the network fails? DB is down? External API times out? |

**Overall score** = average of all axes.

### Phase 5: Refactor

Based on critique findings:

1. Fix any score < 7 issues
2. Remove redundancy
3. Harden edge cases
4. Improve naming (no abbreviations, descriptive)
5. Add error boundaries where missing

### Phase 6: Final Version

Production-ready output. No commentary, no TODOs, no "we could also...".

### Auto-Recursion Rule

> **If overall score < 8, automatically iterate phases 4–5 again.** Maximum 2 iterations — if still < 8 after 2 passes, flag the specific issues for the user.

---

## Mini Loop (3-Phase) — Use for: small functions, single-file edits, bug fixes

1. **Generate** — Write the solution
2. **Critique harshly** — What's wrong? Edge cases? Performance? Could it be simpler?
3. **Refactor** — Apply fixes. Return final version only.

Skip this for truly trivial changes (typo fix, config value change).

---

## Mode Selection Guide

| Change scope                       | Loop         | Auto-pause?                           |
| ---------------------------------- | ------------ | ------------------------------------- |
| New module / feature / multi-file  | Full 6-phase | Pause after Phase 2 for user approval |
| Refactor existing code (2+ files)  | Full 6-phase | Pause after Phase 2                   |
| Single function / hook / component | Mini 3-phase | No pause — auto mode                  |
| Bug fix with clear root cause      | Mini 3-phase | No pause — auto mode                  |
| Config / env / typo                | None         | Just do it                            |

---

## Recursive Patterns Reference

| Pattern                              | Loop                                                        | Application        |
| ------------------------------------ | ----------------------------------------------------------- | ------------------ |
| **PDCA** (Plan-Do-Check-Act)         | Plan → Implement → Verify → Record lesson                   | Every task         |
| **OODA** (Observe-Orient-Decide-Act) | Read context → Understand state → Choose approach → Execute | Session start      |
| **Generate-Critique-Refine**         | LLM output → self-score → iterate                           | Agent code quality |
| **TDD feedback**                     | Write expectation → implement → verify → adjust             | Verification step  |

The key insight: **persistence creates real recursion**. Without saving lessons (`tasks/lessons.md`), decisions (memory), and context, each session starts from zero. The recursive loop only works if the Reflect step writes to durable storage.
