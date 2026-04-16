---
name: standup
description: Quick session start — shows recent activity, current state, and what to do next. Lighter than /next-task for continuing sessions where you already know the context.
argument-hint: 'Optional: "yesterday" to see last 24h, or "week" for the past 7 days'
---

# Standup

Quick status check. No heavy analysis — just facts and a recommendation.

---

## 1. Recent Activity

- Run `git log --oneline --since="24 hours ago"` (or `--since="7 days ago"` if argument is "week")
- Count commits and summarise in one line

## 2. Current State

- Read `tasks/todo.md` Active This Session table
  - Any rows with status `Queued` or in-progress? → Show them
  - All rows `✅ Done`? → Note "session table clear"
- Read `tasks/todo.md` Open Bugs section
  - Any open bugs? → Flag them

## 3. Quick Health Check

- Are there un-codified lessons? Count entries in `tasks/lessons.md` not yet marked `(→ codified)`
- Determine the actual day of week by running `date +%A`
- Run `date +%Y-%m-%d` to get today's date
- Read `tasks/maintenance-log.md` (if it exists) to get last-run dates for maintenance tasks
- Fire a maintenance trigger only if: the cadence condition is true AND the task hasn't been run yet today

## 4. Output

```
## Standup

**Recent**: [N commits — one-line summary of what changed]
**Active**: [Current task from Active This Session, or "none"]
**Bugs**: [Open bug count, or "none"]
**Maintenance**: [Any triggers firing with task name, or "all clear"]
```

## 5. Next Step

Recommend ONE specific action: either continue the active task, start the next queued task, or run a maintenance task if one is firing.
