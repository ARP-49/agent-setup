---
description: "Implement the next pending task from progress.md. Uses Haiku CodeCraft agent for fast, precise execution."
agent: Haiku CodeCraft
---

# /implement — Execute Next Task

You are being invoked as a **sub-agent** to implement exactly ONE task. You are context-isolated — you know nothing about previous work except what's in the codebase and the files referenced below.

## Startup Sequence (mandatory, in order)

1. **Read `progress.md`** — Find the first unchecked `[ ]` task
2. **Read the PRD** referenced in `progress.md` — Find your task's full specification under `## Task Details`
3. **Read `.github/copilot-instructions.md`** — Project coding standards
4. **Read relevant instruction files** — Check `.github/instructions/*.instructions.md` for any guidance on languages/frameworks touched by this task

## Execution Rules

- **One task only.** Complete the first unchecked task. Do not start the next one.
- **Stay in scope.** Only touch files listed in the task spec. Do not refactor unrelated code.
- **Follow existing patterns.** Read the reference files mentioned in "Implementation Notes" before writing code.
- **Verify everything.** Run through the task's `## Verify` checklist. Check TypeScript compilation. Check for errors.
- **Mark complete.** Change `[ ]` to `[x]` for your task in `progress.md`.
- **Update status.** Set `## Status → Current` to the next pending task (or "All complete").
- **Report.** State what you did in 1-3 sentences: files touched, build status.

## If Something Is Wrong

- **Task spec is ambiguous?** Check the codebase for existing patterns. State your assumption and proceed.
- **Dependency not met?** If a prior task's output is missing from the codebase, STOP. Report: "Blocked: Task <N> output not found at `path`."
- **Build breaks?** Fix it. Don't leave broken code. If the fix is outside your task scope, report it but still fix compilation.

## Context From User

{{{ input }}}

If no specific task is mentioned above, execute the **next unchecked task** from `progress.md`.
