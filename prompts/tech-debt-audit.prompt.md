---
name: tech-debt-audit
description: 'Cross-reference lessons and code to build a prioritized tech debt register. Run monthly or before major refactors to understand where the real debt is.'
---

# Tech Debt Audit

Build a prioritized tech debt register by cross-referencing lessons, code patterns, and architecture pain points.

---

## Phase 1 — Gather Evidence

### From lessons
1. Read `tasks/lessons.md` — group repeated patterns (a lesson repeated 2+ times is structural debt)
2. Note: which file paths appear most in lessons? (those are hot spots)

### From code
3. Run a search for known debt markers:
   ```bash
   grep -r "TODO\|FIXME\|HACK\|XXX\|DEBT" --include="*.ts" --include="*.tsx" src/ lib/
   ```
4. Note: `any` types that aren't justified, `eslint-disable` comments, `// @ts-ignore`

### From architecture
5. Read `docs/architecture.md` (if it exists)
6. Ask: which areas have the most incidents or bugs? (check recent git log for `fix:` commits in hot spots)

---

## Phase 2 — Classify Debt

| Category | Description | Examples |
|---|---|---|
| **Design debt** | Wrong abstraction or boundary | A service doing 3 different things |
| **Test debt** | Missing coverage on critical path | Business logic with 0% branch coverage |
| **Documentation debt** | Undocumented invariants | Implicit "always pass X" rule with no enforcement |
| **Performance debt** | Gradual degradation as scale grows | Unindexed query called on every page load |
| **Dependency debt** | Outdated/vulnerable packages | `npm audit` critical findings |

---

## Phase 3 — Build the Register

For each debt item:

```
## [ID]: [Short title]

**Category**: Design / Test / Documentation / Performance / Dependency
**Risk level**: P1 (blocks scale) / P2 (will bite soon) / P3 (long-term compound)
**Evidence**: [Lesson numbers, file paths, or symptoms]
**Root cause**: [Why does this exist?]
**Remediation**: [What needs to change? Estimated effort: S/M/L]
**Impact of not fixing**: [What gets worse if we leave this?]
```

---

## Phase 4 — Prioritize

Rank by: Risk × Frequency of touch. Debt in code that no one touches is fine.
Debt in code touched every sprint computes daily.

P1 items → Add to `tasks/todo.md` as bugs immediately.  
P2 items → Add to `tasks/backlog/tech-debt.md`.  
P3 items → Note in the audit doc, revisit quarterly.

---

## Phase 5 — Write the Audit Doc

Save to `tasks/research/tech-debt-NNN.md`:

```markdown
# Tech Debt Audit — [date]

## Summary
[N items found: X P1, Y P2, Z P3]

## Register
[From Phase 3]

## Patterns
[Which categories dominate? What does that tell us about our process?]

## Immediate Actions
[P1 items → links to todos]
```
