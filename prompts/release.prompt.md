---
name: release
description: Pre-deploy release checklist — gather changes since last release, check for breaking changes, generate release notes, create git tag. Run before deploying to production.
argument-hint: 'Optional: version bump type ("patch", "minor", "major") or "dry-run" to preview without tagging'
---

# Release Checklist

Verify, document, and tag a release before deploying.

---

## 1. Gather Changes

- Find the last release tag: `git describe --tags --abbrev=0` (or `git tag -l` if no tags yet)
- List all commits since then: `git log <last-tag>..HEAD --oneline`
- Read `CHANGELOG.md` entries since the last release

## 2. Categorise Changes

```
## Release: v[X.Y.Z]

### Breaking Changes
[Schema migrations, env var changes, API contract changes, removed endpoints]

### Features
[New functionality]

### Fixes
[Bugs resolved]

### Internal
[Refactors, test improvements, DX changes]
```

## 3. Security Gate

Run `/security-review` on the full diff since the last release tag:

```bash
git diff <last-tag>..HEAD --stat
```

**Hard gate:** Any Critical finding blocks the release. Resolve it, then restart from Step 1.

High findings must be either fixed or explicitly accepted with rationale in the release note.

## 4. Pre-Deploy Checks

All must pass before tagging:

- [ ] `{{CHECK_COMMAND}}` — green (build + tests + lint)
- [ ] Production build succeeds
- [ ] No uncommitted changes: `git status` is clean
- [ ] DB schema changes: confirm migrations have been run (or are queued to run before code deploys)
- [ ] New env vars: documented and set in production environment
- [ ] Broken dependencies: `npm audit` shows no critical issues

## 5. Determine Version Bump

Use semantic versioning (semver.org):

| Change type | Bump |
|---|---|
| Bug fix, internal change, no API change | patch (0.0.X) |
| New feature, backward-compatible | minor (0.X.0) |
| Breaking change (removed endpoint, changed interface, migration required) | major (X.0.0) |

## 6. Tag and Push

```bash
git tag -a v[X.Y.Z] -m "Release v[X.Y.Z]: [one-line summary]"
git push origin v[X.Y.Z]
```

## 7. Release Notes

Post to wherever your team tracks releases (GitHub Releases, Slack, etc.):

```
## v[X.Y.Z] — [date]

[1-2 sentence summary]

### What changed
[Key changes from Step 2]

### How to upgrade (if any breaking changes)
[Migration steps]
```
