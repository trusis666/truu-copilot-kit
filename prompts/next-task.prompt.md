---
name: next-task
description: Analyse tasks/todo.md to identify the highest-priority next task. Reads context, classifies all open tasks, and presents a recommended task with reasoning.
---

# Next Task Selection

Read the backlog, load project context, and recommend the single best task to work on now.

## 1. Session Start Checklist

Run these **before** analysing the backlog:

1. **Read `tasks/lessons-quick-ref.md`** (if it exists) — 30-second scan of all rules
2. **Read `tasks/todo.md`** — the full file, top to bottom

## 2. Classify Every Open Task

Scan `tasks/todo.md` and build a table of **all uncompleted items**. For each, note:

| Field | What to capture |
|---|---|
| **ID** | Task number or section name |
| **Title** | Short description |
| **Priority** | P1–P8 as marked, or infer from section placement |
| **Status** | Not started / Partially done / Blocked |
| **Dependencies** | What must be done first |
| **Effort** | Quick win (< 1h) / Medium (1–4h) / Large (4h+) |
| **Type** | Code / Config / Manual / Research |

## 3. Apply Selection Criteria

Rank using these rules **in order of priority**:

1. **Unfinished work** — partially done items are cheapest to finish
2. **Explicitly prioritised** — items in a "CURRENT PRIORITY" or "Active" section
3. **Unblocked before blocked** — skip tasks whose dependencies aren't met
4. **Quick wins first** — among equal-priority, prefer tasks completable in < 1 hour
5. **Revenue impact** — prefer tasks that directly improve revenue, conversion, or distribution
6. **Foundation before features** — schema/API work that unblocks multiple downstream tasks
7. **Code tasks over manual tasks** — flag manual tasks but don't select them as the AI task

## 4. Present Recommendation

```
## Recommended Next Task

**Task**: [ID — Title]
**Why**: [One sentence — why this ranks highest right now]
**Effort**: [Quick / Medium / Large]
**First step**: [The very first concrete action to take]

## Alternatives Considered
1. [Task] — skipped because [reason]
2. [Task] — skipped because [reason]
```

Then ask: "Should I start on this, or do you want a different task?"
