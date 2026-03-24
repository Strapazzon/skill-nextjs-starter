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
