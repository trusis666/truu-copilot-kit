---
mode: agent
description: Smart kit command — detects whether this project has truu-copilot-kit installed, then either runs full first-time setup or pulls the latest updates. Run this from any project.
---

You are managing `truu-copilot-kit` for the current workspace. First detect what state this project is in, then act accordingly. Work autonomously — do not ask for confirmation between steps unless something is genuinely ambiguous.

---

## Phase 0 — Detect project state

Run these checks to determine which path to take:

```bash
# 1. Does the kit exist in this project?
ls .github/kit-templates/ 2>/dev/null && echo "KIT_PRESENT" || echo "KIT_MISSING"

# 2. Is the truu-kit remote registered?
git remote get-url truu-kit 2>/dev/null && echo "REMOTE_OK" || echo "REMOTE_MISSING"

# 3. Does .github/copilot-instructions.md exist and have no {{PLACEHOLDERS}}?
# Only match {{UPPER_SNAKE_CASE}} — not JSX/template syntax like style={{}}
grep -cP "\{\{[A-Z_]+\}\}" .github/copilot-instructions.md 2>/dev/null || echo "NOT_CONFIGURED"
```

**Decision:**
- `KIT_MISSING` → go to **PATH A: First-time setup**
- `KIT_PRESENT` + copilot-instructions.md has `{{` placeholders → go to **PATH B: Kit present but not configured**
- `KIT_PRESENT` + copilot-instructions.md has no placeholders → go to **PATH C: Pull updates**

---

## PATH A: First-time setup

The kit is not installed yet. Install it and configure the project.

### A1 — Wire the subtree

```bash
git remote add truu-kit https://github.com/trusis666/truu-copilot-kit
git config --global merge.ours.driver true
```

Check if `.github/` already has content:
```bash
ls .github/ 2>/dev/null && echo "HAS_GITHUB" || echo "NO_GITHUB"
```

**If NO_GITHUB** — clean subtree add:
```bash
git subtree add --prefix=.github truu-kit main --squash
```

**If HAS_GITHUB** — prefix already exists, use the workaround:
```bash
git fetch truu-kit
git merge -s ours --no-commit --allow-unrelated-histories truu-kit/main
git read-tree --prefix=.github/ -u truu-kit/main
git commit -m "chore: add truu-copilot-kit into .github"
```

### A2 — Bootstrap .gitattributes

```bash
cp .github/kit-templates/gitattributes .gitattributes
```

Leave all entries commented out for now. The setup agent will uncomment them as it enriches skills.

### A3 — Run full project configuration

Now run the setup agent instructions from the kit:

> Read the file `.github/kit-templates/setup-project.prompt.md` and follow every phase in it exactly. That file is the authoritative setup workflow — explore the codebase, fill all templates, create skills, configure the dev agent, verify, and commit.

---

## PATH B: Kit present, project not configured

The kit files are here but templates were never filled. Run the configuration without reinstalling:

> Read the file `.github/kit-templates/setup-project.prompt.md` and follow every phase in it exactly, starting from Phase 3 (skip the subtree steps — the kit is already installed).

---

## PATH C: Pull updates

The kit is installed and configured. Pull the latest changes from the kit.

### C1 — Ensure prerequisites

```bash
# Add remote if missing
git remote get-url truu-kit 2>/dev/null || git remote add truu-kit https://github.com/trusis666/truu-copilot-kit
git config --global merge.ours.driver true
```

### C2 — Pull

Try standard subtree pull first:
```bash
git subtree pull --prefix=.github truu-kit main --squash 2>&1
```

**If it succeeds** → continue to C3.

**If it fails with "was never added"** — the subtree wasn't bootstrapped via `git subtree add` (common for source/founder repos). In this case only update `kit-templates/` — prompts, agents, and skills in `.github/` are project-owned and must NOT be overwritten:
```bash
git archive truu-kit/main kit-templates/ | tar -x -C .github/
git add .github/kit-templates/
git diff --cached --stat
git commit -m "chore: update kit-templates from truu-copilot-kit"
```

**If it fails with "unrelated histories"** — use the DAG workaround (only for projects where `.github/` content is entirely from the kit):
```bash
git fetch truu-kit
git merge -s ours --no-commit --allow-unrelated-histories truu-kit/main
git read-tree --prefix=.github/ -u truu-kit/main
git commit -m "chore: pull truu-copilot-kit updates"
```

### C3 — Review changes

```bash
git diff HEAD~1 HEAD -- .github/
```

Report:
- Files updated from kit (prompts, agents, copilot-recursive-template, kit-templates)
- Files protected by `.gitattributes` — kept project version, no diff shown
- Any conflicts needing manual resolution

### C4 — Check for new skills or templates

```bash
ls .github/skills/
grep -rl "{{" .github/kit-templates/ --include="*.md" 2>/dev/null
```

- If a new skill directory appeared from the kit that the project doesn't have a project-specific version of — note it (no action required unless the project has relevant patterns to add)
- If any kit-template has new `{{placeholders}}` that aren't in the project's active files — flag them for the user

### C5 — Summarise

Report concisely:
- What changed (file list)
- What was protected
- Any new capabilities now available (new prompts, skills, templates)
- Any action items

