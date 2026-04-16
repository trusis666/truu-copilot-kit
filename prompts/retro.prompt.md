---
name: retro
description: Session or weekly retrospective — reviews what was accomplished, what went well, what caused friction, and captures new lessons. Run at end of session or end of week.
argument-hint: 'Optional: "session" (default) or "week" for a broader look'
---

# Retrospective

Review the work done and extract actionable improvements.

---

## 1. Gather Data

### For session retro (default)

- Run `git log --oneline --since="8 hours ago"` to see what was committed
- Read `tasks/todo.md` Active This Session — what was planned vs completed
- Read `CHANGELOG.md` — latest entries from today
- Check `tasks/lessons.md` — were any new lessons added this session?

### For weekly retro (argument: "week")

- Run `git log --oneline --since="7 days ago"` to see all commits this week
- Read `tasks/backlog/*.md` — what moved from backlog to done?
- Count: tasks completed, bugs fixed, research docs written
- Check `tasks/lessons.md` — all lessons added this week

## 2. Analyse

```
## What got done
[List of completed tasks/commits with one-line summaries]

## What went well
[Patterns that worked]
[Tools/prompts that saved time]
[Good decisions that paid off]

## What caused friction
[Repeated issues]
[Time sinks — tasks that took longer than expected and why]
[Blocked moments — what caused them, how were they resolved]

## Surprises
[Things discovered that weren't expected — pre-existing bugs, undocumented behaviour]
```

## 3. Extract Lessons

For each friction point or surprise:

| Friction / Surprise | Already a lesson? | Action |
|---|---|---|
| [description] | Check `tasks/lessons.md` | Add new lesson / Skip (already known) |

Write any new lessons to `tasks/lessons.md` immediately.

## 4. Update Task Tracking

- Remove completed rows from `tasks/todo.md` Active This Session
- Move unfinished items to the appropriate `tasks/backlog/*.md` file
- Note anything that needs to carry forward

## 5. Output Summary

```
## Retro Summary

**Completed**: [N tasks]
**New lessons**: [N — list titles]
**Carries forward**: [Any in-progress work or blocked items]
**Next session priority**: [One sentence]
```
