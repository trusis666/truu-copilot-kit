---
name: review
description: Code review before commit — checks the current diff for bugs, security issues, missing edge cases, test coverage gaps, and project convention violations. Run between /implement and /commit.
argument-hint: 'Optional: specific area to focus on (e.g. "security", "performance", "just the service file")'
---

# Code Review

Review the current diff for quality, security, and correctness before committing.

---

## 1. Gather Context

- Run `git diff --stat` for an overview of changed files
- Run `git diff` for the full diff (staged + unstaged)
- Read `tasks/todo.md` Active This Session to understand the intent of the changes
- If a specific focus area was provided as an argument, prioritise that

---

## 2. Review Checklist

Score each axis **Pass / Warn / Fail**. Any **Fail** must be fixed before `/commit`.

### Correctness

- [ ] All new code paths have matching test coverage
- [ ] Edge cases handled: empty arrays, null/undefined, zero values, boundary conditions
- [ ] Error handling: are errors caught at the right level? Do they propagate useful messages?
- [ ] Async: no fire-and-forget promises that should be awaited; no unhandled rejections
- [ ] Types: no unsafe `as any` casts that could hide real bugs (test files excluded)

### Security (OWASP-aware)

- [ ] No secrets, API keys, or credentials in the diff (check `.env` files are gitignored)
- [ ] Input validation at system boundaries (API routes, user input)
- [ ] No SQL injection via raw queries (ORM parameterises by default — flag raw SQL)
- [ ] No XSS vectors in user-facing output
- [ ] Auth: new API routes check identity before accessing data
- [ ] No SSRF: URLs from user/agent context are validated before fetch

### Performance

- [ ] No N+1 queries (look for DB calls inside loops — use batching or includes)
- [ ] No unbounded loops or uncapped array operations on user data
- [ ] No unnecessary re-renders (React: deps arrays correct, no object/function literals in JSX props)
- [ ] No fetching more columns than needed from the DB

### Project Conventions

- [ ] Styling follows project conventions (see `.github/skills/code-standards/SKILL.md`)
- [ ] No `console.log` in committed code — use the project's logger
- [ ] File naming follows project suffix rules
- [ ] Component structure follows project's component conventions
- [ ] New public APIs are documented (comment or type-level)

### Tests

- [ ] Every new file has a co-located `.test.ts`
- [ ] Tests test behavior, not implementation (no direct mocking of private functions)
- [ ] Mocks accurately reflect what the real dependency does
- [ ] No `test.skip`, `it.skip`, or disabled test blocks in the diff

---

## 3. Findings Report

For each finding, report:

```
[LEVEL] [File:Line] — [Issue]
Impact: [What breaks or gets exposed]
Fix: [Specific change to make]
```

Levels:
- **Critical** — blocks commit, security vulnerability or correctness break
- **High** — blocks commit, significant bug or design flaw
- **Medium** — should fix before commit, but won't break on day 1
- **Low** — suggestion, stylistic, or future concern

---

## 4. Sign-off

If there are no Critical or High findings:

```
✅ Review passed — [N] findings ([X] medium, [Y] low)
Ready for /commit
```

If there are Critical or High findings:

```
❌ Review blocked — N critical/high findings
Fix before committing:
1. [Finding]
2. [Finding]
```
