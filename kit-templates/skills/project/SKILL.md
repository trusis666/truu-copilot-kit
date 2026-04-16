---
name: project
description: 'Project-wide reference for {{PROJECT_NAME}} — structure, tech stack, commands, coding conventions, environment variables, and task management process. Read when you need to understand how the project is organised or how to run it.'
metadata:
  author: '{{PROJECT_NAME}}'
  version: '1.0'
---

# Project Reference

Use this skill for project-wide context: structure, stack, conventions, and process.

---

## Project Overview

{{PROJECT_NAME}} is a {{TECH_STACK}} application.

<!-- Add 1-2 sentences describing what the project does and who uses it. -->

---

## Monorepo / Directory Structure

```
{{REPO_ROOT_STRUCTURE}}
```

<!-- Example:
my-project/
├── src/
│   ├── app/          # Next.js App Router pages & API routes
│   ├── components/   # React components
│   └── lib/          # Business logic, services, utilities
├── prisma/
│   └── schema.prisma
└── package.json
-->

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | <!-- e.g. Next.js 15, React 19, TypeScript --> |
| Backend | <!-- e.g. Next.js API Routes, Node.js --> |
| Database | <!-- e.g. PostgreSQL + Prisma --> |
| Testing | <!-- e.g. Vitest, Playwright --> |
| Deployment | <!-- e.g. Vercel, Railway, Fly.io --> |
| Auth | <!-- e.g. Auth.js v5, Clerk, JWT --> |

---

## Key Commands

```bash
# Development
npm run dev           # start dev server

# Build
npm run build         # production build

# Verification (run after every non-trivial change)
{{CHECK_COMMAND}}     # build + test + lint — must be green before marking any task done

# Tests
npx vitest run        # all tests
npx vitest run <file> # single test file
npx vitest            # watch mode

# Database
npx prisma db push    # sync schema to DB
npx prisma generate   # regenerate client after schema change
npx prisma studio     # visual DB browser
```

---

## Environment Variables

| Variable | Required | Description |
|---|---|---|
| `DATABASE_URL` | ✅ | PostgreSQL connection string |
| <!-- fill in --> | | |

See `.env.example` for a complete list with example values.

---

## Coding Conventions

<!-- Document your project's non-negotiable rules here.
Reference the domain-specific constraints from copilot-instructions.md. -->

### Authentication

{{AUTH_PATTERN}}

### Database Access

{{DB_IMPORT_PATTERN}}

Every query must include: `{{DB_SCOPE_PATTERN}}`

### Logging

{{LOGGER_PATTERN}}

### Styling

{{STYLE_APPROACH}}

### File Naming

<!-- e.g.:
- `.service.ts` — business logic
- `.test.ts` — co-located unit test
- `.component.tsx` — React component
-->

---

## Task Management Process

Tasks live in `tasks/todo.md`. Lessons live in `tasks/lessons.md`.

- Before starting a task: add a row to the Active This Session table in `tasks/todo.md`
- After completing: remove the row, add to `CHANGELOG.md`
- After a mistake or correction: add to `tasks/lessons.md` immediately

Lessons are periodically distilled into skill files via `/distill-lessons` so they're seen at point-of-use.
