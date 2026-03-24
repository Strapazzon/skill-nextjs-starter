# nextjs-starter

Claude Code skill that scaffolds production-ready Next.js projects with an opinionated architecture.

## What it does

When invoked, the skill interactively creates a complete Next.js project with:

- **Next.js** App Router + TypeScript strict mode
- **Tailwind CSS 4** with automatic v3/v4 detection
- **shadcn/ui** component library (Radix + Tailwind)
- **Prettier** + **ESLint** configured and integrated
- **Hybrid folder structure** — layer-based for global code, feature-based for domain
- **CLAUDE.md** with rigid architecture rules for all future Claude sessions
- **.mcp.json** with Context7 and optional MCPs
- Optional extras: husky, commitlint, .editorconfig, VS Code settings, .env.example

## Installation

### Option 1: Add as a local plugin (development)

```bash
claude plugins add /path/to/skill-nextjs-starter
```

### Option 2: Add from GitHub

```bash
claude plugins add github:your-username/skill-nextjs-starter
```

After installing, restart Claude Code. The skill will be available automatically.

## Usage

Say any of these to Claude Code:

- "Create a new Next.js project"
- "Scaffold a web app"
- "Bootstrap a frontend application"
- "Init a new Next.js project"

The skill will guide you through 4 questions:

1. **Project name** — the directory name for your project
2. **Package manager** — pnpm (recommended), npm, or bun
3. **Extras** — optional tooling (husky, commitlint, editorconfig, VS Code, .env.example)
4. **MCPs** — Context7 (always included), Figma MCP, Browser Preview, or custom

Then it automatically:

1. Runs `create-next-app` with the right flags
2. Configures shadcn/ui, Prettier, ESLint
3. Sets up the folder structure
4. Writes `CLAUDE.md` with architecture rules
5. Writes `.mcp.json` with your MCP choices
6. Verifies the build and lint pass
7. Creates the initial git commit

## Project structure generated

```
src/
├── app/                    # Routes and layouts ONLY
├── components/
│   ├── ui/                 # shadcn/ui (managed by CLI, never edit manually)
│   └── shared/             # Reusable components (composed from ui/)
├── features/               # Domain logic (auth/, dashboard/, etc.)
│   └── <name>/
│       ├── components/     # Feature-specific components
│       ├── hooks/          # Feature-specific hooks
│       ├── lib/            # Feature-specific utilities
│       └── types/          # Feature-specific types
├── hooks/                  # Global reusable hooks
├── lib/                    # Utilities, helpers, configs
└── types/                  # Global shared types
```

## Architecture rules (enforced via CLAUDE.md)

- Every component uses shadcn/ui primitives — no native HTML form elements
- `components/ui/` is managed exclusively by the shadcn CLI
- `app/` contains only routes, layouts, loading, and error files
- Named exports only (except page components)
- Server Components by default, `"use client"` only when necessary
- Path aliases (`@/`) for all imports
- Feature isolation — each feature is self-contained

## Skills integration

The generated `CLAUDE.md` instructs Claude to use these skills when working on the project:

- **superpowers:brainstorming** before creating any new feature
- **frontend-design** when building visual components and pages
- **superpowers:writing-plans** for features touching more than 2 files
- **superpowers:verification-before-completion** before declaring tasks done

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI installed
- Node.js 18+
- One of: pnpm, npm, or bun

## Plugin structure

```
skill-nextjs-starter/
├── .claude-plugin/
│   └── plugin.json         # Plugin metadata
├── skills/
│   └── nextjs-starter/
│       └── SKILL.md        # The skill (all logic lives here)
└── README.md
```
