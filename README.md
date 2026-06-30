# agent-setup

> Plug-and-play AI coding agent configuration for VS Code (GitHub Copilot) and Claude Code CLI. Copy `.github/` and `.claude/` into any project and instantly gain a structured multi-agent development system.

## What Is This?

This repo is a portable AI coding agent configuration kit. It supports two tooling paths:

- **GitHub Copilot in VS Code** — via `.github/agents/`, `.github/prompts/`, `.github/instructions/`, `.github/skills/`
- **Claude Code CLI** — via `.claude/agents/` and `.claude/commands/`

Both paths share the same prompt library, skill library, and dev-team workflow philosophy: a Planner decomposes features into surgical tasks, an Implementer executes one task at a time, a Validator checks regressions, and a Reviewer approves before merge.

### Three-layer architecture (Copilot path)

- **Layer 1 — Foundation**: `.github/instructions/` files auto-apply to matching file types via `applyTo` glob patterns. They shape all agent behavior without explicit invocation.
- **Layer 2 — Specialists**: `.github/agents/` persona files with defined models, roles, and tool access. Invoked by `@name` in Copilot Chat.
- **Layer 3 — Capabilities**: `.github/prompts/` slash-command workflows and `.github/skills/` multi-step skill packs for common engineering tasks.

## Quick Start

### GitHub Copilot (VS Code)

1. Copy `.github/` into your project root.
2. Open VS Code with GitHub Copilot Chat enabled (requires VS Code 1.99+).
3. Type `@` in Copilot Chat to invoke an agent — e.g., `@Opus Architect plan a caching layer`.
4. Type `/` to browse slash-command prompts — e.g., `/plan`, `/implement`, `/claude-dev-team`.

### Claude Code CLI

1. Copy `.claude/` into your project root.
2. In Claude Code, type `/dev-team <request>` to run the full Claude dev-team pipeline.
3. Subagents (`haiku-code-craft`, `sonnet-architect`) are spawned automatically by the orchestrator.

## File Structure

```text
.claude/
  agents/
    haiku-code-craft.md          # Claude Code subagent: fast implementer (Haiku 4.5)
    sonnet-architect.md          # Claude Code subagent: planner + validator + reviewer (Sonnet)
  commands/
    dev-team.md                  # /dev-team slash command: full 6-phase pipeline in Claude Code

.github/
  agents/                        # Copilot Chat specialist agents
    Codex-Planner.agent.md       #   o3 — strategic planner (GPT stack)
    GPT-41-Coder.agent.md        #   GPT-4.1 — fast implementer (GPT stack)
    Haiku-CodeCraft.agent.md     #   Claude Haiku 4.5 — fast implementer (Claude stack)
    Opus-Architect.agent.md      #   Claude Opus 4.6 — planner + validator + reviewer (Claude stack)
    python-mcp-expert.agent.md   #   Claude Opus 4.6 — Python MCP server specialist

  instructions/                  # Auto-applied coding rules (Layer 1)
    ai-prompt-engineering-safety-best-practices.instructions.md
    github-actions-ci-cd-best-practices.instructions.md
    python.instructions.md
    tailwind-v4-vite.instructions.md
    taming-copilot.instructions.md
    typescript-5-es2022.instructions.md

  prompts/                       # Slash-command workflows (Layer 3)
    # Orchestration
    claude-dev-team.prompt.md    #   Full Claude pipeline (Opus + Haiku)
    dev-team-claude.prompt.md    #   Alias / extended variant
    gpt-dev-team.prompt.md       #   Full GPT pipeline (Codex + GPT-4.1)
    dev-team-gpt.prompt.md       #   Alias / extended variant
    orchestrate.prompt.md        #   Full autopilot: plan → implement in one prompt
    mle-team-claude.prompt.md    #   ML engineering team pipeline (Claude)
    # Planning and implementation
    plan.prompt.md
    plan-gpt.prompt.md
    implement.prompt.md
    implement-gpt.prompt.md
    # Agents
    mle-agent.prompt.md          #   ML engineering agent
    instruction-md-agent.prompt.md
    codebase-delta-reader-agent.prompt.md
    requirement-reader.agent.md
    confluence-md-publishing-agent.prompt.md
    pre-deploy-sim.prompt.md
    # Testing and quality
    pytest-coverage.prompt.md
    sql-code-review.prompt.md
    sql-optimization.prompt.md
    python-mcp-server-generator.prompt.md
    # Utilities
    remember.prompt.md
    canary-goose.prompt.md
    caveman-default.prompt.md

  skills/                        # Multi-step reusable capability workflows (Layer 3)
    # Generic engineering
    agentic-eval/                #   Generate-Evaluate-Critique-Refine loops
    canary-goose/                #   Goose persona for Claude Code sessions
    caveman/                     #   Terse/minimal token mode
    data-quality/                #   Data quality assessment patterns
    frontend-dev/                #   Frontend development workflows
    git-commit/                  #   Conventional commit generation
    grill-me/                    #   Clarifying question patterns
    handoff/                     #   Agent handoff protocols
    ml-patterns/                 #   ML engineering patterns
    ponytail/                    #   Laziness ladder: YAGNI → minimum that works
    pre-deploy-sim/              #   Pre-deployment simulation checklist
    prd/                         #   Product Requirements Document generation
    refactor/                    #   Surgical code refactoring
    security-review/             #   8-step security audit
    software-engineer/           #   General software engineering guidelines
    spark-python-data-source/    #   PySpark data source patterns
    tdd/                         #   Test-driven development workflow
    write-a-skill/               #   Scaffold a new SKILL.md
    TEMPLATE/                    #   Blank skill template

    # Databricks
    databricks-agent-bricks/
    databricks-ai-functions/
    databricks-aibi-dashboards/
    databricks-apps-python/
    databricks-bundles/
    databricks-config/
    databricks-dbsql/
    databricks-docs/
    databricks-execution-compute/
    databricks-genie/
    databricks-iceberg/
    databricks-jobs/
    databricks-lakebase-autoscale/
    databricks-lakebase-provisioned/
    databricks-metric-views/
    databricks-mlflow-evaluation/
    databricks-model-serving/
    databricks-python-sdk/
    databricks-spark-declarative-pipelines/
    databricks-spark-structured-streaming/
    databricks-synthetic-data-gen/
    databricks-unity-catalog/
    databricks-unstructured-pdf-generation/
    databricks-vector-search/
    databricks-zerobus-ingest/
```

