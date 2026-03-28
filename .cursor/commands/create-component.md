# Create component

Implement a **new React component** for this Excalidraw monorepo based on the user’s description (name, purpose, location, props).

## Placement

- **Library UI** (`@excalidraw/excalidraw`) → `packages/excalidraw/components/` (or the closest existing pattern in that tree).
- **App-only UI** → `excalidraw-app/` following existing layout there.

Do not touch protected files listed in `.cursor/rules/do-not-touch.mdc` unless the user explicitly requires it and accepts the review/QA bar.

## Conventions (must follow)

From `.cursor/rules/conventions.mdc` and `AGENTS.md`:

- Functional component + hooks only; **named export**; props type `{ComponentName}Props`.
- File name: **PascalCase** for the component file (e.g. `MyWidget.tsx`).
- Colocate tests: `ComponentName.test.tsx` next to the component when adding behavior worth testing.
- TypeScript strict: prefer `import type { ... }`; avoid `any` and `@ts-ignore`.
- **No new npm packages** without explicit approval (see `.cursor/rules/architecture.mdc` / security rules).

## State and data

- Library editor state: updates go through **`actionManager.dispatch()`** — not Redux/Zustand in `packages/excalidraw`.
- Reuse existing components, hooks, and styling patterns from neighboring files (spacing, CSS modules/classes, i18n keys if the area uses them).

## Verification

After implementation, run **`yarn test:typecheck`**; run **`yarn test:update`** or targeted tests if the project’s workflow applies (see `.cursor/rules/testing.mdc`).

## Ask if unclear

If the user did not specify: public API (props), where it mounts, accessibility expectations, or translations — ask briefly before coding.
