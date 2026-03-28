---
name: codebase-explorer
description: Explores unfamiliar areas of a codebase, maps key files and responsibilities, traces data flow, and identifies package dependencies. Use when the user asks to explore, investigate, or understand how a feature/module works.
---

# Codebase Explorer

## When to use

Use this skill when you need to understand an unfamiliar area of the codebase.

Common triggers:
- "explore"
- "investigate"
- "how does X work?"

## Inputs

- Area of interest (module, feature, file pattern)

## Workflow

1. Identify relevant directories and files using explicit paths (for example `@folder`) or broad codebase search.
2. Read local `README` files or top-level comments in the target area first.
3. Map key files and state each file's responsibility in one sentence.
4. Trace data flow end-to-end: entry point -> processing -> output.
5. Identify dependencies by checking imports from other internal packages/modules.
6. Validate findings against concrete code references, then write a concise summary.

## Output format

Provide:

- Purpose: what this area does
- Key files: file path + responsibility
- Data flow: entry point -> processing -> output
- Dependencies: internal/external modules used
- Related files: prioritized list for deeper investigation

## Safety

- READ-ONLY: do not modify files during exploration
- Base conclusions on code evidence, not assumptions
