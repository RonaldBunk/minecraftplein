<!-- BEGIN:nextjs-agent-rules -->

## This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->

# Agent Guidelines (Codex)

## Overview & Tech Stack

- **Framework**: Next.js 16.4+ (App Router, Turbopack, React 19, React Compiler enabled)
- **Styling**: Tailwind CSS v4 (`@theme inline`, OKLCH variables), `tw-animate-css`
- **UI Components**: shadcn/ui (`base-luma` style, `@base-ui/react`, `@shadcn/react`, `@/components/ui/*`),
  `lucide-react`
- **Utilities**: `cn` helper in `@/lib/utils` (re-exports `cn`), Shiki highlighter in `@/lib/highlighter`
- **State & Data**: TanStack React Query v5 (`@tanstack/react-query`)
- **Backend / Database**: Supabase (Postgres 17 local; config in `supabase/config.toml`)
- **Tooling**: Biome (linter/formatter), TypeScript (strict, `@/*` alias), pnpm (v11)

## Key Commands

- **Dev Server**: `pnpm dev`
- **Build**: `pnpm build`
- **Lint**: `pnpm lint` (`biome check`)
- **Format**: `pnpm format` (`biome format --write`)
- **Database (Supabase)**: `npx supabase status` / `npx supabase start` / `npx supabase db diff`

## Token Efficiency & RTK (Rust Token Killer)

- **CLI Proxies**: RTK is installed. When running terminal commands, prefer `rtk` wrappers to filter noise and preserve
  context window tokens:
    - `rtk git status`, `rtk git diff`, `rtk git log`
    - `rtk pnpm build`, `rtk err pnpm lint`
    - `rtk find ...`, `rtk rg ...`, `rtk ls`
- **Fallback**: Use standard commands (`pnpm ...`, `git ...`) if `rtk` is unavailable or full uncompressed diagnostic
  logs are required.
- **Context Hygiene**: Avoid dumping large unindexed files into context; keep tool outputs dense and focused.

## Subagent Delegation

- **When to Use**: Spawn subagents selectively for parallel explorations, deep isolated searches across large modules,
  or separate verification tasks.
- **When to Avoid**: Do not spawn subagents for straightforward single-file edits, minor refactors, or sequential tasks.
  Conserve parent context.

## Architecture & Code Conventions

- **App Router (`src/app/`)**:
    - Default to Server Components; add `'use client'` only where state/effects/events are needed.
    - Next 16 typings: use `LayoutProps<"/">` for root layout; dynamic route `params` and `searchParams` are async
      `Promise` objects (React 19).
- **Components (`src/components/ui/`)**:
    - Composable UI primitives powered by `@base-ui/react` and `@shadcn/react`.
    - Always merge dynamic Tailwind classes using `cn(...)` from `@/lib/utils`.
- **Biome & Code Quality**:
    - Enforce 2-space indentation and clean import sorting.
    - Run `pnpm lint` and `pnpm build` to verify changes before concluding tasks.
