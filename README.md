# Copilot Research → Plan → Implement Workflow

A portable GitHub Copilot configuration that gives you a structured feature development workflow with sub-agents, feedback loops, and human checkpoints.

## Setup

Copy the `.github/` folder into the root of any repo. Copilot picks it up automatically.

```
your-repo/
└── .github/
    ├── copilot-instructions.md
    └── agents/
        ├── ticket-intake.md
        ├── researcher.md
        ├── planner.md
        ├── implementer.md
        ├── locator.md
        ├── analyzer.md
        └── pattern-finder.md
```

## How It Works

### The Four Agents

Use these in Copilot chat by typing `@agent-name`:

**`@ticket-intake`** — The entry point when you have a Jira ticket (or any ticket). Paste the description and acceptance criteria, and it parses the ticket, identifies which systems are involved, formulates targeted research questions, confirms its understanding with you, then hands off to `@researcher`. Passes the original ACs through so they flow to `@planner` later.

**`@researcher`** — Explores the codebase to answer questions from `@ticket-intake`, the user directly, or `@planner` (when gaps are found). Delegates to three hidden sub-agents (`@locator`, `@analyzer`, `@pattern-finder`) for parallel exploration. Saves findings to `docs/research/YYYY-MM-DD-description.md`. Hands off to `@planner` when done.

**`@planner`** — Creates a phased implementation plan from research and acceptance criteria. Checks for gaps (loops back to `@researcher` if needed), derives or refines acceptance criteria, checks in with you for alignment, then writes a detailed plan with test cases and success criteria per phase. Saves plans to `docs/plans/YYYY-MM-DD-description.md`. Hands off to `@implementer` when done.

**`@implementer`** — Executes an approved plan phase by phase. Writes tests first, then implementation, runs all automated verification, and **stops between every phase** for your confirmation before moving on.

### Sub-Agents (Hidden)

These are not visible in the agent picker. They're called by `@researcher` and `@planner` behind the scenes:

- **`@locator`** — Finds WHERE files live, categorized by type
- **`@analyzer`** — Explains HOW code works with data flow and file:line references
- **`@pattern-finder`** — Finds existing examples to use as templates

### The Flow

```
@ticket-intake → @researcher → @planner ⟲ @researcher (if gaps) → @implementer
      ↓               ↓             ↓                                    ↓
  ✋ confirm      writes doc    ✋ confirm ACs                    Phase 1 → ✋ confirm
  understanding                ✋ confirm phases                 Phase 2 → ✋ confirm
                                                                Phase 3 → ✋ confirm
                                                                        ...
```

1. `@ticket-intake` parses the ticket, identifies systems involved, formulates research questions, and confirms with you
2. `@researcher` explores the codebase and writes a research doc
3. `@planner` reads the research, checks for gaps (loops back to `@researcher` if needed), derives acceptance criteria, and writes a phased plan
4. `@implementer` executes the plan one phase at a time, pausing for your approval at each boundary

You can also skip `@ticket-intake` and start at any agent directly — use `@researcher` for ad-hoc codebase questions, `@planner` if you already know what to build, or `@implementer` if you already have a plan.

### Global Instructions

`copilot-instructions.md` applies to every Copilot interaction in the repo. It enforces:

- **Documentarian philosophy** — Describe what exists, don't critique
- **Planning first** — Research and plan before writing code
- **Human in the loop** — Checkpoints at every decision point
- **Behavioral tests** — Test what code does, not how it does it
- **Success criteria separation** — Automated checks vs. manual verification

## Usage Examples

**Start from a ticket (recommended):**
```
@ticket-intake

## Description
Add rate limiting to all public API endpoints to prevent abuse.
Limits should be configurable per-endpoint.

## Acceptance Criteria
- AC-1: Public endpoints return 429 when rate limit exceeded
- AC-2: Rate limits are configurable per endpoint via config file
- AC-3: Rate limit headers (X-RateLimit-Remaining, etc.) included in responses
```

**Research a feature area directly:**
```
@researcher How does the authentication flow work in this app?
```

**Plan when you already know the codebase:**
```
@planner Add rate limiting to the API endpoints. Research is at docs/research/2026-03-25-api-middleware.md
```

**Implement an existing plan:**
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
