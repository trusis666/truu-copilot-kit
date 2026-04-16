---
name: ingest-tech-link
description: 'Read a tech article, doc, or library page and extract actionable backlog items. Use when you find something worth investigating but don''t have time to act on it now.'
argument-hint: 'URL of the article, doc, or library to ingest'
---

# Ingest Tech Link

Read an external resource and extract what's worth acting on.

---

## Phase 1 — Read the Resource

If a URL was provided: fetch the content.  
If no URL: ask the user for the URL or paste the content.

---

## Phase 2 — Summarise

```
**Source**: [URL or title]
**Type**: [Library / Tutorial / RFC / Blog post / Release notes / Tool]
**Relevance**: [Why does this matter for this project? Be specific.]
**TL;DR**: [2-3 sentences: what is it, what does it do]
```

---

## Phase 3 — Extract Actions

For each actionable item found:

```
### Action: [Title]
**Type**: Research spike / Upgrade / Refactor / New feature / Security patch
**Priority**: P1 / P2 / P3
**Why now**: [What would we gain, or what breaks if we ignore it]
**Estimated effort**: S (< 2h) / M (< 1d) / L (> 1d)
**Proposed task**: [Specific task description for the backlog]
```

---

## Phase 4 — Add to Backlog

For each P1 or P2 action: add to `tasks/todo.md` or `tasks/backlog/features.md`.  
For P3: add to `tasks/backlog/tech-debt.md` with the source URL.

Format:
```markdown
- [ ] [Title] — from [source URL]. [One sentence rationale]. _Priority: P2_
```

---

## Phase 5 — Output

```
## Ingested: [Title]

**Added to backlog**: [N items]
**Skipped**: [N items — reason: not relevant / already done / too speculative]
**Immediate action**: [If anything is P1 — flag it explicitly]
```
