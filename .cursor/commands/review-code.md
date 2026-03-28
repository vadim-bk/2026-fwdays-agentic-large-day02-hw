# Review code

Perform a structured code review of the **current change set** (git diff, open files, or whatever the user indicates).

## Scope

- Prefer `git diff` / staged changes when available; otherwise review the files or feature the user names.
- Respect **protected files** in `.cursor/rules/do-not-touch.mdc` — flag any edits there as high risk and require explicit approval rationale.
- Apply **security** guidance from `.cursor/rules/security.mdc` (secrets, URL sanitization, embeds, no new deps without approval).

## Checklist

1. **Correctness** — Logic matches intent; edge cases; error handling where appropriate.
2. **Architecture** — Excalidraw uses `actionManager` for state in the library; no Redux in `packages/excalidraw`. Canvas renders via the existing pipeline, not React DOM for drawing.
3. **Conventions** — `.cursor/rules/conventions.mdc`: functional components, `{Name}Props`, named exports, colocated `*.test.tsx`, strict TypeScript (no careless `any` / `@ts-ignore`).
4. **Testing** — `.cursor/rules/testing.mdc`: Vitest, meaningful assertions; mention if `yarn test:typecheck` / `yarn test:update` should be run before merge.
5. **Diff quality** — Focused changes; no unrelated refactors unless necessary.

## Output

- **Summary** — Verdict in one short paragraph.
- **Findings** — Grouped by severity: **Blocker** / **Important** / **Minor** / **Suggestion**, each with file path and concrete fix or question.
- **Tests** — What to run and what might be missing.

If information is missing (e.g. no diff), say what you need and review what is available.
