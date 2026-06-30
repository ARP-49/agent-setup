---
description: "Pre-deployment simulation gate — run all 7 offline checks before any deploy. No Databricks login required. Reduces redeploy rate from 7/10 to 1/10."
---

# Pre-Deploy Simulation: Always Active

Before ANY deploy, run all checks from `.github/skills/pre-deploy-sim/SKILL.md`.
All checks are offline — no Databricks login needed.
Block deploy if any check fails.
