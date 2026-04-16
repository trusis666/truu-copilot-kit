# copilot-workflow-kit

A reusable GitHub Copilot configuration kit: skills, prompts, agents, and workflow templates for VS Code.
Designed to be pulled into any project via `git subtree` and extended with project-specific overrides.

## What's in the kit

```
copilot-workflow-kit/
├── skills/                     ← generic skills, auto-discovered by Copilot
│   ├── architecture/           ← ADR/ARD/SDD/IRD/RFD decision framework
│   ├── context-engineering/    ← anti-hallucination patterns, prompt quality rubric
│   ├── debugging/              ← debug script conventions
│   ├── security/               ← OWASP Top 10 + SSRF + prompt injection
│   └── testing/                ← co-location, mocking, eval concepts
├── prompts/                    ← slash-command workflow prompts
├── agents/                     ← architect + security-auditor agents
├── copilot-recursive-template.md  ← 6-phase implementation loop
└── kit-templates/              ← starting points for project-specific files
    ├── copilot-instructions.md
    ├── tasks/                  ← lessons.md, todo.md, lessons-quick-ref.md
    ├── skills/                 ← project/, code-standards/ SKILL.md templates
    └── agents/                 ← dev.agent.md, architect.agent.md, security-auditor.agent.md
```

Skills from `skills/` land directly in `.github/skills/` in the target repo and are auto-discovered by Copilot.
`kit-templates/` files are NOT auto-applied — copy them into your project and fill in the `{{PLACEHOLDER}}` values.

## Usage

See [SETUP.md](SETUP.md) for step-by-step subtree setup and first-use instructions.

## Updating the kit

```bash
# Pull latest kit changes into your project
git subtree pull --prefix=.github https://github.com/<you>/copilot-workflow-kit main --squash
```

Only files that exist in the kit will be updated. Your project-specific files (project/SKILL.md, copilot-instructions.md, etc.) are untouched.

## Contributing back

If you fix a bug or improve a generic skill, you can push it back:

```bash
git subtree push --prefix=.github https://github.com/<you>/copilot-workflow-kit main
```
