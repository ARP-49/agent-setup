# agent-setup

> Plug-and-play VS Code Copilot configuration -- custom agents, smart prompts, reusable skills, and coding instructions, all in `.github/`.

## What Is This?

This repo is a portable VS Code GitHub Copilot configuration kit. Copy the `.github/` folder into any project and instantly gain a structured multi-agent AI development system: specialized coding agents, reusable prompt workflows, file-scoped instruction sets, and composable skills for common engineering tasks.

The system uses a **three-layer architecture**:

- **Layer 1 -- Foundation**: Instruction files (`.github/instructions/`) that apply automatically to matching file types via `applyTo` glob patterns in their YAML frontmatter. They shape all agent behavior without requiring explicit invocation.
- **Layer 2 -- Specialists**: Agent persona files (`.github/agents/`) with defined models, roles, and tool access. Invoked by name in Copilot Chat.
- **Layer 3 -- Capabilities**: Slash-command prompts (`.github/prompts/`) and multi-step skill workflows (`.github/skills/`) for common engineering tasks.

All three layers work together through VS Code's built-in Copilot Chat.

## Quick Start

1. Copy `.github/` from this repo into the root of your project.
2. Open your project in VS Code with GitHub Copilot (Chat) enabled (requires VS Code 1.99+ for full prompt/agent support).
3. In Copilot Chat, type `@` to invoke an agent by name -- e.g., `@Codex Planner plan a caching layer`.
4. Type `/` to browse available slash-command prompts -- e.g., `/plan`, `/implement`, `/gpt-dev-team`.
5. Customize `.github/instructions/*.instructions.md` for your project's language, style, and conventions.

## File Structure

```text
.github/
  agents/                                         # Layer 2: Specialist agent personas
    Codex-Planner.agent.md                        #   o3 -- strategic planner (GPT stack)
    GPT-41-Coder.agent.md                         #   GPT-4.1 -- fast implementer (GPT stack)
    Haiku-CodeCraft.agent.md                      #   Claude Haiku 4.5 -- fast implementer (Claude stack)
    Opus-Architect.agent.md                       #   Claude Opus 4.6 -- strategic planner (Claude stack)
    python-mcp-expert.agent.md                    #   Claude Opus 4.6 -- Python MCP server builder
  prompts/                                        # Layer 3: Slash-command prompt workflows
    claude-dev-team.prompt.md                     #   Orchestrate full Claude dev-team pipeline
    gpt-dev-team.prompt.md                        #   Orchestrate full GPT dev-team pipeline
    implement.prompt.md                           #   Execute next task from progress.md
    orchestrate.prompt.md                         #   Full autopilot: plan then implement
    plan.prompt.md                                #   Strategic planning -- PRD + progress.md
    pytest-coverage.prompt.md                     #   Run pytest to 100% line coverage
    python-mcp-server-generator.prompt.md         #   Scaffold a Python MCP server project
    remember.prompt.md                            #   Save lessons to domain memory files
    sql-code-review.prompt.md                     #   SQL security + quality review
    sql-optimization.prompt.md                    #   SQL performance optimization
  instructions/                                   # Layer 1: Auto-applied coding rules
    ai-prompt-engineering-safety-best-practices.instructions.md   # Prompt safety + responsible AI
    github-actions-ci-cd-best-practices.instructions.md           # CI/CD pipeline conventions
    python.instructions.md                                        # Python style + testing rules
    taming-copilot.instructions.md                                # Keeps Copilot focused + surgical
  skills/                                         # Layer 3: Multi-step reusable capability workflows
    agentic-eval/                                 #   Iterative evaluation + refinement loops
    git-commit/                                   #   Conventional commit message generation
    prd/                                          #   Product Requirements Document generation
    refactor/                                     #   Surgical code refactoring
    security-review/                              #   AI-powered security vulnerability scan
README.md
```

## Agents

> Agents are declared in `.github/agents/`. Invoke them in Copilot Chat with `@<agent-name>` or automatically via an orchestrating prompt.

| Agent | Model | Role |
|---|---|---|
| Codex Planner | o3 (Codex Max) | Strategic planner -- decomposes features into PRDs + task lists (GPT stack) |
| GPT-41 Coder | GPT-4.1 | Fast implementer -- one task at a time, marks complete, stops |
| Haiku CodeCraft | Claude Haiku 4.5 | Fast implementer -- pairs with Opus Architect (Claude stack) |
| Opus Architect | Claude Opus 4.6 | Strategic planner + validator + reviewer (Claude stack) |
| Python MCP Expert | Claude Opus 4.6 | Specialist for Python MCP server development |

### Codex Planner

- **File**: `.github/agents/Codex-Planner.agent.md`
- **Model**: o3 (Codex Max)
- **Role**: Planning brain of the GPT dev team. Analyzes requirements, explores the codebase, and decomposes features into surgical, context-isolated tasks (max 3 files each). Outputs `PRD.md` and `progress.md` under `./agentic/features/<feature>/`. Never writes production code.
- **Invoke**: `@Codex Planner <request>` directly, or automatically via `/gpt-dev-team` (Plan phase).

