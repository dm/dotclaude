# Development Guidelines for Claude

## Core Philosophy

**TEST-DRIVEN DEVELOPMENT IS NON-NEGOTIABLE.** Every single line of production code must be written in response to a failing test. No exceptions.

**COMMIT AFTER EVERY RED-GREEN-REFACTOR CYCLE.** Use `/commit` for all commits — it handles staging, message format, and validation.

## Key Principles

- Write tests first (TDD) — RED-GREEN-REFACTOR strictly
- Test behavior, not implementation — through public API only
- No `any` types or type assertions — TypeScript strict mode always
- Immutable data only — no mutations
- Small, pure functions — composition over inheritance
- Use factory functions for test data (no `let`/`beforeEach`)
- Use real schemas/types in tests, never redefine them
- No comments — code should be self-documenting
- Prefer options objects over positional parameters

## Available Documentation

Detailed guidelines are available in `~/.claude/docs/`. Read the relevant file(s) when working on a task that would benefit from them:

**Practices:**
- `~/.claude/docs/testing.md` — Behavior-driven testing, factory patterns, achieving 100% coverage, React component testing
- `~/.claude/docs/workflow.md` — TDD process, quality gates, refactoring priority classification, DRY (knowledge vs code)
- `~/.claude/docs/working-with-claude.md` — Expectations, learning documentation framework, commit discipline

**Code:**
- `~/.claude/docs/typescript.md` — Strict mode, type vs interface, schema-first development with Zod, branded types
- `~/.claude/docs/code-style.md` — Functional programming patterns, immutability catalog, naming conventions, options objects
- `~/.claude/docs/examples.md` — Error handling (Result types), testing behavior, common anti-patterns

**Language-specific:**
- `~/.claude/docs/lang-python.md` — Python project conventions
- `~/.claude/docs/lang-laravel.md` — Laravel project conventions
