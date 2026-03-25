---
description: "Stage and commit changes with semantic commit messages following project conventions"
argument-hint: "[message hint or context]"
---

# Semantic Commit

Create a focused, well-structured commit from the current changes.

Optional context from user: `$ARGUMENTS`

## Step 1: Assess Changes

Run these in parallel:
- `git status` (never use `-uall`)
- `git diff --cached --stat` and `git diff --stat` to see staged/unstaged changes
- `git log --oneline -5` to match the repo's commit style

## Step 2: Stage Files

**Stage specific files by name.** Never use `git add -A` or `git add .` — these risk including secrets (`.env`), large binaries, or unrelated changes.

Review what should be included:
- Group related changes into a single focused commit
- If changes span multiple unrelated concerns, ask the user which to include
- Never stage files that look like secrets (`.env`, credentials, tokens)

## Step 3: Validate (silently — do NOT ask the user)

Check these internally. If any is NO, **stop and explain why you can't commit**. Otherwise proceed directly to committing — do not ask for confirmation.

1. Is this a single, focused change (one task/feature/fix)?
2. Are tests and implementation committed together (not one without the other)?
3. Are all tests passing? (Only run the test suite if you have reason to doubt — e.g. you just made changes. Don't run tests on trivially safe commits like docs or config.)

For **refactoring commits**: no behavior changes, all tests still pass, committed separately from feature work.

## Step 4: Write the Commit Message

### Format

```
type(scope): concise description

- Bullet points for notable details (optional)
- Focus on WHY, not WHAT (the diff shows what)
```

**Subject line**: under 72 characters, lowercase, imperative mood.

### Types

| Type | When |
|------|------|
| `feat` | New feature or behavior change |
| `fix` | Bug fix |
| `refactor` | Code restructuring, no behavior change |
| `test` | Adding or updating tests only |
| `docs` | Documentation changes |
| `chore` | Config, dependencies, tooling |
| `style` | Formatting, whitespace (no logic change) |

### Scope (optional)

Use when it adds clarity: `feat(payments):`, `fix(auth):`, `refactor(api):`. Skip for small repos or obvious context.

### TDD Labels (optional)

When following RED-GREEN-REFACTOR, label commits to make the cycle visible in git history:

```
test: add test for payment validation (RED)
feat: implement payment validation (GREEN)
refactor: extract payment validation constants
```

## Step 5: Commit

Always use HEREDOC for the commit message to preserve formatting:

```bash
git commit -m "$(cat <<'EOF'
type(scope): description

- Detail if needed
EOF
)"
```

After committing, run `git status` to confirm success.

## Rules

- **One concern per commit.** Don't mix a feature with an unrelated fix.
- **Tests travel with implementation.** A `feat` or `fix` commit must include its tests. Exception: pure `test` commits adding coverage to existing code.
- **Refactoring is separate.** Never combine refactoring with feature/fix work in the same commit.
- **WIP files update AFTER code commits.** Never commit plan/WIP progress without the actual work committed first.
- **Never skip hooks.** No `--no-verify`. If a hook fails, fix the underlying issue.
- **Never amend unless asked.** Always create new commits. Amending can destroy previous work, especially after hook failures.
- **Invoking `/commit` IS the request.** Don't ask for confirmation — assess, stage, write the message, and commit immediately. Only stop if validation fails.
