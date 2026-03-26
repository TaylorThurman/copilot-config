# Copilot Research → Plan → Implement Workflow

A portable GitHub Copilot configuration that gives you a structured feature development workflow with sub-agents, feedback loops, and human checkpoints.

## Setup

Copy the `.github/` folder into the root of any repo. Copilot picks it up automatically.

```
your-repo/
└── .github/
    ├── copilot-instructions.md
    └── agents/
        ├── researcher.md
        ├── planner.md
        ├── implementer.md
        ├── locator.md
        ├── analyzer.md
        └── pattern-finder.md
```

## How It Works

### The Three Agents

Use these in Copilot chat by typing `@agent-name`:

**`@researcher`** — Explores the codebase to answer a question or understand a feature area. Delegates to three hidden sub-agents (`@locator`, `@analyzer`, `@pattern-finder`) for parallel exploration. Saves findings to `docs/research/YYYY-MM-DD-description.md`. Hands off to `@planner` when done.

**`@planner`** — Creates a phased implementation plan from research or requirements. Derives acceptance criteria, checks in with you for alignment, then writes a detailed plan with test cases and success criteria per phase. If it finds gaps in the research, it hands back to `@researcher` to fill them before continuing. Saves plans to `docs/plans/YYYY-MM-DD-description.md`. Hands off to `@implementer` when done.

**`@implementer`** — Executes an approved plan phase by phase. Writes tests first, then implementation, runs all automated verification, and **stops between every phase** for your confirmation before moving on.

### Sub-Agents (Hidden)

These are not visible in the agent picker. They're called by `@researcher` and `@planner` behind the scenes:

- **`@locator`** — Finds WHERE files live, categorized by type
- **`@analyzer`** — Explains HOW code works with data flow and file:line references
- **`@pattern-finder`** — Finds existing examples to use as templates

### The Flow

```
@researcher → @planner ⟲ @researcher (if gaps) → @implementer
                                                      ↓
                                              Phase 1 → ✋ confirm
                                              Phase 2 → ✋ confirm
                                              Phase 3 → ✋ confirm
                                                      ...
```

1. `@researcher` explores the codebase and writes a research doc
2. `@planner` reads the research, checks for gaps (loops back to `@researcher` if needed), derives acceptance criteria, and writes a phased plan
3. `@implementer` executes the plan one phase at a time, pausing for your approval at each boundary

### Global Instructions

`copilot-instructions.md` applies to every Copilot interaction in the repo. It enforces:

- **Documentarian philosophy** — Describe what exists, don't critique
- **Planning first** — Research and plan before writing code
- **Human in the loop** — Checkpoints at every decision point
- **Behavioral tests** — Test what code does, not how it does it
- **Success criteria separation** — Automated checks vs. manual verification

## Usage Examples

**Research a feature area:**
```
@researcher How does the authentication flow work in this app?
```

**Plan from research:**
```
@planner Create an implementation plan based on docs/research/2026-03-25-auth-flow.md
```

**Plan from scratch:**
```
@planner Add rate limiting to the API endpoints
```

**Implement a plan:**
```
@implementer Execute docs/plans/2026-03-25-rate-limiting.md
```

## File Conventions

| Type | Location |
|------|----------|
| Research docs | `docs/research/YYYY-MM-DD-description.md` |
| Implementation plans | `docs/plans/YYYY-MM-DD-description.md` |

## Ported From

This configuration is a port of a Claude Code slash command workflow (`/compozed:feature:research`, `/compozed:feature:plan`, `/compozed:feature:implement`) adapted to use Copilot's custom agents, sub-agents, and handoff features.
