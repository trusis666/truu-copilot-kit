---
mode: agent
description: Set up truu-copilot-kit for this project. Explores the codebase, fills all templates, creates project-specific skills. Run once after adding the kit subtree.
---

You are setting up `truu-copilot-kit` for this project. Work autonomously through all phases. Do not ask for confirmation between steps unless a decision is genuinely ambiguous.

---

## Phase 1 — Explore the codebase

Before touching any file, understand the project:

1. Read `README.md` (or the root doc) to get the project name, purpose, and tech stack
2. List the root directory structure — identify framework (Next.js / Express / Rails / etc.), test runner, build tool, package manager
3. Find the check/test/lint command: look for `package.json` scripts (`check`, `test`, `lint`, `ci`) or `Makefile` targets
4. Identify auth pattern: grep for `requireAuth`, `middleware`, `authenticate`, `session`
5. Identify DB pattern: grep for `prisma`, `mongoose`, `drizzle`, `knex`, `sequelize`, `pg`
6. Identify logging pattern: grep for `pino`, `winston`, `logger`, `console.log` usage in lib/
7. Identify CSS approach: grep for `module.css`, `tailwind`, `styled-components`, `emotion`
8. List top-level source directories and describe each (e.g. `src/`, `app/`, `lib/`, `packages/`)
9. Check what existing `.github/skills/` files are present and what they cover

Record your findings — you will use them in every template fill.

---

## Phase 2 — Bootstrap .gitattributes

1. Check if `.gitattributes` exists in the repo root
2. If not: `cp .github/kit-templates/gitattributes .gitattributes`
3. Review `.gitattributes`. Uncomment protection (`merge=ours`) only for skills that you are about to enrich with project-specific content in Phase 4. Leave generic skills unprotected until you actually add project content to them.
4. Register the merge driver (run once, silently fails if already set):
   ```bash
   git config --global merge.ours.driver true
   ```

---

## Phase 3 — Fill copilot-instructions.md

1. `cp .github/kit-templates/copilot-instructions.md .github/copilot-instructions.md`
2. Fill every `{{PLACEHOLDER}}` using your Phase 1 findings:

| Placeholder | Source |
|---|---|
| `{{PROJECT_NAME}}` | README / package.json name |
| `{{PROJECT_DESCRIPTION}}` | README first paragraph |
| `{{CHECK_COMMAND}}` | package.json `check` or `test` + `lint` scripts |
| `{{TECH_STACK}}` | Framework + DB + test runner (e.g. "Next.js 16, Prisma, TypeScript, Vitest") |
| `{{REPO_ROOT_STRUCTURE}}` | Annotated directory tree (2-3 levels) |
| `{{DOMAIN_CONSTRAINTS}}` | Non-negotiable rules you found: projectId scoping, LLM wrapper, auth pattern, etc. |
| `{{AUTH_PATTERN}}` | Exact import + function call from codebase |
| `{{DB_IMPORT_PATTERN}}` | Exact import from codebase |
| `{{DB_SCOPE_PATTERN}}` | How queries are scoped (tenantId, userId, projectId, etc.) |
| `{{LOGGER_PATTERN}}` | Exact logger import + usage |
| `{{STYLE_APPROACH}}` | CSS Modules / Tailwind / etc. with exact usage example |

If a placeholder has no answer (feature doesn't exist in this project), remove the line — don't leave `{{...}}` in the file.

Verify: `grep -r "{{" .github/copilot-instructions.md` must return nothing.

---

## Phase 4 — Skills: update or create

For each domain the project has, decide:

**Option A — Project uses a generic kit skill as-is**: leave it, no action needed. It will update from kit pulls.

**Option B — Project has specific patterns that enrich a generic skill**: edit the existing kit skill file to add a project-specific section at the bottom. Then uncomment that skill in `.gitattributes` so kit pulls won't overwrite it.

**Option C — Project has a domain that the kit has no skill for** (e.g. `auth`, `database`, `ui`, `api`, `payments`): create a new skill file:
```bash
mkdir -p .github/skills/<domain>
# create .github/skills/<domain>/SKILL.md
```
New skill files are yours entirely — kit never touches them, no `.gitattributes` entry needed.

**Minimum skills every project should have:**
- `project/SKILL.md` — tech stack, commands, folder layout, key conventions (from `kit-templates/skills/project/SKILL.md`)
- `code-standards/SKILL.md` — file naming, import rules, component structure (from `kit-templates/skills/code-standards/SKILL.md`)

Fill both from templates, using your Phase 1 findings.

**Skill quality bar**: each skill file must answer "what does a senior engineer need to know before touching this area?" Specific enough to prevent common mistakes. Not a tutorial — a reference.

---

## Phase 5 — Dev agent

1. `cp .github/kit-templates/agents/dev.agent.md .github/agents/<project-name>-dev.agent.md`
   - Replace `<project-name>` with the project's short slug (lowercase, no spaces)
2. Fill every `{{PLACEHOLDER}}` with project-specific content from Phase 1
3. The dev agent description should call out the project name and tech stack so Copilot picks the right agent automatically (e.g. "Use when coding in the `truu-invoices` Next.js app")

---

## Phase 6 — Task tracking

```bash
mkdir -p tasks
cp .github/kit-templates/tasks/todo.md tasks/todo.md
cp .github/kit-templates/tasks/lessons.md tasks/lessons.md
cp .github/kit-templates/tasks/lessons-quick-ref.md tasks/lessons-quick-ref.md
```

Fill `todo.md` header with project name. Leave `lessons.md` and `lessons-quick-ref.md` mostly blank — they fill over time.

---

## Phase 7 — Final verification

1. `grep -r "{{" .github/ tasks/ --include="*.md"` — must return nothing (all placeholders filled)
2. Check that `.github/copilot-instructions.md` exists and has real content (not template boilerplate)
3. Check that `tasks/todo.md` exists
4. Check that at least one project-specific skill exists (`project/` or `code-standards/`)
5. Check that a dev agent exists in `.github/agents/`

---

## Phase 8 — Commit

```bash
git add .github/ tasks/ .gitattributes
git commit -m "chore: set up truu-copilot-kit — skills, instructions, agent"
```

Report: list every file created or modified, and flag any placeholder that could not be filled automatically.
