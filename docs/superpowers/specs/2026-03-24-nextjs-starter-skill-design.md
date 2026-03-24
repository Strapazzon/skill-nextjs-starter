# Next.js Starter Skill - Design Spec

## Overview

A Claude Code skill that scaffolds a production-ready, highly opinionated Next.js project from scratch. Generates the entire project structure, installs dependencies, configures tooling, and produces a `CLAUDE.md` with rigid architecture rules for all future work.

## Goals

- One-command project bootstrap with interactive customization
- Enforce consistent architecture across all projects
- Integrate with superpowers and frontend-design skills via CLAUDE.md
- Configure MCP servers (Context7 as base) for enhanced development

## Non-Goals

- Runtime framework or library (this is a scaffolder only)
- Ongoing enforcement engine (CLAUDE.md handles that passively)
- Imposing a specific package manager (user chooses)

---

## Skill Metadata

```yaml
---
name: nextjs-starter
description: Scaffold a production-ready Next.js project with App Router, TypeScript, Tailwind 4, shadcn/ui, and opinionated project structure. Use when the user asks to create a new Next.js project, start a new web app, or bootstrap a frontend application.
---
```

Lives in `skills/nextjs-starter/SKILL.md` as a monolithic skill file.

---

## Execution Flow

### Phase 1: Interactive Questions

Four questions via `AskUserQuestion`:

**Q1 - Project name** (free input):
> "What is the project name?"

**Q2 - Package manager** (single-select):
- pnpm (Recommended)
- npm
- bun

**Q3 - Extras** (multi-select, all recommended by default):
- husky + lint-staged (pre-commit hooks)
- commitlint (conventional commits)
- `.editorconfig`
- VS Code settings (`.vscode/settings.json` + recommended extensions)
- `.env.example` with common variables

**Q4 - MCPs** (multi-select):
- Context7 (always included, not deselectable)
- Figma MCP
- Browser Preview (Claude Preview)
- Other (free input)

No further questions. Everything else is decided by the skill.

---

### Phase 2: Base Scaffold

```bash
# Uses the package manager chosen in Q2. Examples:
pnpm create next-app <name> --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" --use-pnpm
npx create-next-app <name> --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" --use-npm
bunx create-next-app <name> --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" --use-bun
```

All subsequent install/run commands use the chosen package manager (e.g. `pnpm add`, `npm install`, `bun add`).

Then remove default boilerplate:
- Default home page content (replace with minimal placeholder)
- Default styles (keep only Tailwind imports)
- Default favicon and images

---

### Phase 3: Configuration

**Core dependencies:**
- `shadcn/ui` via `<pm> dlx shadcn@latest init` (style: default, base color: neutral, CSS variables: yes) — where `<pm>` is the chosen package manager (`pnpm dlx`, `npx`, `bunx`)
- `prettier` + `prettier-plugin-tailwindcss`
- ESLint config with import ordering rules

**Generated config files:**

`tsconfig.json` - TypeScript strict mode, path aliases (`@/*` -> `src/*`)

`.prettierrc`:
```json
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "tabWidth": 2,
  "printWidth": 80,
  "plugins": ["prettier-plugin-tailwindcss"]
}
```

ESLint config - Match whatever format `create-next-app` generates (flat config `eslint.config.mjs` for Next.js 15+, or `.eslintrc.json` for older). Extends `next/core-web-vitals` + TypeScript rules, import ordering.

Tailwind config - Adapt to the installed version: Tailwind v4 uses CSS-based configuration (`@theme` in `globals.css`), v3 uses `tailwind.config.ts`. The skill detects which version `create-next-app` installed and configures accordingly. Integrated with shadcn/ui theme.

`.gitignore` - Complete for Next.js projects.

**Conditional extras (based on Q3):**

- **husky**: `pnpm dlx husky-init`, pre-commit hook runs `lint-staged` (lint + format on staged files)
- **commitlint**: `@commitlint/cli` + `@commitlint/config-conventional`, creates `commitlint.config.js`
- **editorconfig**: `.editorconfig` (indent 2 spaces, UTF-8, LF, trim trailing whitespace)
- **VS Code**: `.vscode/settings.json` (format on save, Prettier default, Tailwind IntelliSense) + `.vscode/extensions.json`
- **.env.example**: Template with `NEXT_PUBLIC_APP_URL`, `DATABASE_URL`, etc.

---

### Phase 4: Folder Structure

