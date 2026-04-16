---
name: spike
description: Time-boxed research spike — explore a question, read code/docs, write a findings document. Use instead of /implement for research tasks. No code changes expected.
argument-hint: 'Research question or task ID (e.g. "Can we replace web_search with Tavily?", "What would it take to add SSO?")'
---

# Research Spike

Explore a question and produce a structured findings document. This is NOT an implementation prompt — the output is a document, not code.

---

## 1. Define the Spike

```
Question    : [What are we trying to learn?]
Scope       : [What to investigate — list specific files, APIs, or systems]
Out of scope: [What we are NOT investigating]
Time box    : [Quick (< 1h) / Medium (1-3h) — pick one]
Output file : tasks/research/<topic>-audit.md
```

If the question is vague, ask the user to narrow it before proceeding.

## 2. Explore

Read code, query docs, and gather evidence. Typical paths:

- **Code audit**: Read the target service/module in full; trace data flow; count lines, callers, tests
- **Architecture question**: Read related files, check how similar problems are solved elsewhere
- **External tool/library**: Check API surface, pricing, integration path, compatibility with existing patterns
- **Production data**: Query via DB tools or debug scripts if applicable

For each finding, note:
- What you found (fact)
- Why it matters (impact)
- Whether it's a problem, an opportunity, or neutral

## 3. Analyse

For each question from Step 1:

```
### [Question]

**Finding**: [What the evidence shows]
**Impact**: [High / Medium / Low — and why]
**Recommendation**: [Do X / Skip / Defer / Needs more data]
```

## 4. Write Findings Document

Write to `tasks/research/<topic>-audit.md`:

```markdown
# [Topic] Audit

_Completed: [date]_

## Summary
[2-3 sentence executive summary]

## Findings
[One section per research question]

## Recommendation
[What to do and why — be direct]

## Open Questions
[What couldn't be answered in the time box]

## Next Steps
[Specific tasks to put in backlog, or "no action needed"]
```

## 5. Add to Backlog

If the spike surfaces new work, add it to the appropriate `tasks/backlog/*.md` file.
