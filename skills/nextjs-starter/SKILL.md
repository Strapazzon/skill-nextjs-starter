---
name: nextjs-starter
description: Scaffold a production-ready Next.js project with App Router, TypeScript, Tailwind 4, shadcn/ui, and opinionated project structure. Use when the user asks to create a new Next.js project, start a new web app, or bootstrap a frontend application.
---

# Next.js Starter Scaffolder

Scaffolds a complete, opinionated Next.js project with interactive customization. Generates project structure, installs dependencies, configures tooling, writes architecture rules in CLAUDE.md, and makes an initial commit.

## Execution Flow

6 phases executed sequentially:
1. Interactive questions (project name, package manager, extras, MCPs)
2. Base scaffold via create-next-app
3. Configuration (shadcn/ui, Prettier, ESLint, extras)
4. Folder structure (hybrid: layer-based global + feature-based domain)
5. Rules files (CLAUDE.md + .mcp.json)
6. Verification + initial commit

---

## Phase 1: Interactive Questions

Ask the user 4 questions using `AskUserQuestion`. Store the answers as variables for use in later phases.

### Q1 — Project Name

```
AskUserQuestion(
  question: "What is the name of your project?",
  type: "free_text"
)
```

Store as `PROJECT_NAME`.

### Q2 — Package Manager

```
AskUserQuestion(
  question: "Which package manager would you like to use?",
  type: "single_select",
  options: [
    { value: "pnpm", label: "pnpm (recommended)" },
    { value: "npm",  label: "npm" },
    { value: "bun",  label: "bun" }
  ]
)
```

Store as `PM`. Derive the following variables from `PM`:

| `PM`   | `PM_RUN`   | `PM_DLX`   | `PM_CREATE`    | `PM_FLAG`    |
|--------|------------|------------|----------------|--------------|
| `pnpm` | `pnpm`     | `pnpm dlx` | `pnpm create`  | `--use-pnpm` |
| `npm`  | `npm run`  | `npx`      | `npx`          | `--use-npm`  |
| `bun`  | `bun`      | `bunx`     | `bunx`         | `--use-bun`  |

### Q3 — Extras

```
AskUserQuestion(
  question: "Which extras would you like to include?",
  type: "multi_select",
  options: [
    { value: "husky",       label: "husky + lint-staged (recommended)" },
    { value: "commitlint",  label: "commitlint (recommended)" },
    { value: "editorconfig",label: ".editorconfig (recommended)" },
    { value: "vscode",      label: "VS Code settings (recommended)" },
    { value: "env_example", label: ".env.example (recommended)" }
  ]
)
```

Store as `EXTRAS` (list of selected values).

### Q4 — MCPs

```
AskUserQuestion(
  question: "Which MCPs would you like to configure? (Context7 is always included)",
  type: "multi_select",
  options: [
    { value: "context7",       label: "Context7 (always included)" },
    { value: "figma",          label: "Figma MCP" },
    { value: "browser_preview",label: "Browser Preview" },
    { value: "other",          label: "Other (you will be prompted for details)" }
  ]
)
```

Store as `MCPS` (list of selected values). `context7` is always included regardless of selection. If `other` is selected, ask a follow-up free-text question to capture the additional MCP details.