---

## Agents

### Copilot Agents (`.github/agents/`)

Invoke in Copilot Chat with `@<name>`.

| Agent | Model | Role |
|---|---|---|
| Codex Planner | o3 (Codex Max) | Strategic planner — decomposes features into PRDs + task lists (GPT stack) |
| GPT-41 Coder | GPT-4.1 | Fast implementer — one task at a time, marks `[x]`, stops |
| Haiku CodeCraft | Claude Haiku 4.5 | Fast implementer — Claude stack equivalent of GPT-41 Coder |
| Opus Architect | Claude Opus 4.6 | Strategic planner + validator + reviewer (Claude stack) |
| Python MCP Expert | Claude Opus 4.6 | Python MCP server specialist — FastMCP by default |

### Claude Code Subagents (`.claude/agents/`)

Spawned automatically by the `/dev-team` command orchestrator. Not invoked directly.

| Subagent | Role |
|---|---|
| `haiku-code-craft` | Fast implementer — one task per spawn, reads `progress.md`, marks `[x]`, stops |
| `sonnet-architect` | Planner, validator, and reviewer — produces PRDs, validates implementations, approves PRs |

---

## Prompts

### Orchestration

| Prompt | Description |
|---|---|
| `/claude-dev-team` | Full Claude pipeline: Opus Architect plans, Haiku CodeCraft implements, 6-phase workflow |
| `/gpt-dev-team` | Full GPT pipeline: Codex Planner plans, GPT-4.1 implements, same 6-phase workflow |
| `/orchestrate` | Full autopilot in one prompt — plans then spawns sub-agents per task sequentially |
| `/mle-team-claude` | ML engineering team pipeline via Claude agents |

### Planning and Implementation

| Prompt | Description |
|---|---|
| `/plan` | Strategic planning only — produces `PRD.md` + `progress.md` under `./agentic/features/<feature>/` |
| `/plan-gpt` | GPT variant of `/plan` using Codex Planner |
| `/implement` | Executes next unchecked task from `progress.md`, marks `[x]`, stops |
| `/implement-gpt` | GPT variant of `/implement` |

### Specialist Agents

