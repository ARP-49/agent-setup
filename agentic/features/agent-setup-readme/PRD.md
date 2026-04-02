# Feature: agent-setup README

## Goal

Create a comprehensive root `README.md` for the `agent-setup` repository that fully documents the VS Code GitHub Copilot configuration system. Developers who discover or adopt this repo should understand what it is, how to install it, what every component does, and how to invoke each piece — all within a single, top-to-bottom read. The current placeholder (`# agent-setup`) is replaced with production-quality, visually organized documentation.

## Architecture

The README follows a top-down reading journey: context → installation → component reference → workflow → invocation guide.

```
README.md
├── Title + tagline
├── ## What Is This?         (1-paragraph overview + three-layer model)
├── ## Quick Start           (5-step install + invoke guide)
├── ## File Structure        (annotated .github/ tree)
├── ## Agents                (table + per-agent descriptions)
├── ## Prompts               (4 categories, tables + descriptions for all 10 prompts)
├── ## Instructions          (table + description for all 4 instruction files)
├── ## Skills                (table + description for all 5 skills)
├── ## Dev Team Workflow     (6-phase pipeline diagram + agent assignment table)
└── ## How to Invoke         (quick-reference invocation cheat-sheet)
```

## Stack

- Language: Markdown only
- File: `README.md` (repo root)

## Dependencies

None. All content is sourced from `.github/` and summarized inline.

## Out of Scope

- Modifying any file in `.github/`
- Creating additional documentation files
- Detailed API documentation (the `.agent.md` / `.prompt.md` files serve that purpose)

---

## Task Details

### Task 1: Scaffold README — Title, Overview, Quick Start, File Structure

**Files**: `README.md`
**Depends on**: none

#### Description

Replace the current `README.md` (which contains only `# agent-setup`) with a scaffolded document containing four sections: (1) a title heading with tagline, (2) a "What Is This?" overview, (3) a numbered Quick Start, and (4) an annotated File Structure tree of the entire `.github/` directory.

#### Verification

- [ ] `README.md` exists and the old `# agent-setup` placeholder is replaced
- [ ] H1 title present: `agent-setup`
- [ ] Tagline blockquote present directly below title
- [ ] H2 `## What Is This?` section present, mentions three-layer model
- [ ] H2 `## Quick Start` section with numbered steps
- [ ] H2 `## File Structure` section with annotated tree showing all files
- [ ] No placeholder or TODO text

---

### Task 2: Agents and Prompts Sections

**Files**: `README.md`
**Depends on**: Task 1

#### Description

Append the Agents section (5 agents, summary table + H3 per agent) and Prompts section (10 prompts in 4 categories) to `README.md`.

#### Verification

- [ ] H2 `## Agents` section with 5-row summary table
- [ ] All 5 agents have H3 sub-sections with File, Model, Role, and Invoke fields
- [ ] H2 `## Prompts` section with 4 category sub-sections
- [ ] All 10 prompts listed with descriptions
- [ ] No existing sections modified or removed

---

### Task 3: Instructions, Skills, Dev Team Workflow, How to Invoke

**Files**: `README.md`
**Depends on**: Task 2

#### Description

Append the final four sections: Instructions (4-row table), Skills (5-row table), Dev Team Workflow (ASCII pipeline + agent table), and How to Invoke (cheat-sheet).

#### Verification

- [ ] H2 `## Instructions` section with 4-row table
- [ ] H2 `## Skills` section with 5-row table
- [ ] H2 `## Dev Team Workflow` section with ASCII pipeline and agent-assignment table
- [ ] H2 `## How to Invoke` section with usage examples
- [ ] Full document reads coherently top-to-bottom
- [ ] No placeholder text, no TODO items
