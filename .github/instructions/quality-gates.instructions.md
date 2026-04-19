---
applyTo: "**"
description: "Enforce quality gates: pre-commit must pass, unit tests must pass, and README.md must be updated once work is confirmed working."
---

# Quality Gates — Non-Negotiable Rules

These rules apply to **all agents, all tasks, all languages**. They are not optional.

---

## Gate 1 — Pre-Commit Must Pass Before Every Commit

Before running any `git commit`, always run pre-commit checks first:

```bash
# If the project uses the pre-commit framework
pre-commit run --all-files

# If the project uses a custom lint/format script, run it instead
# Check package.json scripts, Makefile, or project docs to find the right command
```

**If pre-commit fails:**
- Fix every reported issue before committing.
- Do NOT use `--no-verify` or skip hooks. Fix the root cause.
- If you cannot determine the fix, STOP and report the exact error to the user.

**If no pre-commit config exists in the project:**
- Run basic checks appropriate to the language (e.g., `pylint`/`ruff` for Python, `eslint` for JS/TS).
- Note the absence and recommend adding one.

---

## Gate 2 — All Unit Tests Must Pass Before Marking a Task Done

Never mark a task `[x]` in `progress.md` until tests pass.

```bash
# Python
pytest

# JavaScript / TypeScript
npm test

# Other — check Makefile, package.json scripts, or project docs
```

**Rules:**
- Run the full test suite, not just tests for the files you changed.
- Zero test failures allowed. If tests were already broken before your change, fix them or explicitly document the pre-existing failure before proceeding.
- If the task adds new functionality, write or update tests for it first (TDD where possible).
- Only after `All tests passed` (or equivalent) may you mark the task complete.

---

## Gate 3 — One README.md, Updated When Work Is Confirmed Working

There is **exactly one** `README.md` at the repository root. It is the single source of truth for what the project does and how to use it.

**Update `README.md` when all of the following are true:**
1. All tasks in `progress.md` are marked `[x]` (or the current milestone is fully done).
2. Pre-commit passes clean.
3. All unit tests pass.

**What to update in `README.md`:**
- Document any new features, commands, agents, prompts, skills, or config options added.
- Update the file structure diagram if new files were added.
- Update any "Quick Start" steps that changed.
- Remove or correct any documentation that is now outdated.

**Rules:**
- Do NOT create additional markdown files to document the same project (e.g., `OVERVIEW.md`, `DOCS.md`) — put it all in `README.md`.
- Do NOT update `README.md` mid-task while work is still in progress or tests are failing.
- Keep the README accurate: if you remove a feature, remove it from the README too.

---

## Enforcement Summary

| Gate | When | Blocker? |
|------|------|----------|
| Pre-commit passes | Before every `git commit` | Yes — do not commit if failing |
| Unit tests pass | Before marking any task `[x]` | Yes — do not mark done if failing |
| README.md updated | After all confirmed-working | Yes — do not close a milestone with stale docs |
