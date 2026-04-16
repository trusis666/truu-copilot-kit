---
name: security-review
description: 'Full security review of a code diff or specific files — checks OWASP Top 10, auth invariants, data leak, prompt injection, SSRF, and secrets exposure. Use before merging any change that touches API routes, tools, agent prompts, or DB queries.'
argument-hint: 'Optional: specific files or concern to focus on (e.g. "all API routes", "the tool changes", "prompt injection risk"). Leave blank to review the current diff.'
---

# Security Review

Adversarial review of code changes for real exploitable vulnerabilities. Any Critical or High finding blocks the commit.

---

## Phase 1 — Gather the Diff

If specific files were provided, read those files directly.

Otherwise:
1. Run `git diff --stat` — get the list of changed files
2. Run `git diff` — get the full diff
3. Note which layers were touched (API routes, tools, agents, DB queries, auth, prompts)

---

## Phase 2 — Targeted Analysis by Layer

Run the checks relevant to what changed.

### API Routes

For every new or modified route handler:
- [ ] **Auth**: identity check is the FIRST statement — no code runs before auth
- [ ] **Tenant scoping**: tenant/project ID comes from the auth result, NOT from user-controlled request params without re-verification
- [ ] **Membership**: if the route accesses a resource owned by a tenant, verify the authed user is a member of THAT tenant
- [ ] **Input validation**: request body parsed through a schema at the handler boundary
- [ ] **Output safety**: response doesn't include internal-only fields
- [ ] **Error responses**: catch blocks return generic messages to the client — no stack traces or file paths

### Tools / External Integrations

For every new or modified integration:
- [ ] **SSRF**: does it call `fetch()` with a URL from user input, tool args, or agent context?
  - If yes: is the URL validated against an IP blocklist (localhost, 10.*, 172.16-31.*, 192.168.*, 169.254.*)?
- [ ] **Input sanitization**: are arguments validated before use? String inputs going into external API calls?
- [ ] **Side effects**: if this tool has irreversible side effects (sends email, publishes content, creates charges), is it behind an approval gate?

### Agent Prompts & AI Context

For every new or modified prompt-building function:
- [ ] **Prompt injection**: does user-controlled text land directly in the prompt without delimiters?
- [ ] **Data leak via prompt**: does the prompt include sensitive fields (tokens, keys, private emails) that could be echoed in output?
- [ ] **Output validation**: if LLM output is parsed and written to DB, is the schema validated before writing?

### Database Queries

For every new or modified query:
- [ ] **Tenant scoping**: every query includes `{ where: { tenantId } }` (or equivalent) — never a bare `{ where: { id } }`
- [ ] **No raw SQL with user values**: parameterised queries only — no string interpolation into raw query calls
- [ ] **Select projection**: only select columns that the caller needs — no `select: *` when returning to client

### Secrets & Config

- [ ] No API keys, tokens, or passwords hardcoded in source files
- [ ] `.env*` files are in `.gitignore`
- [ ] New environment variables are documented (`.env.example` or similar)

---

## Phase 3 — Findings Report

For each finding:

```
[SEVERITY] [File:Line] — [Vulnerability]
Attack path: [How an attacker exploits this]
Impact: [What they gain]
Fix: [Exact change needed]
```

Severity levels:
- **Critical** — direct exploitability: auth bypass, RCE, data exfiltration
- **High** — significant risk: cross-tenant data leak, stored XSS, SSRF to internal services
- **Medium** — needs fixing: missing validation, overly broad queries, sensitive data in logs
- **Low** — defense in depth: hardening, better error messages, documentation

---

## Phase 4 — Sign-off

Critical/High findings: **block commit** — list exact fixes required.  
Medium/Low findings: report but don't block (user decision).

```
[BLOCKED] — N critical/high findings. Fix before committing:
  1. [Finding]
  
[APPROVED] — N findings (M medium, L low). Safe to commit.
```
