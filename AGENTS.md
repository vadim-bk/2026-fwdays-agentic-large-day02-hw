# AGENTS.md

## Project Structure

Excalidraw is a **monorepo** with a clear separation between the core library and the application:

- **`packages/excalidraw/`** - Main React component library published to npm as `@excalidraw/excalidraw`
- **`excalidraw-app/`** - Full-featured web application (excalidraw.com) that uses the library
- **`packages/`** - Core packages: `@excalidraw/common`, `@excalidraw/element`, `@excalidraw/math`, `@excalidraw/utils`
- **`examples/`** - Integration examples (NextJS, browser script)

## Development Workflow

1. **Package Development**: Work in `packages/*` for editor features
2. **App Development**: Work in `excalidraw-app/` for app-specific features
3. **Testing**: Always run `yarn test:update` before committing
4. **Type Safety**: Use `yarn test:typecheck` to verify TypeScript

## Development Commands


```bash
yarn test:typecheck  # TypeScript type checking
yarn test:update     # Run all tests (with snapshot updates)
yarn fix             # Auto-fix formatting and linting issues
```


## Architecture Notes

### Package System

- Uses Yarn workspaces for monorepo management
- Internal packages use path aliases (see `vitest.config.mts`)
- Build system uses esbuild for packages, Vite for the app
- TypeScript throughout with strict configuration

## Documentation

- Product docs: `docs/product/PRD.md`, `docs/product/domain-glossary.md`
- Technical docs: `docs/technical/architecture.md`, `docs/technical/dev-setup.md`
- Project memory docs: `docs/memory/projectbrief.md`, `docs/memory/productContext.md`, `docs/memory/techContext.md`, `docs/memory/systemPatterns.md`, `docs/memory/activeContext.md`, `docs/memory/progress.md`, `docs/memory/decisionLog.md`

## Memory Bank Pattern

- Treat `docs/memory/` as the project Memory Bank and keep it current.
- After each project change (feature, fix, refactor, docs, or architecture update), update the relevant Memory Bank files in the same work session.
- Record decisions in `docs/memory/decisionLog.md` and progress updates in `docs/memory/progress.md`.
- Keep `docs/memory/activeContext.md` focused on current priorities and next steps.
