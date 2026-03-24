# Next.js Starter Skill - Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a Claude Code skill that scaffolds a complete, opinionated Next.js project with interactive customization.

**Architecture:** Single monolithic `SKILL.md` file containing all scaffolding logic across 6 phases. The skill uses `AskUserQuestion` for interactive configuration, `Bash` for commands, and `Write` for file generation. A `plugin.json` registers the skill as a Claude Code plugin.

**Tech Stack:** Claude Code Skill (Markdown), Next.js, TypeScript, Tailwind 4, shadcn/ui, Prettier, ESLint

---

## File Structure

```
skill-nextjs-starter/
├── .claude-plugin/
│   └── plugin.json              # Plugin metadata for Claude Code
└── skills/
    └── nextjs-starter/
        └── SKILL.md             # Complete skill (all 6 phases)
```

---

### Task 1: Create Plugin Metadata

**Files:**
- Create: `.claude-plugin/plugin.json`

- [ ] **Step 1: Create the plugin.json file**

```json
{
  "name": "nextjs-starter",
  "description": "Scaffold production-ready Next.js projects with App Router, TypeScript, Tailwind 4, shadcn/ui, and opinionated architecture",
  "author": {
    "name": "Getulio"
  }
}
```

- [ ] **Step 2: Commit**

```bash
git add .claude-plugin/plugin.json
git commit -m "chore: add plugin metadata"
```

---

### Task 2: Write SKILL.md - Frontmatter + Overview

**Files:**
- Create: `skills/nextjs-starter/SKILL.md`

- [ ] **Step 1: Write the frontmatter and overview section**

```markdown
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
```

- [ ] **Step 2: Commit**

```bash
git add skills/nextjs-starter/SKILL.md
git commit -m "feat: add skill frontmatter and overview"
```

---

### Task 3: Write Phase 1 - Interactive Questions

**Files:**
- Modify: `skills/nextjs-starter/SKILL.md`

- [ ] **Step 1: Append Phase 1 to SKILL.md**

This section instructs the agent to ask 4 questions using `AskUserQuestion`:

- Q1: Project name (free input)
- Q2: Package manager (single-select: pnpm recommended, npm, bun)
- Q3: Extras (multi-select: husky+lint-staged, commitlint, .editorconfig, VS Code settings, .env.example — all recommended)
- Q4: MCPs (multi-select: Context7 always included, Figma MCP, Browser Preview, Other free input)

Store answers as variables: `PROJECT_NAME`, `PM` (package manager), and derived commands:

| PM chosen | `PM_RUN` | `PM_DLX` | `PM_CREATE` | `PM_FLAG` |
|-----------|----------|----------|-------------|-----------|
| pnpm | `pnpm` | `pnpm dlx` | `pnpm create` | `--use-pnpm` |
| npm | `npm run` | `npx` | `npx` | `--use-npm` |
| bun | `bun` | `bunx` | `bunx` | `--use-bun` |

Also: `EXTRAS` (list of selected extras), `MCPS` (list of selected MCPs).

Include the exact `AskUserQuestion` call structure for each question with options, descriptions, and multi-select flags.

- [ ] **Step 2: Commit**

```bash
git add skills/nextjs-starter/SKILL.md
git commit -m "feat: add phase 1 - interactive questions"
```

---

### Task 4: Write Phase 2 - Base Scaffold

**Files:**
- Modify: `skills/nextjs-starter/SKILL.md`

- [ ] **Step 1: Append Phase 2 to SKILL.md**

Instructions for the agent to:
1. Run `{PM_CREATE} next-app {PROJECT_NAME} --typescript --tailwind --eslint --app --src-dir --import-alias "@/*" {PM_FLAG}`
2. `cd` into the project directory
3. Remove boilerplate: replace `src/app/page.tsx` with minimal placeholder, clean `globals.css` to keep only Tailwind directives and CSS variables, remove default images from `public/`

Include the exact replacement content for `page.tsx`:
```tsx
export default function Home() {
  return (
    <main className="flex min-h-screen flex-col items-center justify-center">
      <h1 className="text-4xl font-bold">Welcome</h1>
    </main>
  );
}
```

- [ ] **Step 2: Commit**

```bash
git add skills/nextjs-starter/SKILL.md
git commit -m "feat: add phase 2 - base scaffold"
```

---

### Task 5: Write Phase 3 - Configuration

**Files:**
- Modify: `skills/nextjs-starter/SKILL.md`

- [ ] **Step 1: Append Phase 3 core configuration to SKILL.md**

Instructions for:

1. Initialize shadcn/ui: `{PM_DLX} shadcn@latest init` with flags for style default, base color neutral, CSS variables yes
2. Add button component: `{PM_DLX} shadcn@latest add button`
3. Install Prettier: `{PM} add -D prettier prettier-plugin-tailwindcss`
4. Write `.prettierrc` with exact JSON content (semi, singleQuote, trailingComma, tabWidth, printWidth, plugins)
5. Add `"format": "prettier --write ."` script to `package.json`
6. Verify `tsconfig.json` has strict mode and `@/*` path alias — `create-next-app` already generates these with the `--import-alias` flag, so only verify and adjust if needed
6. Detect ESLint config format (flat config `eslint.config.mjs` or legacy `.eslintrc.json`) and extend with import ordering rules
7. Detect Tailwind version (v4 CSS-based vs v3 `tailwind.config.ts`) and configure accordingly with shadcn theme

- [ ] **Step 2: Append Phase 3 conditional extras to SKILL.md**

For each extra selected in Q3:

