---
name: architecture
description: 'Reviewing, planning, and documenting architecture decisions — system design, technical debt, scalability, ADR/ARD/SDD/IRD/RFD drafts, service boundaries, data modeling, and long-term plans. Use when reasoning about how the system is structured, where it will break, or what changes will compound well or badly. Trigger keywords: architecture, design, scale, tech debt, ADR, decision doc, bottleneck, system design, data model, future plan, service boundaries, performance, cost optimization.'
---

# Architecture

Use this skill when analyzing system structure, writing decision documents, identifying technical debt, planning scaling, or making technology choices.

---

## Guiding Principles

- **Every constraint is a decision.** Name what's being traded off: reversibility, cost, coupling, operational burden.
- **Boring technology first.** Justify exotic choices hard. Favor what the team already understands.
- **Think in 3 time horizons**: Now (next sprint), Near (next quarter), Far (next year). Separate concerns.
- **Debt is not bad by default.** Rushed debt in non-critical paths is acceptable. Debt in the critical execution path is not — it fails silently.
- **Second-order effects.** Every architectural change ripples. Trace the ripple before approving.

---

## Architecture Quick Reference

> **Project-specific.** Read `.github/skills/project/SKILL.md` for the actual layer map for this codebase.
> The questions below apply regardless of stack.

Key questions when reviewing any architectural area:
1. What does data flow look like end-to-end through this area?
2. Where are the synchronous vs. async boundaries?
3. What fails silently vs. loudly?
4. What's the blast radius if this breaks in production?
5. What invariants must NEVER be violated, and who enforces them?

---

## Decision Document Selection

Write a decision document **before touching code** whenever any of these triggers fire:

| Situation | Document | Template location |
|---|---|---|
| Picking a new library, pattern, or architectural approach | **ADR** | `docs/templates/TEMPLATE-ADR.md` |
| Changing agent reasoning, output format, or prompt strategy | **ARD** | `docs/templates/TEMPLATE-ARD.md` |
| New DB model or Zod schema that propagates across layers | **SDD** | `docs/templates/TEMPLATE-SDD.md` |
| Connecting a new external API or third-party service | **IRD** | `docs/templates/TEMPLATE-IRD.md` |
| Changing routing predicates or autonomy dispatch logic | **RFD** | `docs/templates/TEMPLATE-RFD.md` |

**After writing**: add to `docs/adr/README.md` immediately, set status to `Draft`.  
**Status lifecycle**: `Draft` → `Accepted` → `Implemented` → (optionally) `Superseded`

If no template directory exists yet, create `docs/adr/` with a `README.md` index and `docs/templates/` with one file per type above.

---

## Decision Document Structure (ADR)

Every ADR answers these questions, in order:

```markdown
## Status
Draft | Accepted | Implemented | Superseded by ADR-XXX

## Context
[What situation forced this decision? What stayed the same?]

## Decision
[What was decided? One clear statement.]

## Alternatives Considered
[At least 3 alternatives including "do nothing". For each: pros, cons, reason rejected.]

## Consequences
### Positive
### Negative / Trade-offs
### Risks

## Implementation Notes
[What must be done? Any order-dependency?]
```

---

## Technical Debt Taxonomy

| Category | Description | Urgency signal |
|---|---|---|
| **Design debt** | Wrong abstraction level, or wrong boundary | Hard to add features without touching many files |
| **Test debt** | Missing coverage on critical paths | Bugs appear in production that should be caught by tests |
| **Documentation debt** | Undocumented invariants, implicit conventions | New team members break things regularly |
| **Dependency debt** | Outdated or vulnerable packages | `npm audit` finds high/critical issues |
| **Performance debt** | N+1 queries, unindexed columns, unbounded loops | Response times degrade as data grows |

Prioritize: Design debt in critical paths > Test debt in critical paths > everything else.

---

## Self-Critique Rubric for Architectural Proposals

Before presenting any architectural proposal, score it 1–5 on each axis:

| Axis | Score 1 | Score 5 |
|---|---|---|
| **Reversibility** | One-way door, hard to undo | Can be A/B tested or gradually rolled out |
| **Blast radius** | Failure affects entire system | Failure is isolated to one module |
| **Cognitive load** | Developers need to know the whole system to work here | Works independently with a clear interface |
| **Observability** | Failures are silent | Every error surfaces with actionable context |
| **Consistency** | Introduces a new pattern | Follows existing project conventions |

Minimum acceptable: all axes ≥ 3, average ≥ 4. If not, the proposal needs revision before implementation.
