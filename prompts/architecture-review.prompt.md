---
name: architecture-review
description: 'Deep review of a system area for architectural concerns — data flow correctness, invariant violations, coupling, single points of failure, and scalability. Produces a prioritized findings report and draft fixes or ADRs. Use before major changes, after incidents, or quarterly.'
argument-hint: 'System area to review (e.g. "payment pipeline", "auth system", "full system") — be as specific or broad as needed'
---

# Architecture Review

Analyze a system area for architectural correctness, risk, and scalability.  
Output: a prioritized findings report, with P1 items having draft remediation plans.

---

## Phase 1 — Scope

```
Area          : [argument or ask user]
Files in scope: [list after reading]
Out of scope  : [what this review is NOT covering]
Time horizon  : [now-bugs / near-term scale / long-term design — pick one or all]
```

---

## Phase 2 — Read the Territory

Before analyzing anything, read the relevant source files. Do not reason from memory.

For each layer in scope:
1. Read the source file(s) — not just the interface, but the implementation
2. Note: inputs, outputs, error paths, and coupling to other layers
3. Note: what happens at 10× volume, what happens when a dependency fails

Also read:
- `docs/architecture.md` (if it exists)
- `tasks/lessons.md` — recurring failures signal architectural risk

---

## Phase 3 — Risk Checklists

### Invariant Audit

For every stated invariant in the code, docs, or instructions:
- Is the invariant actually enforced in code (test, type, guard)?
- Is it documented where it matters?
- What happens when it's violated?

### Coupling Audit

A module is over-coupled if:
- It imports from more than 3 other modules
- Changing its interface requires changing 5+ files
- It has side effects that aren't obvious from its signature

### Failure Mode Audit

For each external dependency (DB, cache, external API, queue):
- What happens when it's down?
- Does the system fail gracefully or cascade?
- Is there retry / circuit breaker / fallback logic?

### Scalability Audit

- Which queries don't have indexes on their filter columns?
- Which operations are O(n) in user data without pagination?
- Which cron jobs would take > 30s at 10× data?

### Silent Failure Audit

Silent failures are the most dangerous. Look for:
- Missing error handling on async operations
- Swallowed exceptions (`catch (e) {}`)
- Missing logging around critical state changes
- A/B logic that routes to the wrong path without logging

---

## Phase 4 — Findings Report

Classify every finding:

```
## P1 — Critical (data loss, security, or system halt)
## P2 — High (significant bug or design flaw, will bite soon)
## P3 — Medium (won't break today, but will compound)
```

For each finding:

```
**[Area]**: [description]
Impact: [what fails, how bad]
Root cause: [why this is a problem structurally]
Fix: [specific remediation OR "needs ADR before code change"]
```

---

## Phase 5 — Recommendations

For P1 findings: Produce a draft patch or ADR immediately.  
For P2 findings: Add to `tasks/todo.md` as bugs.  
For P3 findings: Add to `tasks/backlog/tech-debt.md`.

When multiple findings share a root cause (e.g. "missing centralized validation"), recommend the structural fix once, not N separate fixes.
