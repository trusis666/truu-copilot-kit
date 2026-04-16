# Setup Guide

How to wire `truu-copilot-kit` into any project via `git subtree`, and how to contribute improvements back.

---

## Two repo roles

| Role | Who | Subtree direction |
|---|---|---|
| **Source** | founder-os | Pushes improvements TO kit |
| **Consumer** | every other project | Pulls updates FROM kit |

**Founder-os** has enriched versions of the 5 generic skills and protects them via `.gitattributes merge=ours`. It CAN pull kit updates — prompts, agents, and `copilot-recursive-template.md` update freely. Only skills are blocked.

---

## New project: pull from kit

### Step 1 — Add the subtree (manual, 30 seconds)

```bash
git remote add truu-kit https://github.com/trusis666/truu-copilot-kit
git subtree add --prefix=.github truu-kit main --squash
git config --global merge.ours.driver true   # once per machine
```

### Step 2 — Run the setup agent (everything else is automated)

Open GitHub Copilot Chat, switch to **Agent** mode, and run:

```
/setup-project
```

Or attach the prompt manually:

```
Use the instructions in .github/kit-templates/setup-project.prompt.md to set up this project.
```

The agent will:
1. Explore your codebase — tech stack, auth, DB, logging, CSS, folder structure
2. Fill `copilot-instructions.md` from the template with real project values
3. Decide which generic skills to enrich (project-specific additions) vs. leave as-is
4. Create project-specific skills for domains the kit doesn't cover (auth, database, UI, etc.)
5. Uncomment `.gitattributes` protection only for skills it has enriched
6. Create your dev agent and task tracking files
7. Verify no `{{placeholders}}` remain and commit everything

> The agent reads the full prompt from `kit-templates/setup-project.prompt.md`. That file is the source of truth for the setup workflow — read it if you want to understand or customize the steps.

---

## Pulling kit updates (consumer projects)

```bash
git subtree pull --prefix=.github truu-kit main --squash
```

- **Skills you've enriched** (listed in `.gitattributes` with `merge=ours`) → kept unchanged
- **Skills you haven't touched** → update from kit
- **Prompts, generic agents, `copilot-recursive-template.md`** → always update from kit
- `copilot-instructions.md`, your project skills, your dev agent → not in kit, never touched

---

## What the kit manages vs. what you own

| Path | Owned by |
|---|---|
| `.github/skills/architecture/` | Kit (until you enrich it → then yours) |
| `.github/skills/context-engineering/` | Kit (until you enrich it → then yours) |
| `.github/skills/debugging/` | Kit (until you enrich it → then yours) |
| `.github/skills/security/` | Kit (until you enrich it → then yours) |
| `.github/skills/testing/` | Kit (until you enrich it → then yours) |
| `.github/prompts/` (all 16) | Kit — always updates |
| `.github/agents/architect.agent.md` | Kit — always updates |
| `.github/agents/security-auditor.agent.md` | Kit — always updates |
| `.github/copilot-recursive-template.md` | Kit — always updates |
| `.github/kit-templates/` | Kit — reference templates |
| `.github/copilot-instructions.md` | **You** — never in kit |
| `.github/skills/project/` | **You** — never in kit |
| `.github/skills/<domain-specific>/` | **You** — never in kit |
| `.github/agents/<project>-dev.agent.md` | **You** — never in kit |
| `tasks/` | **You** — never in kit |

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
