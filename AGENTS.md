# AGENTS.md

## Project Overview

This repository is the **Excalidraw** monorepo: a canvas-based diagramming product shipped as the **`@excalidraw/excalidraw`** React library plus the **`excalidraw-app`** web client and shared packages (`common`, `element`, `math`, `utils`). Agents should treat library boundaries seriously—editor behavior lives mainly under `packages/excalidraw/`, product-specific shell under `excalidraw-app/`, and cross-cutting rules in `.cursor/rules/*.mdc`.

## Tech Stack

- **Languages**: TypeScript (strict), modern JavaScript where legacy scripts remain
- **UI**: React 19 (library + app)
- **Package manager**: Yarn **1.22.22** workspaces (`package.json` → `workspaces`)
- **Build**: **esbuild** for publishable packages; **Vite** for `excalidraw-app`
- **Tests**: **Vitest** (`vitest.config.mts` at repo root)
- **Quality**: **ESLint**, **Prettier**, **tsc** (`yarn test:typecheck`)
- **Rendering**: HTML5 **Canvas 2D** in the editor pipeline—not React DOM or Konva/Fabric/Pixi for scene drawing (see `.cursor/rules/architecture.mdc`)

## Project Structure

Excalidraw is a **monorepo** with a clear separation between the core library and the application:

- **`packages/excalidraw/`** - Main React component library published to npm as `@excalidraw/excalidraw`
- **`excalidraw-app/`** - Full-featured web application (excalidraw.com) that uses the library
- **`packages/`** - Core packages: `@excalidraw/common`, `@excalidraw/element`, `@excalidraw/math`, `@excalidraw/utils`
- **`examples/`** - Integration examples (NextJS, browser script)

## Development Workflow

1. **Package Development**: Work in `packages/*` for editor features
2. **App Development**: Work in `excalidraw-app/` for app-specific features
3. **Testing**: Run `yarn test:typecheck` and prefer `yarn test:all` before commit for the full gate; use `yarn test:update` only when snapshot updates are intentional (see `.cursor/rules/testing.mdc`)
4. **Type Safety**: Use `yarn test:typecheck` to verify TypeScript

## Development Commands (key commands)


```bash
yarn build           # Production build (via excalidraw-app)
yarn start           # Dev server (excalidraw-app)
yarn test:typecheck  # TypeScript type checking
yarn test:all        # Full gate: typecheck, lint, prettier, tests (no watch)
yarn test:app --watch=false  # Tests once, no snapshot updates
yarn test:update     # Refresh snapshots when outputs changed intentionally
yarn fix             # Auto-fix formatting and linting issues
```


## Architecture

### Package System

- Uses Yarn workspaces for monorepo management
- Internal packages use path aliases (see `vitest.config.mts`)
- Build system uses esbuild for packages, Vite for the app
- TypeScript throughout with strict configuration
- Editor state flows through **`actionManager.dispatch()`** in `packages/excalidraw` (not Redux/Zustand); canvas rendering via `renderStaticScene` / `renderInteractiveScene` with typed `renderConfig` (see `.cursor/rules/architecture.mdc`)

## Conventions

- **Branching**: Use feature branches; target **`master`** for PRs (CI runs on push to `master`—see `docs/technical/dev-setup.md`)
- **Commits**: Short, imperative subject lines; keep commits scoped and reviewable
- **Code style**: Functional components + hooks, named exports, props as `type {ComponentName}Props`, kebab-case for utilities / PascalCase for components—details in `.cursor/rules/conventions.mdc`
- **Testing & linting**: Match `.cursor/rules/testing.mdc`; do not use `@ts-ignore` or `any` to silence real issues; run `yarn fix` when appropriate before pushing
- **Dependencies**: No new npm packages without explicit approval (`.cursor/rules/architecture.mdc` / security rules)

## Do-Not-Touch / Constraints

**Protected files** (do **not** change without explicit approval, full dependency understanding, full test suite + manual QA—see `.cursor/rules/do-not-touch.mdc`):

- `packages/excalidraw/scene/Renderer.ts` — render pipeline
- `packages/excalidraw/data/restore.ts` — file format compatibility
- `packages/excalidraw/actions/manager.tsx` — action system
- `packages/excalidraw/types.ts` — core types

**Other constraints**: follow `.cursor/rules/security.mdc` for URLs, embeds, secrets, and supply chain; do not relax iframe sandbox or add unvetted dependencies. Before commit, confirm `git diff` does not touch protected paths unless that change was explicitly approved.

## Documentation

- Product docs: `docs/product/PRD.md`, `docs/product/domain-glossary.md`
- Technical docs: `docs/technical/architecture.md`, `docs/technical/dev-setup.md`
- Project memory docs: `docs/memory/projectbrief.md`, `docs/memory/productContext.md`, `docs/memory/techContext.md`, `docs/memory/systemPatterns.md`, `docs/memory/activeContext.md`, `docs/memory/progress.md`, `docs/memory/decisionLog.md`

## Memory Bank Pattern

- Treat `docs/memory/` as the project Memory Bank and keep it current.
- After each project change (feature, fix, refactor, docs, or architecture update), update the relevant Memory Bank files in the same work session.
- Record decisions in `docs/memory/decisionLog.md` and progress updates in `docs/memory/progress.md`.
- Keep `docs/memory/activeContext.md` focused on current priorities and next steps.