### GPT-41 Coder

- **File**: `.github/agents/GPT-41-Coder.agent.md`
- **Model**: GPT-4.1
- **Role**: Implementation sub-agent for the GPT stack. Reads `progress.md`, executes exactly one unchecked task, verifies it, marks `[x]`, then stops. Enforces strict TypeScript types, no `any`, minimal code.
- **Invoke**: `/implement` prompt, or automatically via `/gpt-dev-team` (Execute phase).

### Haiku CodeCraft

- **File**: `.github/agents/Haiku-CodeCraft.agent.md`
- **Model**: Claude Haiku 4.5
- **Role**: Fast implementation sub-agent for the Claude stack. Identical single-task execution protocol to GPT-41 Coder. Strictly forbids emojis and decorative Unicode in production code (ASCII only enforced).
- **Invoke**: `/implement` prompt, or automatically via `/claude-dev-team` (Execute phase).

### Opus Architect

- **File**: `.github/agents/Opus-Architect.agent.md`
- **Model**: Claude Opus 4.6
- **Role**: Strategic planner for the Claude stack. Produces PRDs and progress files. Also acts as Validator and Reviewer in the dev-team pipeline. Enforces a single-README documentation rule: all docs must go to root `README.md` only.
- **Invoke**: `/plan` prompt, or automatically via `/claude-dev-team` (Plan, Validate, Review phases).

### Python MCP Expert

- **File**: `.github/agents/python-mcp-expert.agent.md`
- **Model**: Claude Opus 4.6
- **Role**: Specialist for building Model Context Protocol servers in Python. Uses FastMCP by default; drops to low-level `Server` only when needed. Requires complete type hints (they drive schema generation). Returns structured Pydantic/TypedDict output. Supports stdio and streamable-HTTP transports.
- **Invoke**: `/python-mcp-server-generator` prompt, or `@Python MCP Server Expert <request>` directly.

---

## Prompts

> Prompts are slash-command workflows in `.github/prompts/`. In Copilot Chat, type `/` to browse available prompts (requires VS Code 1.99+).

### Orchestration

Full multi-agent dev-team pipelines with branching, planning, execution, validation, and review.

| Prompt | Description |
|---|---|
| `/claude-dev-team` | Orchestrates the Claude stack (Opus Architect + Haiku CodeCraft). Runs a 6-phase pipeline: Understand, Branch, Plan, Execute, Validate, Review. Supports cleanup/refactor mode via Janitor agent. |
| `/gpt-dev-team` | Orchestrates the GPT stack (Codex Planner + GPT-41 Coder). Same 6-phase pipeline structure. |
| `/orchestrate` | Full autopilot in a single prompt -- plans (Opus-style), then spawns Haiku-style sub-agents per task sequentially. No `agent:` tag, so `runSubagent` is available. |

### Planning and Implementation

| Prompt | Description |
|---|---|
| `/plan` | Strategic planning only. Reads `progress.md` and any existing PRD, explores the codebase, produces a new `PRD.md` + `progress.md`. Uses Opus Architect. |
| `/implement` | Executes the next unchecked task from `progress.md`. Reads the task spec from the PRD, implements, verifies, marks `[x]`, updates status, then stops. Uses Haiku CodeCraft. |

### Testing and Quality

| Prompt | Description |
|---|---|
| `/pytest-coverage` | Runs `pytest --cov --cov-report=annotate:cov_annotate`, identifies uncovered lines, writes tests for them, and iterates until 100% line coverage is achieved. |
| `/sql-code-review` | Comprehensive SQL review: injection prevention, parameterized queries, access control, N+1 detection, naming conventions, and anti-pattern detection. Works across MySQL, PostgreSQL, SQL Server, and Oracle. |
| `/sql-optimization` | SQL performance optimization: query structure, index strategy, JOIN ordering, cursor-based pagination, aggregation patterns, and batch operations. |

### Utilities

| Prompt | Description |
|---|---|
| `/python-mcp-server-generator` | Scaffolds a complete Python MCP server project using `uv`. Generates a FastMCP server with typed tools, error handling, and testing instructions. |
| `/remember` | Saves a lesson learned into a domain-organized memory instruction file. Syntax: `/remember [>domain [scope]] lesson`. Scope: `global` (default, cross-project) or `workspace`/`ws` (current project only). |

---

## Instructions

> Instruction files in `.github/instructions/` are automatically injected into Copilot's context for matching files via `applyTo` glob patterns in their YAML frontmatter. They shape all agent and prompt behavior without requiring explicit invocation.