| Prompt | Description |
|---|---|
| `/mle-agent` | ML engineering agent — model development, evaluation, data pipelines |
| `/instruction-md-agent` | Creates `.instructions.md` files from observed coding patterns |
| `/codebase-delta-reader-agent` | Reads recent git changes and summarises what changed and why |
| `/requirement-reader` | Extracts and structures requirements from raw documents |
| `/confluence-md-publishing-agent` | Publishes markdown content to Confluence |
| `/pre-deploy-sim` | Pre-deployment simulation — checks environment, config, dependencies |

### Testing and Quality

| Prompt | Description |
|---|---|
| `/pytest-coverage` | Runs pytest to 100% line coverage iteratively |
| `/sql-code-review` | SQL security + quality review — injection, N+1, naming, anti-patterns |
| `/sql-optimization` | SQL performance — index strategy, JOIN ordering, pagination, batch ops |
| `/python-mcp-server-generator` | Scaffolds a FastMCP Python MCP server project |

### Utilities

| Prompt | Description |
|---|---|
| `/remember` | Saves a lesson to a domain memory instruction file |
| `/canary-goose` | Activates Goose persona for Claude Code sessions |
| `/caveman-default` | Activates terse/minimal-token mode |

---

## Instructions

Auto-applied to matching file types via `applyTo` glob in YAML frontmatter.

| File | Applies To | Purpose |
|---|---|---|
| `ai-prompt-engineering-safety-best-practices` | All files | Prompt safety, bias mitigation, responsible AI, injection prevention |
| `github-actions-ci-cd-best-practices` | All files | CI/CD pipeline structure, caching, matrix builds, deployment |
| `python` | `**/*.py` | PEP 8, type hints, pytest patterns, async/await, project structure |
| `tailwind-v4-vite` | `**/*.{ts,tsx,css}` | Tailwind v4 utility patterns, Vite config, component conventions |
| `taming-copilot` | All files | Keeps Copilot focused: code only when asked, no unsolicited refactoring |
| `typescript-5-es2022` | `**/*.{ts,tsx}` | Strict TypeScript 5, no `any`, ES2022 features, type-safe patterns |

---

## Skills

### Generic Engineering Skills

| Skill | When to Use |
|---|---|
| `agentic-eval` | Quality-critical output needing iterative refinement — Generate-Evaluate-Critique-Refine |
| `canary-goose` | Claude Code persona — addresses user as Goose, active by default in configured sessions |
| `caveman` | Terse mode — minimal tokens, no explanations, activate with "caveman mode" |
| `data-quality` | Data quality assessment and remediation workflows |
| `frontend-dev` | Frontend development with component design and styling workflows |
| `git-commit` | Conventional commit generation from staged diff |
| `grill-me` | Ask clarifying questions before implementing — avoids building the wrong thing |
| `handoff` | Agent handoff protocols for multi-agent sessions |
| `ml-patterns` | ML engineering patterns — model training, evaluation, feature pipelines |
| `ponytail` | Laziness ladder: YAGNI → reuse → stdlib → one-liner → minimum that works |
| `pre-deploy-sim` | Pre-deployment simulation checklist before pushing to prod |
| `prd` | Full PRD generation via user interview — exec summary, stories, acceptance criteria |
| `refactor` | Surgical refactoring — code smells, design patterns, preserve test coverage |
| `security-review` | 8-step security audit: deps, secrets, OWASP, data-flow, severity report |
| `software-engineer` | General software engineering principles and decision-making |
| `spark-python-data-source` | PySpark custom data source patterns — auth, partitioning, streaming |
| `tdd` | Test-driven development — red-green-refactor workflow |
| `write-a-skill` | Scaffold a new `SKILL.md` for any reusable workflow |

### Databricks Skills

