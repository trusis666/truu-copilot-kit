---
name: code-standards
description: 'Enforcing {{PROJECT_NAME}} file structure, naming conventions, import hierarchy, and component organisation rules. Use when creating new files, moving files, organising directories, or checking whether a structure is compliant with project standards.'
metadata:
  author: '{{PROJECT_NAME}}'
  version: '1.0'
---

# Code Standards

Use this skill when creating new files, refactoring directories, deciding where to put a new component or service, or reviewing whether an existing structure is compliant.

---

## File Naming Rules

<!-- Document your project's file naming conventions. Example:

Every file in `src/lib/` must use the suffix that announces its intent:

| Suffix | Contents | Test required? |
|---|---|---|
| `.service.ts` | Business logic (DB + external calls) | ✅ Yes |
| `.util.ts` | Pure utility functions | ✅ Yes |
| `.types.ts` | Type definitions only | ❌ No (no runtime code) |
| `.test.ts` | Unit test | — |
| `index.ts` | Barrel re-exports only | ❌ No (no logic) |

No `utils.ts`, `helpers.ts`, or bare `foo.ts` in `lib/`.
-->

---

## Component Structure

<!-- Document your component organisation. Example:

Every React component lives in its own named folder:

```
components/<level>/<ComponentName>/
├── index.ts                    # export { X } from './X'
├── <ComponentName>.tsx         # presentational — named export
├── <ComponentName>.module.css  # co-located styles
└── <ComponentName>.test.tsx    # mandatory test
```
-->

---

## Import Rules

<!-- Document allowed import paths. Example:

- DB via `@/lib/db` — never `new PrismaClient()` directly
- External API clients via service modules — never inline in route handlers
- Components via `@/components/` absolute imports — no deep relative paths across features
-->

---

## Test Co-location

Every source file gets a test file next to it:

```
src/lib/payments/stripe.service.ts
src/lib/payments/stripe.service.test.ts   ← co-located, always
```

No `__tests__/` directories. Tests live next to the code they test.

---

## What Gets Tested

| Code type | Test requirement |
|---|---|
| Pure functions | Unit test — no mocks needed |
| Service functions | Integration test — mock external deps |
| API route handlers | Handler test — mock auth + DB |
| React components | Component test — mock data hooks |

Every new module gets a co-located `.test.ts`. No exceptions.

---

## Gotchas

<!-- Document your project's common structure mistakes here.
These should be distilled from tasks/lessons.md via /distill-lessons.
Format: **Rule.** Rationale. Location.

Example:
- **Never import directly from `lib/db.ts` in route handlers.** Use the service layer. Direct DB access in route handlers prevents mocking in tests. _(Lesson 3)_
-->
