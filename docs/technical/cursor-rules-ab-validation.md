# A/B validation: Cursor rule scope (`.cursor/rules`)

**Date:** 2026-03-28  
**Rules under test:** `conventions.mdc` vs `testing.mdc` (both `alwaysApply: false`)

## Hypothesis

Project rules that use **file globs** attach only when matched files are in context. The **same user intent** can therefore receive **different** rule-backed guidance depending on whether work happens under `packages/**` vs `excalidraw-app/**`, and whether the active file is a test or implementation file.

## Method

1. Read each rule’s frontmatter (`description`, `globs`, `alwaysApply`).
2. Map globs to concrete paths in this repo (spot-check with file search).
3. For a fixed scenario, record **Variant A** vs **Variant B** outcomes: which rule content would Cursor include, and what an agent might say with vs without that content.

*Note:* Cursor’s exact injection behavior can vary by product version; this validation is **glob semantics + content gap analysis**, not a live UI experiment.

---

## Variant A — `conventions.mdc` in scope

**Rule config:** `globs: packages/**/*.ts,packages/**/*.tsx`, `alwaysApply: false`

**Simulated context:** Primary file is e.g. `packages/excalidraw/components/LayerUI.tsx`.

**Result (expected inclusion):** Yes — path matches `packages/**/*.tsx`.

**Effect on a typical task** (“add a component + test, align with repo style”):

- Explicit expectations: functional components + hooks, `{ComponentName}Props`, named exports only, colocated `ComponentName.test.tsx`, strict TS (`no any`, `no @ts-ignore`), kebab-case for non-component modules, PascalCase for components.
- Verification pointer: `yarn test:typecheck`, `yarn test:all` / `yarn fix`, cross-link to `testing.mdc` for when tests are warranted.

**Repo check:** Many `packages/excalidraw/**/*.tsx` files exist; globs clearly apply across the library package.

---

## Variant B — `conventions.mdc` out of scope (app-only context)

**Simulated context:** Primary file is e.g. `excalidraw-app/index.tsx` or another `excalidraw-app/**/*.tsx` file **without** a matching `packages/**` path.

**Result (expected inclusion):** No — `excalidraw-app/` is **not** under `packages/`, so this rule’s globs do not match.

**Effect on the same task:**

- The agent still sees **always-applied** rules and `AGENTS.md` (if indexed), which mention yarn workspaces, `yarn test:update`, `yarn test:typecheck`, and where app vs package code lives.
- **Gap vs Variant A:** No automatic inclusion of the detailed **naming/export/props** conventions from `conventions.mdc` unless duplicated elsewhere (they are **not** fully spelled out in `AGENTS.md`).
- **Mitigation in practice:** `testing.mdc` **does** attach when editing `excalidraw-app/tests/*.test.tsx` because its globs are `**/*.test.ts,**/*.test.tsx,vitest.config.mts` — so test workflow guidance still appears for app tests.

---

## Side-by-side: `testing.mdc` for app vs package tests

**Rule config:** `globs: **/*.test.ts,**/*.test.tsx,vitest.config.mts`, `alwaysApply: false`

| Location | Example | Matches `testing.mdc`? |
|----------|---------|-------------------------|
| App | `excalidraw-app/tests/collab.test.tsx` | Yes (`**/*.test.tsx`) |
| Package | `packages/excalidraw/components/FontPicker/FontPicker.test.tsx` | Yes |

**Result:** Variant A/B **split by folder** applies to `conventions.mdc`, not to `testing.mdc` — test files anywhere in the repo pull in the Vitest command table and quality bar.

---

## Conclusion

1. **Glob-scoped rules work as a filter:** `conventions.mdc` reliably targets **library/package** TypeScript but **not** `excalidraw-app/` sources, so A/B behavior is real: package work gets stricter, file-local convention text; app-only UI work does not get that rule unless the agent opens a matching file or the team duplicates guidance in an always-on rule or `AGENTS.md`.

2. **`testing.mdc` coverage is broader:** Because globs use `**/*.test.tsx`, app and package tests both attach the same testing workflow — good consistency for “what to run” and snapshot discipline.

3. **Recommendation:** If app code should follow the same component conventions as `packages/`, either narrow the gap by adding `excalidraw-app/**/*.tsx` to `conventions.mdc` globs, or document the key bullets in `AGENTS.md` / an always-applied rule so Variant B is not weaker than Variant A for style.

4. **Limitation:** This document validates **intent and path matching**; re-run with a live Cursor session (Composer vs Chat, included rules panel) if you need product-version-specific confirmation of injection.
