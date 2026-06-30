---
name: pre-deploy-sim
description: Pre-deployment simulation. Runs lint, type-check, tests, import validation, env var audit, secrets audit, and Databricks config review locally before any deploy. Requires no active Databricks login. Reduces redeploy rate from 7/10 to 1/10. Use when about to deploy, push feature branch, run databricks bundle deploy, or user says "simulate", "pre-deploy", "dry run", "will this work", "check before deploy", "pre-sim".
---

# Pre-Deploy Simulation

## Purpose

Run before every deploy. Each check = one class of prod failure eliminated.
Target: 1/10 redeploy rate.

## Checklist

### 1. Lint
```bash
ruff check src/ tests/
```
Zero errors. Zero warnings.

### 2. Type Check
```bash
mypy src/ --ignore-missing-imports
```
Zero type errors.

### 3. Tests
```bash
pytest tests/ -x -q
```
All pass. `-x` = stop first failure.

### 4. Import Validation
```bash
python -c "import orchestrator; import models; import agents.md_generator; import agents.confluence_publisher; import agents.codebase_reader; print('OK')"
```
No ImportError = deps installed + no circular imports.

### 5. Env Var Audit
```bash
grep -rn "os\.environ\|os\.getenv" src/
```
Each var found → verify set in job config or Databricks secret scope.
Missing = silent crash at runtime.

### 6. Secrets Audit
For every `w.secrets.get_secret(scope=..., key=...)` call:
- [ ] Scope exists in workspace
- [ ] Key name exact match (case-sensitive)
- [ ] Service principal has READ on scope

### 7. Databricks Config Static Review
No login required. Review offline:
- `databricks.yml` — target, job names, task keys, python_file paths all present and correct?
- `resources/jobs.yml` — all `{{job.parameters.*}}` match fields in `JobParameters`?
- Cluster/environment config present and valid (runtime version, env vars set)?
- Python file paths relative to bundle root — do they exist on disk?

Read `.github/skills/databricks-bundles/SKILL.md` for bundle schema rules.
Read `.github/skills/databricks-jobs/SKILL.md` for job config rules.
Flag any mismatch as RED before touching `databricks bundle deploy`.

### 8. Execution Trace
Walk the critical path mentally:
1. Entry args → all required params present?
2. Each fn call → what raises?
3. External calls (API, secrets, volumes) → fail gracefully?
4. Outputs (volume writes, Confluence) → permissions OK?

Flag gaps before deploying.

## Pass Gate

All 8 checks green → deploy.
Any red → fix first. No exceptions.
