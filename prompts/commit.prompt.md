---
name: commit
description: Systematic workflow for verifying, documenting, committing, and pushing changes. Run whenever committing code — ensures pre-commit checks, changelog updates, and proper documentation.
argument-hint: 'Provide a summary of the changes being committed'
---

# Commit Changes Workflow

Verify, document, commit, and push all changes to the repository.

## 1. Check Git Status

- Run `git status --short` to see modified/new files
- Run `git diff --stat` for a quick overview, then `git diff` for details
- Identify all files that need to be committed

## 2. Pre-Commit Verification

**Do NOT skip** — these must pass before anything is committed:

1. **Full check**: Run `{{CHECK_COMMAND}}` — must be fully green. Any failure is a real regression to fix before committing.
2. **No console debris**: Ensure no leftover debug `console.log` statements in committed code
3. **Clean up one-time scripts**: If any temp fix scripts were created this session and have been run, delete them before committing. Keep scripts only if they're genuinely reusable.
4. **Lessons synced**: Check `tasks/lessons.md` — if anything was learned this session, record it before committing
5. **Todo synced**: Check `tasks/todo.md` — update with current state if stale

## 3. Update Changelog

Update `CHANGELOG.md` (root of repo):

- Read the current changelog to understand format and latest entry
- Add a new `## [YYYY-MM-DD]` section (with letter suffix if multiple entries today, e.g. `[2026-02-23b]`)
- Use proper categories: `### Added`, `### Changed`, `### Fixed`, `### Removed`
- Include: what changed and why, file paths affected, breaking changes or migration notes

## 4. Update Project Documentation

**If changes impact project structure or workflows**, update:

- `.github/copilot-instructions.md` — if new conventions or constraints were established
- Relevant `.github/skills/<domain>/SKILL.md` — if a domain behavior changed
- `docs/` system or guide page — if one covers this area

## 5. Stage and Commit

```bash
git add -A
git status  # verify what's being committed
git commit -m "<type>: <concise description>

<body if needed — what and why, not how>

<BREAKING CHANGE: description if applicable>"
```

Commit type prefixes: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `perf`

## 6. Push

```bash
git push origin <branch>
```

If pushing to `main` directly: confirm this is intentional.

---

## Commit Message Quality Gate

A good commit message answers: "If applied, this commit will **[message]**"

- `feat: add Stripe webhook handler for subscription events` ✅
- `fix: scope DB query to projectId to prevent cross-tenant leak` ✅
- `stuff` ❌
- `WIP` ❌ (never commit WIP — stash it)
