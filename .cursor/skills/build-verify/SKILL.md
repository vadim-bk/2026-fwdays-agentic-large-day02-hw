---
name: build-verify
description: Runs `yarn build` and `yarn test:typecheck` at the Excalidraw monorepo root after code changes, fixes compilation/type errors without ts-ignore or test hacks, and reports status with fixes and full output. Use when the user asks to build, verify, or check compilation, or after edits that might affect compilation.
---

# Skill: Build & Verify

## When to use

After making code changes that might affect compilation.
Triggered by: "build", "verify", "check compilation".

## Inputs

- Changed files (from git diff or conversation context)

## Steps

Work from the **Excalidraw monorepo root** (library packages under `packages/*`, app under `excalidraw-app/`).

1. Run `yarn build` in the project root.
2. Run `yarn test:typecheck` in the project root (root `tsc` over the workspace — required for every verification pass, not only when the bundler fails).
3. If **both** succeed, report success and list changed files.
4. If **`yarn build` fails** or **`yarn test:typecheck` fails**:
   a. Read error output; identify file, line, and error type.
   b. Open the file at the error line.
   c. **Mandatory after (a) and (b) when `yarn build` failed:** run `yarn test:typecheck` and use **both** build and `tsc` output to decide fixes (bundler and TypeScript can surface different errors).
   d. **High-impact paths** (fix root cause here first when errors touch them; then re-run the full check pair in step f): `packages/excalidraw/` (notably `renderer/`, `scene/`, `data/`, `components/`), `packages/element/`, `packages/common/`, `packages/math/`, `packages/utils/`, and `excalidraw-app/`. This repo has no top-level `packages/core` or `src/components`; treat those names as “core package / app UI” analogs only.
   e. Fix the issue (type error, missing import, syntax) without `@ts-ignore` or `any`.
   f. Re-run **`yarn build`**, then **`yarn test:typecheck`**. **Each fix attempt must end with both commands succeeding** before you report success; do not report pass on build alone.
   g. Repeat from (a) through (f) up to **3 attempts**. If either command still fails after 3 attempts, stop and report with full output from both.

## Outputs

- `yarn build` status: PASS / FAIL
- `yarn test:typecheck` status: PASS / FAIL
- List of fixes applied (if any)
- Full output from the last run of each command

## Safety

- Do NOT fix errors by adding `@ts-ignore` or `any`
- Do NOT modify test files to fix build errors
- If 3 attempts fail (after running **both** `yarn build` and `yarn test:typecheck` each time), stop and report to the user
