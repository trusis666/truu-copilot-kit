# Kit Templates

These are **starting points** for project-specific files. They are NOT auto-applied by `git subtree pull`.

After running `git subtree add --prefix=.github <kit-repo>`, copy these templates to their final locations and fill in all `{{PLACEHOLDER}}` values:

```bash
# 1. Project-specific instructions (REQUIRED)
cp .github/kit-templates/copilot-instructions.md .github/copilot-instructions.md

# 2. Project skill (REQUIRED)
mkdir -p .github/skills/project
cp .github/kit-templates/skills/project/SKILL.md .github/skills/project/SKILL.md

# 3. Code standards skill (recommended)
mkdir -p .github/skills/code-standards
cp .github/kit-templates/skills/code-standards/SKILL.md .github/skills/code-standards/SKILL.md

# 4. Dev agent (REQUIRED for custom agent mode)
cp .github/kit-templates/agents/dev.agent.md .github/agents/{{PROJECT_NAME}}-dev.agent.md

# 5. Task tracking (REQUIRED for /standup, /next-task, /retro)
mkdir -p tasks
cp .github/kit-templates/tasks/lessons.md tasks/lessons.md
cp .github/kit-templates/tasks/lessons-quick-ref.md tasks/lessons-quick-ref.md
cp .github/kit-templates/tasks/todo.md tasks/todo.md
```

## Find all placeholders

After copying:

```bash
grep -r "{{" .github/ tasks/ --include="*.md" -l
```

## Placeholder reference

| Placeholder | Replace with |
|---|---|
| `{{PROJECT_NAME}}` | "My Project" |
| `{{CHECK_COMMAND}}` | `npm run check` or `./scripts/check.sh` etc. |
| `{{TECH_STACK}}` | e.g. "Next.js 15, Prisma, TypeScript, Vitest" |
| `{{REPO_ROOT_STRUCTURE}}` | Your folder layout (see project README) |
| `{{DOMAIN_CONSTRAINTS}}` | Non-negotiable rules specific to your stack |
| `{{AUTH_PATTERN}}` | How API routes authenticate (e.g. `requireAuth(req)`) |
| `{{DB_IMPORT_PATTERN}}` | How to import the DB client |
| `{{DB_SCOPE_PATTERN}}` | How to scope queries to a tenant/user |
| `{{LOGGER_PATTERN}}` | Project logger (e.g. `logger.info(...)`) |
| `{{STYLE_APPROACH}}` | CSS Modules / Tailwind / CSS-in-JS |
