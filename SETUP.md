# Setup Guide

How to wire `copilot-workflow-kit` into a new or existing repo via `git subtree`.

---

## Important: the "source" repo pattern

If your project is the **source** the kit was extracted from (i.e. you built the kit FROM this project), do NOT run `git subtree add` on it — you'd be pulling a diluted version back into a more complete one. Those repos should keep their current `.github/` as-is and simply maintain the kit by pushing improvements outward.

Use `git subtree add` only for **new projects** or **projects with a minimal starting `.github/`**.

---

## Initial Setup (first time in a new repo)

### 1. Add the subtree

```bash
# Run from your repo root
git subtree add --prefix=.github https://github.com/<you>/copilot-workflow-kit main --squash
```

This pulls all kit files into `.github/`. Your existing `.github/` files (if any) are merged.

### 2. Copy and fill in the project-specific templates

These files are in `kit-templates/` — they were pulled in by the subtree but are just starters.
Copy each one to its final location and replace all `{{PLACEHOLDER}}` values:

```bash
# Project-specific copilot instructions (MANDATORY)
cp .github/kit-templates/copilot-instructions.md .github/copilot-instructions.md

# Project skill — describes your stack, commands, conventions
mkdir -p .github/skills/project
cp .github/kit-templates/skills/project/SKILL.md .github/skills/project/SKILL.md

# Code standards skill — naming, structure, import rules for your project
mkdir -p .github/skills/code-standards
cp .github/kit-templates/skills/code-standards/SKILL.md .github/skills/code-standards/SKILL.md

# Dev agent — project-specific Copilot agent mode
cp .github/kit-templates/agents/dev.agent.md .github/agents/<yourproject>-dev.agent.md

# Task tracking files (lessons, todo, maintenance)
mkdir -p tasks
cp .github/kit-templates/tasks/lessons.md tasks/lessons.md
cp .github/kit-templates/tasks/lessons-quick-ref.md tasks/lessons-quick-ref.md
cp .github/kit-templates/tasks/todo.md tasks/todo.md
```

### 3. Fill in all placeholders

Run this to find everything that needs your attention:

```bash
grep -r "{{" .github/ tasks/ --include="*.md" -l
```

Replace:
| Placeholder | Replace with |
|---|---|
| `{{PROJECT_NAME}}` | Your project name |
| `{{CHECK_COMMAND}}` | Build + test + lint command (e.g. `npm run check`) |
| `{{TECH_STACK}}` | Short description (e.g. "Next.js 15, Prisma, TypeScript, Vitest") |
| `{{REPO_ROOT_STRUCTURE}}` | Your folder layout |
| `{{DOMAIN_CONSTRAINTS}}` | Project-specific non-negotiable rules |
| `{{AUTH_PATTERN}}` | How API routes authenticate |
| `{{DB_PATTERN}}` | How DB access works |
| `{{STYLE_PATTERN}}` | CSS/styling approach |

### 4. Commit the result

```bash
git add .github/ tasks/
git commit -m "chore: add copilot-workflow-kit via subtree"
```

---

## Updating an existing project

```bash
git subtree pull --prefix=.github https://github.com/<you>/copilot-workflow-kit main --squash
```

Resolve any merge conflicts (rare — only happens if you edited files that exist in the kit).

---

## What the kit manages vs. what you own

| Path | Owned by |
|---|---|
| `.github/skills/architecture/` | Kit — `git subtree pull` updates this |
| `.github/skills/context-engineering/` | Kit |
| `.github/skills/debugging/` | Kit |
| `.github/skills/security/` | Kit |
| `.github/skills/testing/` | Kit |
| `.github/prompts/` (all) | Kit |
| `.github/agents/architect.agent.md` | Kit |
| `.github/agents/security-auditor.agent.md` | Kit |
| `.github/copilot-recursive-template.md` | Kit |
| `.github/kit-templates/` | Kit (reference templates, safe to edit copies) |
| `.github/copilot-instructions.md` | **You** — project-specific |
| `.github/skills/project/` | **You** — project-specific |
| `.github/skills/code-standards/` | **You** — project-specific |
| `.github/skills/<any other>/` | **You** — project-specific |
| `.github/agents/<project>-dev.agent.md` | **You** — project-specific |
| `tasks/` | **You** — project-specific |

---

## Adding a remote alias (optional, easier pulls)

```bash
git remote add copilot-kit https://github.com/<you>/copilot-workflow-kit
# Then:
git subtree pull --prefix=.github copilot-kit main --squash
```
