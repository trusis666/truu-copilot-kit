---
name: adr-draft
description: 'Draft an Architecture Decision Record (ADR, ARD, SDD, IRD, or RFD) for a technical decision. Ensures decisions are documented before implementation, with clear alternatives and consequences. Use when picking a new pattern, library, or approach; changing agent reasoning; adding a DB model; connecting a new API; or changing routing logic.'
argument-hint: 'Describe the decision to document (e.g. "adopt Resend for transactional email", "add BlogPost DB model", "change search agent to use structured output")'
---

# Decision Document Draft

Produce a structured decision record before implementation begins. Forces explicit trade-off analysis.

---

## Phase 1 — Classify the Decision

| If you're... | Document | Template |
|---|---|---|
| Picking a new library, pattern, or architectural approach | **ADR** | `docs/templates/TEMPLATE-ADR.md` |
| Changing agent reasoning, output format, or prompt strategy | **ARD** | `docs/templates/TEMPLATE-ARD.md` |
| Adding a DB model or schema that propagates across layers | **SDD** | `docs/templates/TEMPLATE-SDD.md` |
| Connecting a new external API or third-party service | **IRD** | `docs/templates/TEMPLATE-IRD.md` |
| Changing routing predicates, triage logic, or dispatch | **RFD** | `docs/templates/TEMPLATE-RFD.md` |

Read the template for the selected type before writing anything.

---

## Phase 2 — Frame the Decision

```
Decision   : [What exactly is being decided? One sentence.]
Trigger    : [What prompted this? Feature need, failure, scaling limit?]
Constraints: [What can't change? Existing contracts, data in DB, user commitments]
Deadline   : [When does this need to be decided? Why?]
Decider    : [Who needs to approve this?]
```

If you can't write a single-sentence "Decision" statement, the scope needs to be narrowed.

---

## Phase 3 — Research Current State

1. Read `docs/adr/README.md` — is there an existing ADR for this area? If so, read it.
2. Read the source files touched by this decision
3. Identify: what breaks if this decision goes wrong? what's the rollback path?

---

## Phase 4 — Identify Alternatives

List at least 3 alternatives. "Do nothing" (keep current approach) is always one.

For each alternative:

```
### Option N: [Name]

**How it works**: [1-2 sentences]
**Pros**: [bullet list]
**Cons / risks**: [bullet list]
**Verdict**: [Chosen / Rejected because X]
```

---

## Phase 5 — Write the Document

Use the template selected in Phase 1. The document must include:

- **Status**: `Draft`
- **Context**: What situation forced this decision?
- **Decision**: What was decided? One clear statement.
- **Alternatives Considered**: From Phase 4
- **Consequences**: Positive, negative, risks
- **Implementation Notes**: What must be done? Any order-dependency?

Save to `docs/adr/ADR-NNN-<short-title>.md` (or `docs/ard/`, `docs/sdd/`, etc.).

---

## Phase 6 — Register the Document

Add a row to `docs/adr/README.md`:

| ADR | Title | Status | Date |
|---|---|---|---|
| ADR-NNN | [Title] | Draft | [date] |

---

## Phase 7 — Handoff

Present the draft to the user for review. After approval:
- Status changes from `Draft` → `Accepted`
- Implementation begins (run `/implement`)
- After implementation, status changes to `Implemented`