```
src/
├── app/                          # Next.js App Router (ONLY routes and layouts)
│   ├── layout.tsx                # Root layout
│   ├── page.tsx                  # Home page (minimal placeholder)
│   ├── globals.css               # Tailwind imports + CSS variables
│   └── (example)/                # Route group example
│       └── page.tsx
│
├── components/
│   ├── ui/                       # shadcn/ui (NEVER edit manually, use CLI)
│   │   ├── button.tsx
│   │   └── ...
│   └── shared/                   # Reusable project components
│       └── .gitkeep              # (composed from ui/)
│
├── features/                     # Business domain (feature-based)
│   └── .gitkeep                  # e.g. features/auth/, features/dashboard/
│                                 # each feature has: components/, hooks/, lib/, types/
│
├── hooks/                        # Global reusable hooks
│   └── .gitkeep
│
├── lib/                          # Utilities, helpers, configs, clients
│   ├── utils.ts                  # cn() helper from shadcn
│   └── .gitkeep
│
└── types/                        # Global / shared types
    └── .gitkeep
```

The skill pre-installs `button` as the first shadcn component to populate `components/ui/`.

---

### Phase 5: Rules Files

#### CLAUDE.md

```markdown
# <Project Name>

## Stack
Next.js (App Router), TypeScript, Tailwind 4, shadcn/ui, <chosen package manager>

## Architecture Rules

### Folder Structure
- `app/` - ONLY routes, layouts, loading, error. Zero business logic
- `components/ui/` - Managed by shadcn CLI. NEVER create/edit manually
- `components/shared/` - Reusable components, ALWAYS composed from ui/
- `features/<name>/` - Self-contained: components/, hooks/, lib/, types/
- `lib/` - Pure functions, clients, configs. No React components
- `hooks/` - Global hooks. Feature hooks live in features/<name>/hooks/
- `types/` - Types shared between features

### Component Rules
- Every visual component MUST use primitives from components/ui/
- NEVER use native <button>, <input>, <select> - use shadcn
- Add new ui components: `<pm> dlx shadcn@latest add <name>`
- Components MUST be reusable. Extract when used 2+ times
- Props typed with interfaces, never any

### Code Rules
- Path aliases: use @/ for all imports (never ../../../)
- Exports: named exports only (never default export except pages)
- Server Components by default. Use "use client" only when necessary
- Pure functions in lib/. Side effects isolated in hooks

## Required Skills
- USE superpowers:brainstorming BEFORE creating any new feature
- USE frontend-design when creating visual components and pages
- USE superpowers:writing-plans for features touching more than 2 files
- USE superpowers:verification-before-completion before declaring tasks done

## Commands
- `<pm> dev` - dev server
- `<pm> build` - production build
- `<pm> lint` - ESLint
- `<pm> format` - Prettier (runs `prettier --write .`)

Note: The skill adds `"format": "prettier --write ."` to package.json scripts.
```

#### .mcp.json

Base configuration with Context7 always included:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp@latest"]
    }
  }
}
```

Additional MCPs from Q4 are appended here with their standard configurations.

---

### Phase 6: Verification + Initial Commit

**Verification (automatic):**
1. `<pm> build` - Ensures project compiles without errors
2. `<pm> lint` - Ensures zero ESLint warnings/errors
3. On failure: skill auto-fixes and re-verifies (max 3 attempts)

**Initial commit:**
```
feat: scaffold project with Next.js, Tailwind 4, shadcn/ui

- App Router with TypeScript strict mode
- shadcn/ui component library (Radix + Tailwind)
- Prettier + ESLint configured
- Opinionated folder structure (hybrid: layer + feature)
- CLAUDE.md with architecture rules
- MCP configuration (Context7 + extras)
```

**Final output to user:**
```
Project <name> created successfully.
- X files generated
- Build: OK | Lint: OK
- Extras: husky, commitlint, ...
- MCPs: Context7, Figma, ...
- Initial commit created

Next step: cd <name> && <pm> dev
```

---

## Architecture Rules Summary

| Rule | Enforcement |
|------|-------------|
| All components use shadcn primitives | CLAUDE.md instruction |
| `components/ui/` never edited manually | CLAUDE.md instruction |
| `app/` contains only routes/layouts | CLAUDE.md instruction |
| Named exports only (except pages) | CLAUDE.md + ESLint |
| Path aliases with @/ | tsconfig + CLAUDE.md |
| Server Components by default | CLAUDE.md instruction |
| Feature-based isolation for domain code | CLAUDE.md + folder structure |
| Brainstorming before new features | CLAUDE.md skill instruction |
| Verification before completion | CLAUDE.md skill instruction |

---

## Dependencies

### Always Installed
- next, react, react-dom
- typescript, @types/react, @types/node
- tailwindcss (v4)
- shadcn/ui (+ radix primitives, class-variance-authority, clsx, tailwind-merge)
- prettier, prettier-plugin-tailwindcss
- eslint, eslint-config-next

### Conditionally Installed
- husky, lint-staged
- @commitlint/cli, @commitlint/config-conventional
