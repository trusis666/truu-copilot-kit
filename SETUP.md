# Setup Guide

How to wire `truu-copilot-kit` into any project via `git subtree`, and how to contribute improvements back.

---

## Two repo roles

| Role | Who | Subtree direction |
|---|---|---|
| **Source** | founder-os | Pushes improvements TO kit |
| **Consumer** | every other project | Pulls updates FROM kit |

**Founder-os is the source.** It has enriched versions of the generic skills and does NOT pull from the kit — it pushes to it. `git subtree pull` in founder-os is deliberately avoided.

---

## New project: pull from kit

### 1. Add the subtree

```bash
git remote add truu-kit https://github.com/trusis/truu-copilot-kit
git subtree add --prefix=.github truu-kit main --squash
```

### 2. Register the ours merge driver (one-time, per machine)

```bash
git config --global merge.ours.driver true
```

Required for `.gitattributes` protection to work on future pulls.

### 3. Add .gitattributes to protect enriched skills

```bash
cp .github/kit-templates/gitattributes .gitattributes
```

Edit it to protect any paths you've enriched with project-specific content.

### 4. Copy and fill in the project-specific templates

```bash
cp .github/kit-templates/copilot-instructions.md .github/copilot-instructions.md
mkdir -p .github/skills/project && cp .github/kit-templates/skills/project/SKILL.md .github/skills/project/SKILL.md
mkdir -p .github/skills/code-standards && cp .github/kit-templates/skills/code-standards/SKILL.md .github/skills/code-standards/SKILL.md
cp .github/kit-templates/agents/dev.agent.md .github/agents/<yourproject>-dev.agent.md
mkdir -p tasks && cp .github/kit-templates/tasks/* tasks/
```

### 5. Fill in all placeholders

```bash
grep -r "{{" .github/ tasks/ --include="*.md" -l
```

| Placeholder | Replace with |
|---|---|
| `{{PROJECT_NAME}}` | Your project name |
| `{{CHECK_COMMAND}}` | Build + test + lint command (e.g. `npm run check`) |
| `{{TECH_STACK}}` | Short description (e.g. "Next.js 15, Prisma, TypeScript, Vitest") |
| `{{REPO_ROOT_STRUCTURE}}` | Your folder layout |
| `{{DOMAIN_CONSTRAINTS}}` | Project-specific non-negotiable rules |
| `{{AUTH_PATTERN}}` | How API routes authenticate |
| `{{DB_IMPORT_PATTERN}}` | How to import the DB client |
| `{{DB_SCOPE_PATTERN}}` | How to scope queries (tenant/user) |
| `{{LOGGER_PATTERN}}` | Project logger |
| `{{STYLE_APPROACH}}` | CSS Modules / Tailwind / etc. |

### 6. Commit

```bash
git add .github/ tasks/ .gitattributes
git commit -m "chore: add truu-copilot-kit via subtree"
```

---

## Pulling kit updates (consumer projects)

```bash
git subtree pull --prefix=.github truu-kit main --squash
```

- Files in `.gitattributes` with `merge=ours` → always kept unchanged
- Prompts, generic agents, `copilot-recursive-template.md` → update from kit
- `copilot-instructions.md`, project skills, your dev agent → NOT in kit, never touched

---

## What the kit manages vs. what you own

| Path | Owned by |
|---|---|
| `.github/skills/architecture/` | Kit |
| `.github/skills/context-engineering/` | Kit |
| `.github/skills/debugging/` | Kit |
| `.github/skills/security/` | Kit |
| `.github/skills/testing/` | Kit |
| `.github/prompts/` (all 16) | Kit |
| `.github/agents/architect.agent.md` | Kit |
| `.github/agents/security-auditor.agent.md` | Kit |
| `.github/copilot-recursive-template.md` | Kit |
| `.github/kit-templates/` | Kit (reference templates) |
| `.github/copilot-instructions.md` | **You** |
| `.github/skills/project/` | **You** |
| `.github/skills/<domain-specific>/` | **You** |
| `.github/agents/<project>-dev.agent.md` | **You** |
| `tasks/` | **You** |

---

## Pushing improvements back to kit

When you improve a generic skill or prompt, contribute it back directly in the kit repo:

```bash
cd /path/to/truu-copilot-kit
# edit the file
git add -A && git commit -m "improve: <what changed>"
git push origin main
# then pull it into your project:
git subtree pull --prefix=.github truu-kit main --squash
```

**Do NOT use `git subtree push --prefix=.github truu-kit main` from a project repo** — it pushes EVERYTHING in `.github/` including project-specific files.
