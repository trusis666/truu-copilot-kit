---
name: improve-copilot-instructions
description: 'Review and improve the developer AI instructions — copilot-instructions.md, skill files, prompt files, and agent files. Identifies stale rules, missing gates, redundancy, and gaps. Use after adding new patterns to lessons.md, when instructions feel out of sync with actual code, or periodically as maintenance.'
argument-hint: 'Optional: scope to a specific section or skill (e.g. "pre-completion gate", "security skill", "commit prompt") or leave blank for a full review'
---

# Improve Copilot Instructions

Review the developer AI instruction layer for quality, coverage, and consistency. Produces a prioritized improvement plan and applies fixes.

---

## Phase 1 — Scope

If an argument was provided, focus only on that section.

If no argument, do a full review:
- `.github/copilot-instructions.md`
- `.github/agents/*.agent.md`
- `.github/skills/*/SKILL.md`
- `.github/prompts/*.prompt.md`

**Read the scope of files first before scoring anything.**

---

## Phase 2 — Lessons Sync Check

1. Read `tasks/lessons.md` — note the most recent 20 entries
2. Read `tasks/lessons-quick-ref.md` (if it exists)
3. For each lesson or rule: **Is this reflected in `copilot-instructions.md` or a relevant skill file?**

Gap list:

```
Lessons not yet in instructions:
- Lesson N: [summary] → should be in [file / section]

Instructions not backed by any lesson (candidates for removal):
- "[instruction text]" — no lesson grounds this rule
```

---

## Phase 3 — Structural Quality Audit

### copilot-instructions.md

- [ ] Checklist gates are specific (each item is binary pass/fail, not a judgment call)
- [ ] Session start, pre-implementation, pre-completion gates are all present
- [ ] Domain constraints are explicitly listed (auth, DB scoping, logging, styling, etc.)
- [ ] No contradiction between rules
- [ ] Token budget reasonable: if > 3000 words, identify what can move to a skill file
- [ ] Skills table lists all `.github/skills/*/SKILL.md` files that actually exist

### Skill files

- [ ] Each SKILL.md has keyword-rich `description` in frontmatter (drives auto-trigger)
- [ ] First paragraph says exactly when to use the skill
- [ ] Contains facts, not just advice (specific paths, function names, code examples)
- [ ] No content that duplicates `copilot-instructions.md`
- [ ] Common pitfalls and gotchas are present (the product of lessons)

### Agent files

- [ ] `description` field is keyword-rich
- [ ] Role is stated clearly in the first paragraph
- [ ] Explicit list of what the agent does NOT do
- [ ] Contains the project architecture quick reference (for domain-specific agents)

### Prompt files

- [ ] `description` is keyword-rich for auto-triggering
- [ ] Each phase has clear outputs/deliverables
- [ ] `{{CHECK_COMMAND}}` and any other project-specific placeholders are filled in

---

## Phase 4 — Produce Improvement Plan

Ordered by impact:

```
## Priority 1 — High Impact
1. [Missing gate or rule that would have prevented a recent incident]

## Priority 2 — Medium Impact  
2. [Outdated rule that no longer matches code reality]

## Priority 3 — Low Impact
3. [Redundancies to remove]
```

---

## Phase 5 — Apply Fixes

For each improvement in the plan:
1. Read the file being modified
2. Make the targeted change (add, remove, or update rule)
3. Confirm the change doesn't contradict other rules in scope

After all fixes: re-read `copilot-instructions.md` end-to-end to confirm consistency.
