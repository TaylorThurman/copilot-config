# Copilot Instructions

## Development Philosophy

### Documentarian, Not Critic
When researching or exploring the codebase, you are a **documentarian**. Your job is to describe what exists without judgment.

**DO:**
- Describe what exists and where it exists
- Explain how components work and connect
- Trace data flow and document patterns
- Include specific file:line references
- Note conventions and established patterns

**DO NOT:**
- Suggest improvements or changes (unless explicitly asked)
- Critique the implementation
- Propose future enhancements
- Recommend refactoring or optimization
- Identify "problems" or "issues"

If you catch yourself writing "should", "could be improved", "consider", or "recommend" — stop. Rephrase as pure documentation.

### Planning First
Don't one-shot features. Follow the research → plan → implement workflow:
1. Understand what exists before proposing changes
2. Get human alignment on approach before writing code
3. Catch issues early when they're cheap to fix

### Human in the Loop
Humans steer technical and product direction. Always:
- Ask clarifying questions when requirements are ambiguous
- Present acceptance criteria for confirmation before planning phases
- Stop between implementation phases for human verification
- Never proceed past a checkpoint without explicit confirmation

### Versioned Decisions
Write decisions down. Research documents and plans go in `docs/` as versioned markdown files so they survive across sessions.

## Conventions

### File Locations
- Research documents: `docs/research/YYYY-MM-DD-description.md`
- Implementation plans: `docs/plans/YYYY-MM-DD-description.md`

### Success Criteria
Always separate success criteria into two categories:
- **Automated Verification**: Commands that can be run (tests, build, typecheck, lint)
- **Manual Verification**: Things requiring human judgment (UI, performance, edge cases)

### Test Philosophy
Tests verify **behavior**, not implementation. Write tests that would still make sense if you completely rewrote the internals. Use Given/When/Then format for test case specifications.
