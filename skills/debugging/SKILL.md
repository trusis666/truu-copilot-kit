---
name: debugging
description: 'Debugging workflow and debug script conventions. Use when diagnosing failures, tracing data flow, writing diagnostic scripts, or understanding why a feature is not behaving as expected. Trigger keywords: debug, trace, failure, broken, not working, investigate, diagnose.'
---

# Debugging

Use this skill when something is broken and you need a systematic approach to find the root cause.

---

## Debugging Workflow

### 1. Reproduce reliably first

Before investigating, confirm you can reproduce the failure consistently:
- What exact input triggers the bug?
- Does it fail every time, or intermittently?
- Which environment (local, staging, production)?

Intermittent failures need a different approach than consistent ones.

### 2. Isolate the layer

Systems have layers. Find which one is broken before reading code:

```
Input → Validation → Service → External API → DB → Output
```

For each boundary, ask: "Is the data correct going INTO this layer?"  
Binary search the layers — don't start at the bottom.

### 3. Read the actual error

Read the full error message and stack trace before assuming what's wrong.  
The first line of the stack trace is usually the symptom. Look for the first line pointing to YOUR code, not library internals — that's usually the cause.

### 4. Check what changed

If something worked yesterday and is broken today:
```bash
git log --oneline --since="24 hours ago"
git diff HEAD~1 HEAD -- <affected-file>
```

### 5. Add targeted logging, don't scatter it

Add one log statement at the exact boundary you want to inspect. Remove it after.

```typescript
// Add at the boundary you're inspecting
console.error('[DEBUG]', { input, result, error }); // remove after debugging
```

Never leave debug logs in committed code.

---

## Debug Script Conventions

For recurring investigation needs, store reusable debug scripts in `.github/skills/debugging/scripts/`.

**Structure for a debug script:**

```javascript
// .github/skills/debugging/scripts/inspect-<thing>.mjs
// Purpose: [One sentence — what this script inspects]
// Usage: node --env-file=.env .github/skills/debugging/scripts/inspect-<thing>.mjs <ARG>
// Needs: [List env vars required]

const arg = process.argv[2];
if (!arg) {
  console.error('Usage: inspect-<thing>.mjs <id>');
  process.exit(1);
}

// ... query logic ...
```

**Rules for debug scripts:**
- Accept arguments, not hardcoded IDs
- Print structured output (JSON or ASCII table), not prose
- Include `--help` or a usage comment at the top
- **Read-only always.** Scripts that write data should be named `fix-*.mjs` and require explicit confirmation
- Run from repo root with `node --env-file=.env .github/skills/debugging/scripts/<script>.mjs <args>`

---

## Common Failure Patterns

### "It works locally but not in CI/production"

1. Environment variable missing → check the CI secret store vs. `.env.example`
2. Path aliasing works in TS but not at runtime → check build output resolves aliases
3. Clock-dependent code differs by timezone → use UTC everywhere
4. Race condition hidden by local speed → add explicit waits only if there's a real async boundary

### "The test passes but the feature is broken"

Test is mocking too aggressively. The mock diverged from the real implementation.
Check: does the mock accurately reflect what the real dependency returns?

### "Everything was fine, then a dependency update broke it"

```bash
git log --oneline --diff-filter=M -- package-lock.json | head -5
git diff HEAD~1 HEAD -- package-lock.json | grep "^[+-].*\"version\""
```

Binary search the upgrade: revert half the packages, confirm broken/working, narrow down.

### "The LLM is returning unexpected output"

1. Log the exact prompt sent (before templating, not the template)
2. Log the exact response received
3. Check: is user-supplied text in the prompt without delimiters? → prompt injection
4. Check: is there data in the output that wasn't in the input? → hallucination
5. Try the same prompt in the API playground to isolate model vs. code issues

---

## Reading Stack Traces

```
Error: Cannot read properties of undefined (reading 'id')
    at getUserById (lib/users/user.service.ts:42:22)   ← YOUR code — start here
    at async GET (app/api/users/route.ts:15:18)
    at async NextResponse (node_modules/next/...)       ← library internals — skip
```

The first line in YOUR code (not `node_modules/`) is the bug site. Go there first.

---

## Diagnosis Hints Checklist

When investigating a failure, run through this list:

- [ ] Is the error message read in full? (Not just the first line)
- [ ] Is the input to the failing function logged and confirmed correct?
- [ ] Is the relevant code actually being reached? (Add a log at the entry point)
- [ ] Is the relevant env var set in this environment?
- [ ] Is the issue reproducible with the same input every time?
- [ ] Did anything change recently that could cause this? (`git log --since="24h"`)
- [ ] Is the bug in your code, or is a dependency behaving differently than expected?
- [ ] Is the production DB schema in sync with the ORM models?
