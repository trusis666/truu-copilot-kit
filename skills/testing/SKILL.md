---
name: testing
description: 'Writing and maintaining tests — co-location conventions, mock patterns, test organisation, coverage strategy, and LLM prompt evaluation. Use when writing tests, fixing test failures, or setting up the test infrastructure for a new module.'
---

# Testing

Use this skill when writing tests, debugging test failures, setting up mocks, or evaluating prompt quality.

---

## Core Conventions

### Co-location

Every source file gets a test file next to it — same directory, same name with `.test.ts` suffix:

```
lib/payments/stripe.service.ts
lib/payments/stripe.service.test.ts   ← co-located, always
```

No `__tests__/` directories for unit tests. Keep tests next to the code they test.

Exception: integration tests, e2e tests, and LLM eval suites may live in dedicated directories.

### Test file naming

| File type | Suffix | Notes |
|---|---|---|
| Unit / integration test | `.test.ts` | Run in CI, fast, no real network |
| LLM prompt eval | `.eval.ts` | Slow, makes real LLM calls, not in CI |
| E2E test | `.spec.ts` | Playwright or similar |

### What gets a test

| Code type | Test type | Mock strategy |
|---|---|---|
| Pure functions | Unit test | No mocks needed |
| Service (DB + external calls) | Integration test | Mock DB client + HTTP |
| API route handler | Handler test | Mock auth + DB |
| React component | Component test | Mock hooks + data |
| Agent / LLM logic | Capability test | Mock `callLLM()` + tools |

**Every new module gets a co-located test file. No exceptions.**

---

## Mock Patterns

### Mock at the module boundary, not inside functions

```typescript
// BAD — mocking at implementation level couples test to internals
vi.spyOn(prisma.user, 'findFirst').mockResolvedValue(mockUser);

// GOOD — mock the whole module where it's imported
vi.mock('@/lib/db', () => ({ prisma: createMockDb() }));
```

### Factory functions for complex mocks

Create reusable factories instead of inline `{}` objects:

```typescript
// tests/mocks/db.ts
export function createMockDb() {
  return {
    user: {
      findFirst: vi.fn(),
      create: vi.fn(),
      update: vi.fn(),
    },
    // ...
  };
}
```

Then in the test:

```typescript
const mockDb = createMockDb();
vi.mock('@/lib/prisma', () => ({ prisma: mockDb }));

beforeEach(() => {
  vi.clearAllMocks();
  mockDb.user.findFirst.mockResolvedValue(null); // reset to safe default
});
```

### Mock reset strategy

- Use `vi.clearAllMocks()` in `beforeEach` — resets call counts and implementations
- OR configure `clearMocks: true` in `vitest.config.ts` to auto-reset between tests
- `vi.restoreAllMocks()` is for `vi.spyOn` (restores original implementation); use in `afterEach` if you spy

**Gotcha:** `vi.resetAllMocks()` removes the mock implementation too — use `clearAllMocks()` unless you explicitly want to reset implementations.

---

## What to Test per Layer

### Pure functions
Test the contract: inputs → outputs. Cover boundary conditions, null, zero, empty array.

```typescript
it('returns empty array when input is empty', () => {
  expect(processItems([])).toEqual([]);
});
it('throws on invalid input', () => {
  expect(() => processItems(null as never)).toThrow('Expected array');
});
```

### Service functions (DB + external)

Test 3 categories: happy path, external failure, invalid input.

```typescript
it('creates user and returns record', async () => {
  mockDb.user.create.mockResolvedValue(mockUser);
  const result = await createUser({ email: 'a@b.com' });
  expect(result.id).toBe(mockUser.id);
  expect(mockDb.user.create).toHaveBeenCalledWith({ data: { email: 'a@b.com' } });
});
it('throws when DB is unavailable', async () => {
  mockDb.user.create.mockRejectedValue(new Error('Connection refused'));
  await expect(createUser({ email: 'a@b.com' })).rejects.toThrow('Connection refused');
});
```

### API route handlers

Test 3 categories: success, auth failure, bad input.

```typescript
it('returns 401 when unauthenticated', async () => {
  mockAuth.mockResolvedValue(null);
  const res = await GET(new Request('http://localhost/api/items'));
  expect(res.status).toBe(401);
});
it('returns items for authenticated user', async () => {
  mockAuth.mockResolvedValue({ userId: 'u1' });
  mockDb.item.findMany.mockResolvedValue([mockItem]);
  const res = await GET(new Request('http://localhost/api/items'));
  expect(res.status).toBe(200);
});
```

---

## LLM Prompt Evaluation (Evals)

Evals are separate from unit tests — they make real LLM calls and measure output quality.

### When to use evals
- Testing prompt correctness after changing prompt templates
- Regression-testing output format (sections present, no hallucination)
- NOT for fast CI — run separately (e.g. `npm run eval`)

### Deterministic assertion types (no scoring LLM needed)

| Type | Use case |
|---|---|
| `contains` | Required section or phrase must appear |
| `not-contains` | Prohibited phrase must not appear |
| `regex` | Format validation (dates, IDs, JSON shape) |
| `javascript` | Custom logic: `output.split('\n').length > 3` |

### What makes a good eval

- Tests ONE specific behavior per test case
- Uses deterministic assertions (no "score ≥ 7" — too subjective for CI)
- Runs against the production LLM call path, not a simplified mock
- Documents what failure looks like (so regressions are obvious)

---

## Coverage Strategy

Set thresholds that protect critical paths without blocking velocity:

- **Critical business logic** (payment processing, auth, data mutation): 80%+
- **Service layer** (DB + external calls): 70%+
- **API routes**: 60%+
- **UI components**: snapshot tests acceptable for stable components

Coverage is a floor, not a goal. 100% coverage of badly written tests = false confidence.

---

## Common Gotchas

| Gotcha | Symptom | Fix |
|---|---|---|
| `vi.mock()` hoisting | Mock defined inside `it()` doesn't work | Move `vi.mock()` to module top level |
| Async not awaited | Test passes but doesn't actually test async behavior | Ensure every `async` call in the test is `await`-ed |
| `clearAllMocks` vs `resetAllMocks` | Mock no longer returns the right value after reset | Use `clearAllMocks()` to preserve implementation; `resetAllMocks()` strips it |
| Date/time-dependent tests | Flaky in CI due to timezone/clock differences | Use `vi.setSystemTime()` to fix the clock |
| Module path aliasing | Imports that work in app fail in tests | Ensure `vitest.config.ts` resolver aliases match `tsconfig.json` paths |