- **husky + lint-staged**: Use `{PM_DLX} husky init` (husky v9+) or `{PM_DLX} husky-init` (v8) — detect the installed version. Configure `.husky/pre-commit` to run `{PM_DLX} lint-staged`, create `.lintstagedrc.json` with `{"*.{js,jsx,ts,tsx}": ["eslint --fix", "prettier --write"], "*.{json,md,css}": ["prettier --write"]}`
- **commitlint**: `{PM} add -D @commitlint/cli @commitlint/config-conventional`, create `commitlint.config.js` with `module.exports = { extends: ['@commitlint/config-conventional'] }`, add commit-msg hook to husky (if husky selected)
- **editorconfig**: Write `.editorconfig` (root=true, indent_style=space, indent_size=2, charset=utf-8, end_of_line=lf, trim_trailing_whitespace=true, insert_final_newline=true)
- **VS Code**: Write `.vscode/settings.json` (formatOnSave, defaultFormatter prettier, tailwindCSS.experimental.classRegex) + `.vscode/extensions.json` (bradlc.vscode-tailwindcss, esbenp.prettier-vscode, dbaeumer.vscode-eslint)
- **.env.example**: Write template with `NEXT_PUBLIC_APP_URL=http://localhost:3000`, `DATABASE_URL=`, `NEXT_PUBLIC_API_URL=`

- [ ] **Step 3: Commit**

```bash
git add skills/nextjs-starter/SKILL.md
git commit -m "feat: add phase 3 - configuration and extras"
```

---

### Task 6: Write Phase 4 - Folder Structure

**Files:**
- Modify: `skills/nextjs-starter/SKILL.md`

- [ ] **Step 1: Append Phase 4 to SKILL.md**

Instructions to create the directory tree and placeholder files:

```bash
mkdir -p src/components/shared src/features src/hooks src/lib src/types
touch src/components/shared/.gitkeep src/features/.gitkeep src/hooks/.gitkeep src/types/.gitkeep
```

Note: `src/components/ui/` is already created by `shadcn init` in Phase 3. `src/lib/utils.ts` is also created by shadcn. `src/app/` files exist from Phase 2.

- [ ] **Step 2: Commit**

```bash
git add skills/nextjs-starter/SKILL.md
git commit -m "feat: add phase 4 - folder structure"
```

---

### Task 7: Write Phase 5 - Rules Files (CLAUDE.md + .mcp.json)

**Files:**
- Modify: `skills/nextjs-starter/SKILL.md`

- [ ] **Step 1: Append CLAUDE.md generation to SKILL.md**

The agent writes a `CLAUDE.md` file at the project root using the `Write` tool. Content uses the `PROJECT_NAME` and `PM` variables. Includes all sections from the spec:

- Stack (with chosen PM)
- Architecture Rules: Folder Structure, Component Rules, Code Rules
- Required Skills: brainstorming, frontend-design, writing-plans, verification-before-completion
- Commands: dev, build, lint, format (with correct PM prefix — note: npm uses `npm run` while pnpm/bun use direct `pnpm`/`bun`)

Include the complete CLAUDE.md template as a heredoc-style block in the skill so the agent writes it verbatim (with variable substitution for project name and PM commands).

- [ ] **Step 2: Append .mcp.json generation to SKILL.md**

The agent writes `.mcp.json` at the project root. Context7 is always included. For each MCP selected in Q4, append the server config:

- **Context7**: `{"command": "npx", "args": ["-y", "@upstash/context7-mcp@latest"]}`
- **Figma MCP**: `{"command": "npx", "args": ["-y", "@anthropic-ai/figma-mcp@latest"]}`
- **Browser Preview**: note that this is configured in Claude Code settings, not .mcp.json — instruct user accordingly
- **Other**: prompt the agent to ask the user for the MCP command/args

- [ ] **Step 3: Commit**

```bash
git add skills/nextjs-starter/SKILL.md
git commit -m "feat: add phase 5 - CLAUDE.md and .mcp.json generation"
```

---

### Task 8: Write Phase 6 - Verification + Initial Commit

**Files:**
- Modify: `skills/nextjs-starter/SKILL.md`

- [ ] **Step 1: Append Phase 6 to SKILL.md**

Instructions for:

1. Run `{PM_RUN} build` — if it fails, read errors, fix, retry (max 3 attempts)
2. Run `{PM_RUN} lint` — if it fails, read errors, fix, retry (max 3 attempts)
3. If both pass, initialize git repo (`git init` if not already), stage all files, commit with the message from the spec
4. Print the summary output showing: project name, file count, build/lint status, extras installed, MCPs configured, next step command

Include the exact commit message and output format from the spec.

- [ ] **Step 2: Commit**

```bash
git add skills/nextjs-starter/SKILL.md
git commit -m "feat: add phase 6 - verification and initial commit"
```

---

### Task 9: Final Review and Polish

**Files:**
- Modify: `skills/nextjs-starter/SKILL.md`

- [ ] **Step 1: Read the complete SKILL.md end-to-end**

Verify:
- All 6 phases are present and in order
- No placeholder text or TODOs remain
- Variable references are consistent (`PM`, `PM_RUN`, `PM_DLX`, `PM_CREATE`, `PM_FLAG`)
- Instructions are clear enough for a fresh agent session to execute
- Skill follows conciseness principle — no unnecessary explanations

- [ ] **Step 2: Test trigger — verify the skill description matches usage patterns**

The description should trigger on:
- "create a new Next.js project"
- "start a new web app"
- "bootstrap a frontend application"
- "scaffold a Next.js app"
- "init a new project"

- [ ] **Step 3: Final commit**

```bash
git add skills/nextjs-starter/SKILL.md
git commit -m "chore: polish skill - final review pass"
```
