---
name: memory-bank-update
description: Updates the Memory Bank with recent changes, ensuring all technical details are accurate and up to date. Use when the user asks to update the memory bank, sync documentation, or refresh project context.
---

# Skill: Memory Bank Update

## When to use

After significant code changes: new features, refactors,
architecture changes, dependency updates, or completed milestones.
Triggered by: "update memory bank", "sync docs",
"refresh project docs".

## Inputs

- What changed (from git diff, conversation, or user description)

## Steps

1. Identify what changed (prefer **staged + working tree** over history; do not use `HEAD~5` while there is an active diff):
   a. Run `git diff --staged --stat` and `git diff --stat` (unstaged vs index).
   b. If **either** output is **non-empty**, base the summary on current work: use the non-empty output(s), or a single `git diff --stat HEAD` if **both** are non-empty so staged and unstaged changes are not mixed up or omitted.
   c. **Only if both** (a) outputs are **empty** (clean index and working tree), fall back to `git diff --stat HEAD~5` for recent committed history.
2. Read relevant Memory Bank files in `docs/memory/`
3. For each changed area, update the matching file:
   - New feature -> `progress.md` + `activeContext.md`
   - Architecture change -> `systemPatterns.md` + `decisionLog.md`
   - Dependency change -> `techContext.md`
   - Scope change -> `projectbrief.md` + `productContext.md`
4. Verify updated content against actual source code
5. Ensure each file stays under 200 lines

## Outputs

- List of updated Memory Bank files
- Summary of what changed and why

## Safety

- Do NOT remove manually curated content without asking
- Do NOT add speculative information - only verified facts
- Do NOT exceed 200 lines per file - summarize if needed
- Verify ALL technical claims against actual code