| Skill | Domain |
|---|---|
| `databricks-agent-bricks` | Knowledge assistants and supervisor agent patterns |
| `databricks-ai-functions` | AI functions — `ai_query`, `ai_forecast`, document pipelines |
| `databricks-aibi-dashboards` | Lakeview dashboard JSON spec, widgets, filters, layout |
| `databricks-apps-python` | Databricks Apps — auth, deployment, Lakebase, MCP approach |
| `databricks-bundles` | Asset Bundles (DAB) — targets, variables, SDP guidance |
| `databricks-config` | Workspace config and environment setup |
| `databricks-dbsql` | SQL warehouses, materialized views, AI functions, scripting |
| `databricks-docs` | Navigating and reading Databricks documentation |
| `databricks-execution-compute` | Databricks Connect, serverless jobs, interactive clusters |
| `databricks-genie` | Genie spaces and conversation API |
| `databricks-iceberg` | Managed Iceberg, UniForm, REST catalog, Snowflake/external interop |
| `databricks-jobs` | Job task types, triggers, schedules, notifications |
| `databricks-lakebase-autoscale` | Lakebase autoscale — connections, operations, reverse ETL |
| `databricks-lakebase-provisioned` | Lakebase provisioned — connection patterns, reverse ETL |
| `databricks-metric-views` | Metric views — YAML reference and patterns |
| `databricks-mlflow-evaluation` | MLflow evaluation — datasets, scorers, judge alignment, traces |
| `databricks-model-serving` | Serving endpoints — deploy, query, logging, GenAI agents |
| `databricks-python-sdk` | Databricks SDK — auth, clusters, SQL, UC, Vector Search |
| `databricks-spark-declarative-pipelines` | DLT pipelines — Python and SQL, streaming, CDC, migration |
| `databricks-spark-structured-streaming` | Structured Streaming — Kafka, stateful ops, joins, best practices |
| `databricks-synthetic-data-gen` | Synthetic data generation patterns |
| `databricks-unity-catalog` | Unity Catalog — volumes, system tables, data profiling |
| `databricks-unstructured-pdf-generation` | PDF generation and unstructured data processing |
| `databricks-vector-search` | Vector Search — index types, search modes, end-to-end RAG |
| `databricks-zerobus-ingest` | Zerobus ingestion — setup, Python client, Protobuf, limits |

---

## Dev Team Workflow

Both `/claude-dev-team` and `/gpt-dev-team` (Copilot) and `/dev-team` (Claude Code) run the same 6-phase pipeline.

```text
Phase 0: Understand
  Explore codebase, detect mode (feature vs. cleanup/refactor), ask if anything is unclear.

Phase 1: Branch
  git checkout -b <feat|fix|refactor|cleanup|chore>/<short-description>

Phase 2: Plan
  Spawn Planner agent.
  Output: ./agentic/features/<feature>/PRD.md + progress.md
  Rules: max 3 files per task, explicit dependencies, verification criteria per task.

Phase 3: Execute
  Spawn one Implementer sub-agent per task (or Janitor in cleanup/refactor mode).
  Each: implement → verify → mark [x] in progress.md → stop.

Phase 4: Validate  (after every 3–5 tasks)
  Spawn Validator. Checks implementation vs PRD, runs tests, flags regressions.

Phase 5: Review and Merge
  Spawn Reviewer. Returns APPROVE or REQUEST_CHANGES → fix → re-review → merge or PR.
```

| Phase | Copilot `/claude-dev-team` | Copilot `/gpt-dev-team` | Claude Code `/dev-team` |
|---|---|---|---|
| Plan | Opus Architect | Codex Planner | sonnet-architect |
| Implement | Haiku CodeCraft | GPT-41 Coder | haiku-code-craft |
| Validate | Opus Architect | Codex Planner | sonnet-architect |
| Review | Opus Architect | Codex Planner | sonnet-architect |

Key rules:
- Orchestrator never writes code — always spawns a sub-agent
- One sub-agent per task (fresh context, no shared state)
- Validate after every 3–5 tasks to catch regressions early
- All documentation goes to root `README.md` only

---

## How to Invoke

**Copilot Chat — agent by name**
```text
@Opus Architect   plan a Redis caching layer for the API
@Haiku CodeCraft  implement task 3 from progress.md
@Python MCP Expert  create an MCP server that queries SQLite
```

**Copilot Chat — slash-command prompt**
```text
/plan        add user authentication with JWT
/implement
/claude-dev-team   add rate-limiting middleware to the Express API
/gpt-dev-team      fix the N+1 query in the orders endpoint
```

**Claude Code CLI — slash command**
```text
/dev-team   add rate-limiting middleware to the Express API
/dev-team   refactor the payment service to the repository pattern
```

**Skills — mention in any conversation**
```text
@Opus Architect  run a security review on src/api/ using the security-review skill
@Haiku CodeCraft  use the git-commit skill to commit staged changes
use the databricks-vector-search skill to set up end-to-end RAG
```

**Save a lesson to memory**
```text
/remember  always use cursor-based pagination for large datasets
/remember  >python ws  prefer dataclasses over TypedDict for mutable models
```