| File | Applies To | Purpose |
|---|---|---|
| `ai-prompt-engineering-safety-best-practices.instructions.md` | All files | Prompt design principles, safety checklists, bias mitigation, responsible AI usage, prompt injection prevention, and data privacy guidelines. |
| `github-actions-ci-cd-best-practices.instructions.md` | All files | CI/CD pipeline structure, caching strategies, matrix builds, deployment best practices, and GitHub Actions workflow conventions. |
| `python.instructions.md` | `**/*.py` | Python conventions: PEP 8 style, type hints, pytest patterns, error handling, async/await usage, and project structure. |
| `taming-copilot.instructions.md` | All files | Keeps Copilot focused and surgical: code only when explicitly asked, preserve existing code, no unsolicited refactoring, direct file edits over copy-paste code blocks. |

---

## Skills

> Skills in `.github/skills/` are multi-step workflows packaged as `SKILL.md` files. Reference a skill by name in any agent or prompt conversation -- e.g., "use the security-review skill on `src/auth/`" -- and the agent will load and follow it.

| Skill | Directory | When to Use | Summary |
|---|---|---|---|
| `agentic-eval` | `.github/skills/agentic-eval/` | Quality-critical output needing iterative refinement | Implements Generate-Evaluate-Critique-Refine loops. Includes basic reflection, evaluator-optimizer separation, code-specific test-driven refinement, and LLM-as-judge patterns. |
| `git-commit` | `.github/skills/git-commit/` | Committing changes with a clean, conventional message | Analyzes `git diff --staged`, determines commit type and scope, stages files intelligently, generates a Conventional Commits message, and executes the commit. |
| `prd` | `.github/skills/prd/` | Starting a new feature or product cycle | Interviews the user, scopes the work, and produces a structured PRD with executive summary, user stories, acceptance criteria, architecture overview, tech specs, and risk analysis. |
| `refactor` | `.github/skills/refactor/` | Improving code structure without changing behavior | Identifies and fixes code smells (long methods, duplication, god classes, magic numbers, nested conditionals, etc.), applies design patterns, and preserves existing test coverage. |
| `security-review` | `.github/skills/security-review/` | Security audit before shipping | 8-step review: scope resolution, dependency audit, secrets scan, OWASP vulnerability scan, cross-file data-flow analysis, self-verification, severity-graded report, and concrete patch proposals. |

---

## Dev Team Workflow

Both `/claude-dev-team` and `/gpt-dev-team` run the same 6-phase pipeline. The only difference is which agents fill each role.

```text
Phase 0: Understand
  Explore codebase, detect mode (feature build vs. cleanup/refactor), ask if anything is unclear.

Phase 1: Branch
  git checkout -b <feat|fix|refactor|cleanup|chore>/<short-description>

Phase 2: Plan
  Spawn Planner agent.
  Output: ./agentic/features/<feature>/PRD.md + progress.md
  Rules: max 3 files per task, explicit dependencies, verification criteria per task.

Phase 3: Execute
  Spawn one Implementer sub-agent per task (or Janitor sub-agent in cleanup/refactor mode).
  Each sub-agent: implement -> verify -> mark [x] in progress.md -> stop.

Phase 4: Validate  (after every 3-5 tasks)
  Spawn Validator agent.
  Checks implementation against PRD, runs tests, flags regressions.

Phase 5: Review and Merge
  Spawn Reviewer agent.
  Returns APPROVE or REQUEST_CHANGES -> fix via Implementer -> re-review -> merge or open PR.
```

Agent assignments by stack:

| Phase | `/claude-dev-team` | `/gpt-dev-team` |
|---|---|---|
| Plan | Opus Architect | Codex Planner |
| Implement | Haiku CodeCraft | GPT-41 Coder |
| Validate | Opus Architect | Codex Planner |
| Review | Opus Architect | Codex Planner |

Feature artifacts always live in `./agentic/features/<feature>/PRD.md` and `./agentic/features/<feature>/progress.md`.

Key pipeline rules:

- The orchestrator never writes code -- it always spawns a sub-agent
- One sub-agent per task (fresh context, no shared state between agents)
- Validate after every 3-5 tasks to catch regressions early
- All documentation updates target root `README.md` only (no fragmented sub-READMEs)
- Cleanup/refactor mode: Janitor agent handles implementation, Validator checks after every 2-3 tasks

---

## How to Invoke

**Invoke an agent directly**

```text
@Codex Planner  plan a Redis caching layer for the API
@Opus Architect  review the auth service architecture
@Python MCP Expert  create an MCP server that queries a SQLite database
```

**Run a slash-command prompt**

Type `/` in Copilot Chat to browse prompts, or type the full name:

```text
/plan        add user authentication with JWT
/implement
/orchestrate refactor the payment service to use the repository pattern
```

**Run the full dev-team pipeline**

```text
/claude-dev-team   add rate-limiting middleware to the Express API
/gpt-dev-team      fix the N+1 query in the orders endpoint
```

**Invoke a skill**

Mention it in any agent conversation:

```text
@Opus Architect  run a security review on src/api/ using the security-review skill
@Haiku CodeCraft  use the git-commit skill to commit staged changes
```

**Save a lesson to memory**

```text
/remember  always use cursor-based pagination for large datasets
/remember  >python ws  prefer dataclasses over TypedDict for mutable models
```
