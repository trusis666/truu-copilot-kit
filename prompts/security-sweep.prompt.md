---
name: security-sweep
description: 'Full codebase OWASP security sweep — systematic scan of all API routes, tools, DB queries, and agent prompts for security vulnerabilities. Run monthly and before major releases.'
---

# Security Sweep

Systematic full-codebase security scan. More thorough than `/security-review` (which covers a diff). This covers the entire codebase.

---

## Phase 1 — Scope

List all areas to cover in this sweep:

- [ ] API route handlers
- [ ] DB query call sites
- [ ] External integrations / tool implementations
- [ ] Agent prompt-building functions
- [ ] Auth middleware and session handling
- [ ] Environment variable usage
- [ ] Dependency audit

---

## Phase 2 — API Routes

Find all route handlers:
```bash
find . -path "*/api/*/route.ts" -not -path "*/node_modules/*"
```

For each handler:
- Does it call the project's auth check before accessing data?
- Does every DB query include tenant/user scoping?
- Is input validated at the boundary (schema parsing)?
- Are error responses generic (no internal paths or stack traces)?

**Produce a table:**

| Route | Auth ✓ | Scoped ✓ | Validated ✓ | Notes |
|---|---|---|---|---|
| `/api/...` | ✅ | ✅ | ❌ | [gap] |

---

## Phase 3 — Database Queries

Search for raw query usage:
```bash
grep -r "\$queryRaw\|\$executeRaw" --include="*.ts" .
```

For each raw query: is it using parameterized values (not string interpolation)?

Search for broad queries:
```bash
grep -r "findMany\|findFirst" --include="*.ts" . | grep -v "test\|spec"
```

Sample 10 queries — do they all include tenant/user scope?

---

## Phase 4 — Tools and External Integrations

Find all `fetch()` calls:
```bash
grep -rn "fetch(" --include="*.ts" . | grep -v "node_modules\|test\|spec"
```

For each: is the URL from user/agent input? If yes, is it validated against the SSRF blocklist?

---

## Phase 5 — Prompt Injection Surface

Find all LLM prompt-building functions:
```bash
grep -rn "buildPrompt\|systemPrompt\|userPrompt\|\`.*\${" --include="*.ts" . | grep -v "test\|node_modules"
```

For each interpolation of user-controlled content: is it wrapped in delimiters?

---

## Phase 6 — Dependencies

```bash
npm audit --audit-level=moderate
```

List all Critical and High findings. Flag packages not updated in > 12 months that are in the critical path.

---

## Phase 7 — Secrets Scan

```bash
grep -rn "sk-\|Bearer \|password.*=.*['\"].\{8,\}\|api_key\|apiKey.*=.*['\"]" --include="*.ts" . | grep -v "node_modules\|test\|\.example"
```

Any matches that aren't env var reads are a Critical finding.

---

## Phase 8 — Report

```
## Security Sweep — [date]

### Critical (fix immediately)
- [Finding: file, line, description]

### High (fix before next release)
- [Finding]

### Medium (fix this sprint)
- [Finding]

### Low (track in backlog)
- [Finding]

### Coverage
- Routes audited: N/N
- Queries sampled: N
- Prompts reviewed: N
- Dependencies: [X critical, Y high from npm audit]
```

Add all High+ findings to `tasks/todo.md` as bugs immediately.
